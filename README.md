commit f241bd4b4d7c7fd14a9380c1a913d24b4d75af67
Author: iceman1001 <iceman@iuse.se>
Date:   Sun May 15 11:37:06 2022 +0200

    added  MAD key Sector0 detection and `hf mf mad` now takes a `-f` param for offline decoding of dump files

diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index d7ef53eb8..f714412b7 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -262,6 +262,11 @@ static void mf_print_blocks(uint16_t n, uint8_t *d, bool verbose) {
     }
     PrintAndLogEx(INFO, "----+-------------------------------------------------+-----------------");
     PrintAndLogEx(HINT, _CYAN_("cyan") " = value block with decoded value");
+
+    // MAD detection
+    if (memcmp(d + (3 * MFBLOCK_SIZE), g_mifare_mad_key, 6) == 0) {
+        PrintAndLogEx(HINT, "MAD key detected. Try " _YELLOW_("`hf mf mad`") " for more details");
+    }
     PrintAndLogEx(NORMAL, "");
 }
 
@@ -3722,6 +3727,11 @@ void printKeyTableEx(uint8_t sectorscnt, sector_t *e_sector, uint8_t start_secto
     } else {
         PrintAndLogEx(SUCCESS, "( " _YELLOW_("0") ":Failed / " _YELLOW_("1") ":Success )");
     }
+
+    // MAD detection
+    if (e_sector[MF_MAD1_SECTOR].foundKey[0] && e_sector[MF_MAD1_SECTOR].Key[MF_KEY_A] == 0xA0A1A2A3A4A5) {
+        PrintAndLogEx(HINT, "MAD key detected. Try " _YELLOW_("`hf mf mad`") " for more details");
+    }
     PrintAndLogEx(NORMAL, "");
 }
 
@@ -5395,6 +5405,7 @@ static int CmdHF14AMfMAD(const char *Cmd) {
         arg_lit0("b",  "keyb",     "use key B for access printing sectors (by default: key A)"),
         arg_lit0(NULL, "be",       "(optional, BigEndian)"),
         arg_lit0(NULL, "dch",      "decode Card Holder information"),
+        arg_str0("f", "file", "<fn>", "load dump file and decode MAD"),        
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -5409,8 +5420,77 @@ static int CmdHF14AMfMAD(const char *Cmd) {
     bool swapmad = arg_get_lit(ctx, 5);
     bool decodeholder = arg_get_lit(ctx, 6);
 
+    int fnlen = 0;
+    char filename[FILE_PATH_SIZE] = {0};
+    CLIParamStrToBuf(arg_get_str(ctx, 7), (uint8_t *)filename, FILE_PATH_SIZE, &fnlen);
     CLIParserFree(ctx);
 
+    if (fnlen > 0) {
+
+        // reserve memory
+        uint8_t *dump = NULL;
+        size_t bytes_read = 0;
+        int res = 0;
+        DumpFileType_t dftype = getfiletype(filename);
+        switch (dftype) {
+            case BIN: {
+                res = loadFile_safe(filename, ".bin", (void **)&dump, &bytes_read);
+                break;
+            }
+            case EML: {
+                res = loadFileEML_safe(filename, (void **)&dump, &bytes_read);
+                break;
+            }
+            case JSON: {
+                dump = calloc(MFBLOCK_SIZE * MIFARE_4K_MAXBLOCK, sizeof(uint8_t));
+                if (dump == NULL) {
+                    PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
+                    return PM3_EMALLOC;
+                }
+                res = loadFileJSON(filename, (void *)dump, MIFARE_4K_MAXBLOCK * MFBLOCK_SIZE, &bytes_read, NULL);
+                break;
+            }
+            case DICTIONARY: {
+                PrintAndLogEx(ERR, "Error: Only BIN/JSON/EML formats allowed");
+                free(dump);
+                return PM3_EINVARG;
+            }
+        }
+
+        if (res != PM3_SUCCESS) {
+            PrintAndLogEx(FAILED, "File: " _YELLOW_("%s") ": not found or locked.", filename);
+            free(dump);
+            return PM3_EFILE;
+        }
+
+        uint16_t block_cnt = MIN(MIFARE_1K_MAXBLOCK, (bytes_read / MFBLOCK_SIZE));
+        if (bytes_read == 320)
+            block_cnt = MIFARE_MINI_MAXBLOCK;
+        else if (bytes_read == 2048)
+            block_cnt = MIFARE_2K_MAXBLOCK;
+        else if (bytes_read == 4096)
+            block_cnt = MIFARE_4K_MAXBLOCK;
+
+        if (verbose) {
+            PrintAndLogEx(INFO, "File: " _YELLOW_("%s"), filename);
+            PrintAndLogEx(INFO, "File size %zu bytes, file blocks %d (0x%x)", bytes_read, block_cnt, block_cnt);
+        }
+
+        // MAD detection
+        if (memcmp(dump + (3 * MFBLOCK_SIZE), g_mifare_mad_key, 6) != 0) {
+            PrintAndLogEx(FAILED, "No MAD key was detected in the dump file");
+            free(dump);
+            return PM3_ESOFT;
+        }
+
+        MADPrintHeader();
+        bool haveMAD2 = false;
+        MAD1DecodeAndPrint(dump, swapmad, verbose, &haveMAD2);
+        free(dump);
+        return PM3_SUCCESS;
+    }
+
+
     uint8_t sector0[16 * 4] = {0};
     uint8_t sector10[16 * 4] = {0};
 
@@ -5438,9 +5518,7 @@ static int CmdHF14AMfMAD(const char *Cmd) {
         return PM3_ESOFT;
     }
 
-    PrintAndLogEx(NORMAL, "");
-    PrintAndLogEx(INFO, "--- " _CYAN_("MIFARE App Directory Information") " ----------------");
-    PrintAndLogEx(INFO, "-----------------------------------------------------");
+    MADPrintHeader();
 
     bool haveMAD2 = false;
     MAD1DecodeAndPrint(sector0, swapmad, verbose, &haveMAD2);
diff --git a/client/src/mifare/mad.c b/client/src/mifare/mad.c
index 256a9bf90..4515427ac 100644
--- a/client/src/mifare/mad.c
+++ b/client/src/mifare/mad.c
@@ -297,6 +297,12 @@ static int MADInfoByteDecode(const uint8_t *sector, bool swapmad, int mad_ver, b
     return info;
 }
 
+void MADPrintHeader(void) {
+    PrintAndLogEx(NORMAL, "");
+    PrintAndLogEx(INFO, "--- " _CYAN_("MIFARE App Directory Information") " ----------------");
+    PrintAndLogEx(INFO, "-----------------------------------------------------");
+}
+
 int MAD1DecodeAndPrint(uint8_t *sector, bool swapmad, bool verbose, bool *haveMAD2) {
     open_mad_file(&mad_known_aids, verbose);
 
diff --git a/client/src/mifare/mad.h b/client/src/mifare/mad.h
index d56de9530..599a3532e 100644
--- a/client/src/mifare/mad.h
+++ b/client/src/mifare/mad.h
@@ -27,5 +27,6 @@ int MAD1DecodeAndPrint(uint8_t *sector, bool swapmad, bool verbose, bool *haveMA
 int MAD2DecodeAndPrint(uint8_t *sector, bool swapmad, bool verbose);
 int MADDFDecodeAndPrint(uint32_t short_aid);
 int MADCardHolderInfoDecode(uint8_t *data, size_t datalen, bool verbose);
+void MADPrintHeader(void);
 
 #endif // _MAD_H_
