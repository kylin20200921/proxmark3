commit 8a54e103ccc22da43da0f59251802a4ad3b4ab61
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Mar 12 18:57:56 2022 +0100

    added a new command

diff --git a/client/src/cmddata.c b/client/src/cmddata.c
index 40ea4dc96..0cf28dd4a 100644
--- a/client/src/cmddata.c
+++ b/client/src/cmddata.c
@@ -34,6 +34,7 @@
 #include "cliparser.h"
 #include "cmdlft55xx.h"          // print...
 #include "crypto/asn1utils.h"    // ASN1 decode / print
+#include "cmdflashmemspiffs.h"   // SPIFFS flash memory download
 
 uint8_t g_DemodBuffer[MAX_DEMOD_BUF_LEN];
 size_t g_DemodBufferLen = 0;
@@ -2881,6 +2882,275 @@ static int CmdAsn1Decoder(const char *Cmd) {
     return PM3_SUCCESS;
 }
 
+
+
+static int CmdDiff(const char *Cmd) {
+
+    CLIParserContext *ctx;
+    CLIParserInit(&ctx, "data diff",
+                    "Diff takes a multitude of input data and makes a binary compare.\n"
+                    "It accepts filenames (filesystem or RDV4 flashmem SPIFFS), emulator memory, magic gen1",
+                    "data diff -w 4 -a hf-mfu-01020304.bin -b hf-mfu-04030201.bin\n"
+                    "data diff -a fileA -b fileB\n"
+                    "data diff -a fileA --eb\n"
+                    "data diff -a fileA --cb\n"
+                    "data diff --fa fileA -b fileB\n"
+                    "data diff --fa fileA --fb fileB\n"
+                    "data diff --ea --cb\n"
+                );
+
+    void *argtable[] = {
+        arg_param_begin,
+        arg_str0("a",  NULL, "<fn>", "input file name A"),
+        arg_str0("b",  NULL, "<fn>", "input file name B"),
+        arg_lit0(NULL, "cb", "magic gen1 <hf mf csave>"),
+        arg_lit0(NULL, "eb", "emulator memory <hf mf esave>"),
+        arg_str0(NULL, "fa", "<fn>", "input spiffs file A"),
+        arg_str0(NULL, "fb", "<fn>", "input spiffs file B"),
+        arg_int0("w",  NULL, "<4|8|16>", "Width of data output"),
+        arg_param_end
+    };
+    CLIExecWithReturn(ctx, Cmd, argtable, false);
+
+    int fnlenA = 0;
+    char filenameA[FILE_PATH_SIZE] = {0};
+    CLIParamStrToBuf(arg_get_str(ctx, 1), (uint8_t *)filenameA, FILE_PATH_SIZE, &fnlenA);
+
+    int fnlenB = 0;
+    char filenameB[FILE_PATH_SIZE] = {0};
+    CLIParamStrToBuf(arg_get_str(ctx, 2), (uint8_t *)filenameB, FILE_PATH_SIZE, &fnlenB);
+
+    bool use_c = arg_get_lit(ctx, 3);
+    bool use_e = arg_get_lit(ctx, 4);
+
+    // SPIFFS filename A
+    int splenA = 0;
+    char spnameA[FILE_PATH_SIZE] = {0};
+    CLIParamStrToBuf(arg_get_str(ctx, 5), (uint8_t *)spnameA, FILE_PATH_SIZE, &splenA);
+
+    // SPIFFS filename B
+    int splenB = 0;
+    char spnameB[FILE_PATH_SIZE] = {0};
+    CLIParamStrToBuf(arg_get_str(ctx, 6), (uint8_t *)spnameB, FILE_PATH_SIZE, &splenB);
+
+    int width = arg_get_int_def(ctx, 7, 16) & 0x3F;
+    CLIParserFree(ctx);
+
+    // sanity check
+    if (IfPm3Rdv4Fw() == false && (splenA > 0 || splenB > 0) ) {
+        PrintAndLogEx(WARNING, "No RDV4 Flashmemory available");
+        return PM3_EINVARG;
+    }
+
+    if (splenA > 32) {
+        PrintAndLogEx(WARNING, "SPIFFS filname A length is large than 32 bytes, got %zu", splenA);
+        return PM3_EINVARG;
+    }
+    if (splenB > 32) {
+        PrintAndLogEx(WARNING, "SPIFFS filname B length is large than 32 bytes, got %zu", splenB);
+        return PM3_EINVARG;
+    }
+
+    //
+    if (width > 16 || width < 1 ) {
+        PrintAndLogEx(INFO, "Width out of range, using default 16 bytes width");
+        width = 16;
+    }
+
+    int res = PM3_SUCCESS;
+    uint8_t *inA, *inB;
+    size_t datalenA = 0, datalenB = 0;
+    // read file A
+    if (fnlenA) {
+
+        // if user supplied dump file,  time to load it
+        DumpFileType_t dftype = getfiletype(filenameA);
+        switch (dftype) {
+            case BIN: {
+                res = loadFile_safe(filenameA, ".bin", (void **)&inA, &datalenA);
+                break;
+            }
+            case EML: {
+                res = loadFileEML_safe(filenameA, (void **)&inA, &datalenA);
+                break;
+            }
+            case JSON: {
+                inA =  calloc(2048, sizeof(uint8_t));
+                if (inA == NULL) {
+                    PrintAndLogEx(ERR, "error, cannot allocate memory ");
+                    return PM3_EMALLOC;
+                }
+                res = loadFileJSON(filenameA, (void *)inA, 2048, &datalenA, NULL);
+                break;
+            }
+            case DICTIONARY: {
+                free(inA);
+                PrintAndLogEx(ERR, "Error: Only BIN/JSON/EML formats allowed");
+                return PM3_EINVARG;
+            }
+        }
+
+        if (res != PM3_SUCCESS) {
+            free(inA);
+            return PM3_EFILE;
+        }
+    }
+
+    // read file B
+    if (fnlenB) {
+        // if user supplied dump file,  time to load it
+        DumpFileType_t dftype = getfiletype(filenameB);
+        switch (dftype) {
+            case BIN: {
+                res = loadFile_safe(filenameB, ".bin", (void **)&inB, &datalenB);
+                break;
+            }
+            case EML: {
+                res = loadFileEML_safe(filenameB, (void **)&inB, &datalenB);
+                break;
+            }
+            case JSON: {
+                inB = calloc(2048, sizeof(uint8_t));
+                if (inB == NULL) {
+                    PrintAndLogEx(ERR, "error, cannot allocate memory ");
+                    return PM3_EMALLOC;
+                }
+                res = loadFileJSON(filenameB, (void *)inB, 2048, &datalenB, NULL);
+                break;
+            }
+            case DICTIONARY: {
+                free(inB);
+                PrintAndLogEx(ERR, "Error: Only BIN/JSON/EML formats allowed");
+                return PM3_EINVARG;
+            }
+        }
+
+        if (res != PM3_SUCCESS) {
+            free(inB);
+            return PM3_EFILE;
+        }
+    }
+
+    // read spiffs file A
+    if (splenA) {
+        res = flashmem_spiffs_download(spnameA, splenA, (void **)&inA, &datalenA);
+        if (res != PM3_SUCCESS) {
+            return res;
+        }
+    }
+
+    // read spiffs file B
+    if (splenB) {
+        res = flashmem_spiffs_download(spnameB, splenB, (void **)&inB, &datalenB);
+        if (res != PM3_SUCCESS) {
+            return res;
+        }
+    }
+
+    // download emulator memory
+    if (use_e) {
+
+        uint8_t *d = calloc(4096, sizeof(uint8_t));
+        if (d == NULL) {
+            PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
+            return PM3_EMALLOC;
+        }
+
+        PrintAndLogEx(INFO, "downloading from emulator memory");
+        if (GetFromDevice(BIG_BUF_EML, d, 4096, 0, NULL, 0, NULL, 2500, false) == false) {
+            PrintAndLogEx(WARNING, "Fail, transfer from device time-out");
+            free(inA);
+            free(inB);
+            return PM3_ETIMEOUT;
+        }
+
+        if (fnlenA) {
+            datalenB = 4096;
+            inB = d;
+        } else {
+            datalenA = 4096;
+            inA = d;
+        }
+    }
+
+    // dump magic card memory
+    if (use_c) {
+
+    }
+
+    size_t n = (datalenA > datalenB) ? datalenB : datalenA;
+    PrintAndLogEx(INFO, "data len:  %zu   A %zu  B %zu", n, datalenA, datalenB);
+
+    if (inA == NULL)
+        PrintAndLogEx(INFO, "inA null");
+
+    if (inB == NULL)
+        PrintAndLogEx(INFO, "inB null");
+
+    int hdr_sln = (width * 3) + 1;
+    PrintAndLogEx(INFO, "");
+
+    char hdr0[200] = " #  | " _CYAN_("A");
+    memset(hdr0 + strlen(hdr0), ' ', hdr_sln - 2);
+    strcat(hdr0 + strlen(hdr0), "| " _CYAN_("B"));
+    PrintAndLogEx(INFO, hdr0);
+
+    char hdr1[200] = "----+";
+    memset(hdr1 + strlen(hdr1), '-', hdr_sln);
+    memset(hdr1 + strlen(hdr1), '+', 1);
+    memset(hdr1 + strlen(hdr1), '-', hdr_sln);
+    PrintAndLogEx(INFO, hdr1);
+
+    // index 4bytes, spaces, bar, bytes with ansi codes
+    // (16 * 2 * 8 ) + 32 
+    char line[500] = {0};
+
+    // print data diff loop
+    for (int i = 0; i < n;  i += width ) {
+
+        memset(line, 0, sizeof(line));
+
+        int diff = memcmp(inA + i, inB + i, width);        
+        // if ok,  just print
+        if (diff == 0) {
+            hex_to_buffer((uint8_t*)line, inA + i, width, width, 0, 1, true);
+            strcat(line + strlen(line), "| ");
+            hex_to_buffer((uint8_t*)(line + strlen(line)), inB + i, width, width, 0, 1, true);
+        } else {
+
+            char dlnA[240] = {0};
+            char dlnB[240] = {0};
+            memset(dlnA, 0, sizeof(dlnA));
+            memset(dlnB, 0, sizeof(dlnB));
+            // if diff,  time to find it
+            for (int j = i; j < (i + width); j++) {
+
+                if (inA[j] != inB[j]) {
+                    //PrintAndLogEx(INFO, "%02X -- %02X", inA[j], inB[j]);
+                    // diff
+                    sprintf(dlnA + strlen(dlnA), _GREEN_("%02X "), inA[j]);
+                    sprintf(dlnB + strlen(dlnB), _RED_("%02X "), inB[j]);
+                } else {
+                    //PrintAndLogEx(INFO, "%02X ++ %02X", inA[j], inB[j]);
+                    // normal
+                    sprintf(dlnA + strlen(dlnA), "%02X ", inA[j]);
+                    sprintf(dlnB + strlen(dlnB), "%02X ", inB[j]);
+                }
+            }
+            sprintf(line, "%s| %s", dlnA, dlnB);
+        }
+        PrintAndLogEx(INFO, "%03X | %s", i, line);
+    }
+
+    PrintAndLogEx(INFO, hdr1);
+    PrintAndLogEx(NORMAL, "");
+
+    free(inB);
+    free(inA);
+    return PM3_SUCCESS;
+}
+
+
 static command_t CommandTable[] = {
     {"help",            CmdHelp,                 AlwaysAvailable,  "This help"},
 
@@ -2915,10 +3185,11 @@ static command_t CommandTable[] = {
     {"getbitstream",    CmdGetBitStream,         AlwaysAvailable,  "Convert GraphBuffer's >=1 values to 1 and <1 to 0"},
 
     {"-----------",     CmdHelp,                 AlwaysAvailable, "------------------------- " _CYAN_("General") "-------------------------"},
-    {"asn1",            CmdAsn1Decoder,         AlwaysAvailable,  "asn1 decoder"},
+    {"asn1",            CmdAsn1Decoder,          AlwaysAvailable,  "asn1 decoder"},
     {"bin2hex",         Cmdbin2hex,              AlwaysAvailable,  "Converts binary to hexadecimal"},
     {"bitsamples",      CmdBitsamples,           IfPm3Present,     "Get raw samples as bitstring"},
     {"clear",           CmdBuffClear,            AlwaysAvailable,  "Clears bigbuf on deviceside and graph window"},
+    {"diff",            CmdDiff,                 AlwaysAvailable,  "diff of input files"},
     {"hexsamples",      CmdHexsamples,           IfPm3Present,     "Dump big buffer as hex bytes"},
     {"hex2bin",         Cmdhex2bin,              AlwaysAvailable,  "Converts hexadecimal to binary"},
     {"load",            CmdLoad,                 AlwaysAvailable,  "Load contents of file into graph window"},
