commit d4c3082741a7a40daa35097a21d8f006e50e44fb
Author: iceman1001 <iceman@iuse.se>
Date:   Sun May 15 23:06:46 2022 +0200

    refactoring loading dump files

diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index f714412b7..71ec686ae 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -36,8 +36,6 @@
 #include "cmdhf14a.h"           // exchange APDU
 #include "crypto/libpcrypto.h"
 
-#define MFBLOCK_SIZE 16
-
 #define MIFARE_4K_MAXBLOCK 256
 #define MIFARE_2K_MAXBLOCK 128
 #define MIFARE_1K_MAXBLOCK 64
@@ -264,7 +262,7 @@ static void mf_print_blocks(uint16_t n, uint8_t *d, bool verbose) {
     PrintAndLogEx(HINT, _CYAN_("cyan") " = value block with decoded value");
 
     // MAD detection
-    if (memcmp(d + (3 * MFBLOCK_SIZE), g_mifare_mad_key, 6) == 0) {
+    if (HasMADKey(d)) {
         PrintAndLogEx(HINT, "MAD key detected. Try " _YELLOW_("`hf mf mad`") " for more details");
     }
     PrintAndLogEx(NORMAL, "");
@@ -1110,38 +1108,11 @@ static int CmdHF14AMfRestore(const char *Cmd) {
     }
 
     // read dump file
-    bytes_read = 0;
     uint8_t *dump = NULL;
-    int res = 0;
-    DumpFileType_t dftype = getfiletype(datafilename);
-    switch (dftype) {
-        case BIN: {
-            res = loadFile_safe(datafilename, ".bin", (void **)&dump, &bytes_read);
-            break;
-        }
-        case EML: {
-            res = loadFileEML_safe(datafilename, (void **)&dump, &bytes_read);
-            break;
-        }
-        case JSON: {
-            dump = calloc(MFBLOCK_SIZE * MIFARE_4K_MAXBLOCK, sizeof(uint8_t));
-            if (dump == NULL) {
-                PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
-                return PM3_EMALLOC;
-            }
-            res = loadFileJSON(datafilename, (void *)dump, MIFARE_4K_MAXBLOCK * MFBLOCK_SIZE, &bytes_read, NULL);
-            break;
-        }
-        case DICTIONARY: {
-            PrintAndLogEx(ERR, "Error: Only BIN/JSON/EML formats allowed");
-            free(dump);
-            return PM3_EINVARG;
-        }
-    }
-
+    bytes_read = 0;
+    int res = pm3_load_dump(datafilename, (void **)&dump, &bytes_read, (MFBLOCK_SIZE * MIFARE_4K_MAXBLOCK));
     if (res != PM3_SUCCESS) {
-        free(dump);
-        return PM3_EFILE;
+        return res;
     }
 
     // default authentication key
@@ -3944,41 +3915,14 @@ int CmdHF14AMfELoad(const char *Cmd) {
     }
 
     uint8_t *data = NULL;
-    size_t datalen = 0;
-    int res = PM3_SUCCESS;
-    DumpFileType_t dftype = getfiletype(filename);
-    switch (dftype) {
-        case BIN: {
-            res = loadFile_safe(filename, ".bin", (void **)&data, &datalen);
-            break;
-        }
-        case EML: {
-            res = loadFileEML_safe(filename, (void **)&data, &datalen);
-            break;
-        }
-        case JSON: {
-            data = calloc(MFBLOCK_SIZE * MIFARE_4K_MAXBLOCK, sizeof(uint8_t));
-            if (data == NULL) {
-                PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
-                return PM3_EMALLOC;
-            }
-            res = loadFileJSON(filename, (void *)data, MIFARE_4K_MAXBLOCK * MFBLOCK_SIZE, &datalen, NULL);
-            break;
-        }
-        case DICTIONARY: {
-            PrintAndLogEx(ERR, "Error: Only BIN/JSON/EML formats allowed");
-            free(data);
-            return PM3_EINVARG;
-        }
-    }
-
+    size_t bytes_read = 0;
+    int res = pm3_load_dump(filename, (void **)&data, &bytes_read, (MFBLOCK_SIZE * MIFARE_4K_MAXBLOCK));
     if (res != PM3_SUCCESS) {
-        free(data);
-        return PM3_EFILE;
+        return res;
     }
 
     // 64 or 256 blocks.
-    if ((datalen % block_width) != 0) {
+    if ((bytes_read % block_width) != 0) {
         PrintAndLogEx(FAILED, "File content error. Size doesn't match blockwidth ");
         free(data);
         return PM3_ESOFT;
@@ -3986,7 +3930,7 @@ int CmdHF14AMfELoad(const char *Cmd) {
 
     // convert plain or old mfu format to new format
     if (block_width == MFU_BLOCK_SIZE) {
-        res = convert_mfu_dump_format(&data, &datalen, true);
+        res = convert_mfu_dump_format(&data, &bytes_read, true);
         if (res != PM3_SUCCESS) {
             PrintAndLogEx(FAILED, "Failed convert on load to new Ultralight/NTAG format");
             free(data);
@@ -3997,7 +3941,7 @@ int CmdHF14AMfELoad(const char *Cmd) {
         printMFUdumpEx(mfu_dump, mfu_dump->pages + 1, 0);
 
         // update expected blocks to match converted data.
-        block_cnt = datalen / MFU_BLOCK_SIZE;
+        block_cnt = bytes_read / MFU_BLOCK_SIZE;
         PrintAndLogEx(INFO, "MIFARE Ultralight override, will use %d blocks ( %u bytes )", block_cnt, block_cnt * block_width);
     }
 
@@ -4010,8 +3954,8 @@ int CmdHF14AMfELoad(const char *Cmd) {
     size_t offset = 0;
     int cnt = 0;
 
-    while (datalen && cnt < block_cnt) {
-        if (datalen == block_width) {
+    while (bytes_read && cnt < block_cnt) {
+        if (bytes_read == block_width) {
             // Disable fast mode on last packet
             g_conn.block_after_ACK = false;
         }
@@ -4026,7 +3970,7 @@ int CmdHF14AMfELoad(const char *Cmd) {
 
         cnt++;
         offset += block_width;
-        datalen -= block_width;
+        bytes_read -= block_width;
     }
     free(data);
     PrintAndLogEx(NORMAL, "");
@@ -4642,38 +4586,12 @@ static int CmdHF14AMfCLoad(const char *Cmd) {
         return PM3_SUCCESS;
     }
 
+    // reserve memory
     uint8_t *data = NULL;
     size_t bytes_read = 0;
-    int res = 0;
-    DumpFileType_t dftype = getfiletype(filename);
-    switch (dftype) {
-        case BIN: {
-            res = loadFile_safe(filename, ".bin", (void **)&data, &bytes_read);
-            break;
-        }
-        case EML: {
-            res = loadFileEML_safe(filename, (void **)&data, &bytes_read);
-            break;
-        }
-        case JSON: {
-            data = calloc(MFBLOCK_SIZE * MIFARE_4K_MAXBLOCK, sizeof(uint8_t));
-            if (data == NULL) {
-                PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
-                return PM3_EMALLOC;
-            }
-            res = loadFileJSON(filename, (void *)data, MIFARE_4K_MAXBLOCK * MFBLOCK_SIZE, &bytes_read, NULL);
-            break;
-        }
-        case DICTIONARY: {
-            PrintAndLogEx(ERR, "Error: Only BIN/JSON/EML formats allowed");
-            free(data);
-            return PM3_EINVARG;
-        }
-    }
-
+    int res = pm3_load_dump(filename, (void **)&data, &bytes_read, (MFBLOCK_SIZE * MIFARE_4K_MAXBLOCK));
     if (res != PM3_SUCCESS) {
-        free(data);
-        return PM3_EFILE;
+        return res;
     }
 
     // 64 or 256blocks.
@@ -5430,37 +5348,9 @@ static int CmdHF14AMfMAD(const char *Cmd) {
         // reserve memory
         uint8_t *dump = NULL;
         size_t bytes_read = 0;
-        int res = 0;
-        DumpFileType_t dftype = getfiletype(filename);
-        switch (dftype) {
-            case BIN: {
-                res = loadFile_safe(filename, ".bin", (void **)&dump, &bytes_read);
-                break;
-            }
-            case EML: {
-                res = loadFileEML_safe(filename, (void **)&dump, &bytes_read);
-                break;
-            }
-            case JSON: {
-                dump = calloc(MFBLOCK_SIZE * MIFARE_4K_MAXBLOCK, sizeof(uint8_t));
-                if (dump == NULL) {
-                    PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
-                    return PM3_EMALLOC;
-                }
-                res = loadFileJSON(filename, (void *)dump, MIFARE_4K_MAXBLOCK * MFBLOCK_SIZE, &bytes_read, NULL);
-                break;
-            }
-            case DICTIONARY: {
-                PrintAndLogEx(ERR, "Error: Only BIN/JSON/EML formats allowed");
-                free(dump);
-                return PM3_EINVARG;
-            }
-        }
-
+        int res = pm3_load_dump(filename, (void **)&dump, &bytes_read, (MFBLOCK_SIZE * MIFARE_4K_MAXBLOCK));
         if (res != PM3_SUCCESS) {
-            PrintAndLogEx(FAILED, "File: " _YELLOW_("%s") ": not found or locked.", filename);
-            free(dump);
-            return PM3_EFILE;
+            return res;
         }
 
         uint16_t block_cnt = MIN(MIFARE_1K_MAXBLOCK, (bytes_read / MFBLOCK_SIZE));
@@ -5477,7 +5367,7 @@ static int CmdHF14AMfMAD(const char *Cmd) {
         }
 
         // MAD detection
-        if (memcmp(dump + (3 * MFBLOCK_SIZE), g_mifare_mad_key, 6) != 0) {
+        if (HasMADKey(dump)) {
             PrintAndLogEx(FAILED, "No MAD key was detected in the dump file");
             free(dump);
             return PM3_ESOFT;
@@ -6249,40 +6139,12 @@ static int CmdHF14AMfView(const char *Cmd) {
     bool verbose = arg_get_lit(ctx, 2);
     CLIParserFree(ctx);
 
-    // reserve memory
+    // read dump file
     uint8_t *dump = NULL;
     size_t bytes_read = 0;
-    int res = 0;
-    DumpFileType_t dftype = getfiletype(filename);
-    switch (dftype) {
-        case BIN: {
-            res = loadFile_safe(filename, ".bin", (void **)&dump, &bytes_read);
-            break;
-        }
-        case EML: {
-            res = loadFileEML_safe(filename, (void **)&dump, &bytes_read);
-            break;
-        }
-        case JSON: {
-            dump = calloc(MFBLOCK_SIZE * MIFARE_4K_MAXBLOCK, sizeof(uint8_t));
-            if (dump == NULL) {
-                PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
-                return PM3_EMALLOC;
-            }
-            res = loadFileJSON(filename, (void *)dump, MIFARE_4K_MAXBLOCK * MFBLOCK_SIZE, &bytes_read, NULL);
-            break;
-        }
-        case DICTIONARY: {
-            PrintAndLogEx(ERR, "Error: Only BIN/JSON/EML formats allowed");
-            free(dump);
-            return PM3_EINVARG;
-        }
-    }
-
+    int res = pm3_load_dump(filename, (void **)&dump, &bytes_read, (MFBLOCK_SIZE * MIFARE_4K_MAXBLOCK));
     if (res != PM3_SUCCESS) {
-        PrintAndLogEx(FAILED, "File: " _YELLOW_("%s") ": not found or locked.", filename);
-        free(dump);
-        return PM3_EFILE;
+        return res;
     }
 
     uint16_t block_cnt = MIN(MIFARE_1K_MAXBLOCK, (bytes_read / MFBLOCK_SIZE));
diff --git a/client/src/fileutils.c b/client/src/fileutils.c
index 21c052550..3b9eaa263 100644
--- a/client/src/fileutils.c
+++ b/client/src/fileutils.c
@@ -83,7 +83,7 @@ DumpFileType_t getfiletype(const char *filename) {
         } else if (str_endswith(s, "json")) {
             o = JSON;
         } else if (str_endswith(s, "dic")) {
-            o = DICTIONARY;
+            o = DICTIONARY; 
         } else {
             // mfd, trc, trace is binary
             o = BIN;
@@ -1880,3 +1880,41 @@ int searchFile(char **foundpath, const char *pm3dir, const char *searchname, con
     free(filename);
     return PM3_SUCCESS;
 }
+
+int pm3_load_dump(const char *fn, void **pdump, size_t *dumplen, size_t maxdumplen) {
+
+    int res = 0;
+    DumpFileType_t dftype = getfiletype(fn);
+    switch (dftype) {
+        case BIN: {
+            res = loadFile_safe(fn, ".bin", pdump, dumplen);
+            break;
+        }
+        case EML: {
+            res = loadFileEML_safe(fn, pdump, dumplen);
+            break;
+        }
+        case JSON: {
+            *pdump = calloc(maxdumplen, sizeof(uint8_t));
+            if (*pdump == NULL) {
+                PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
+                return PM3_EMALLOC;
+            }
+            res = loadFileJSON(fn, *pdump, maxdumplen, dumplen, NULL);
+            break;
+        }
+        case DICTIONARY: {
+            PrintAndLogEx(ERR, "Error: Only BIN/EML/JSON formats allowed");
+            return PM3_EINVARG;
+        }
+    }
+
+    if (res != PM3_SUCCESS) {
+        PrintAndLogEx(FAILED, "File: " _YELLOW_("%s") ": not found or locked.", fn);
+        PrintAndLogEx(INFO, "%d", res);
+        free(*pdump);
+        return PM3_EFILE;
+    }
+
+    return res;
+}
\ No newline at end of file
diff --git a/client/src/fileutils.h b/client/src/fileutils.h
index e0db4ffa1..ebc6e6a4a 100644
--- a/client/src/fileutils.h
+++ b/client/src/fileutils.h
@@ -260,4 +260,16 @@ int searchFile(char **foundpath, const char *pm3dir, const char *searchname, con
  * @return
  */
 DumpFileType_t getfiletype(const char *filename);
+
+
+/**
+ * @brief load dump file into a data array dynamically allocated
+ * @param fn
+ * @param pdump pointer to loaded dump
+ * @param dumplen the number of bytes loaded from dump file
+ * @param maxdumplen maximum size of data array in bytes (JSON files)
+ * @return PM3_SUCCESS if OK
+ */
+int pm3_load_dump(const char *fn, void **pdump, size_t *dumplen, size_t maxdumplen);
+
 #endif // FILEUTILS_H
diff --git a/client/src/mifare/mad.c b/client/src/mifare/mad.c
index 4515427ac..1e42cd2e1 100644
--- a/client/src/mifare/mad.c
+++ b/client/src/mifare/mad.c
@@ -24,6 +24,7 @@
 #include "util.h"
 #include "fileutils.h"
 #include "jansson.h"
+#include "mifaredefault.h"
 
 // https://www.nxp.com/docs/en/application-note/AN10787.pdf
 static json_t *mad_known_aids = NULL;
@@ -396,3 +397,10 @@ int MADDFDecodeAndPrint(uint32_t short_aid) {
     close_mad_file(mad_known_aids);
     return PM3_SUCCESS;
 }
+
+bool HasMADKey(uint8_t *d) {
+    if (d == NULL)
+        return false;
+
+    return (memcmp(d + (3 * MFBLOCK_SIZE), g_mifare_mad_key, 6) != 0);
+}
\ No newline at end of file
diff --git a/client/src/mifare/mad.h b/client/src/mifare/mad.h
index 599a3532e..cf951a0b7 100644
--- a/client/src/mifare/mad.h
+++ b/client/src/mifare/mad.h
@@ -28,5 +28,5 @@ int MAD2DecodeAndPrint(uint8_t *sector, bool swapmad, bool verbose);
 int MADDFDecodeAndPrint(uint32_t short_aid);
 int MADCardHolderInfoDecode(uint8_t *data, size_t datalen, bool verbose);
 void MADPrintHeader(void);
-
+bool HasMADKey(uint8_t *d);
 #endif // _MAD_H_
diff --git a/client/src/mifare/mifaredefault.h b/client/src/mifare/mifaredefault.h
index 3fe163d2e..752679f33 100644
--- a/client/src/mifare/mifaredefault.h
+++ b/client/src/mifare/mifaredefault.h
@@ -21,6 +21,8 @@
 
 #include "common.h"
 
+#define MFBLOCK_SIZE 16
+
 static const uint64_t g_mifare_default_keys[] = {
     0xffffffffffff, // Default key (first key used by program if no user defined key)
     0x000000000000, // Blank key
