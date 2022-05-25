commit 0bcaf5b47a014e52b7b203a4258559477bbd43b4
Author: iceman1001 <iceman@iuse.se>
Date:   Wed Nov 24 20:24:45 2021 +0100

    nfc decode - now acts text files as input if they have the extension EML.  hacky..

diff --git a/client/src/cmdnfc.c b/client/src/cmdnfc.c
index ff356a980..ca0f31a15 100644
--- a/client/src/cmdnfc.c
+++ b/client/src/cmdnfc.c
@@ -93,17 +93,38 @@ static int CmdNfcDecode(const char *Cmd) {
     }
     int res = PM3_SUCCESS;
     if (fnlen != 0) {
-        size_t dumplen = 0;
+
         uint8_t *dump = NULL;
-        if (loadFile_safe(filename, ".bin", (void **)&dump, &dumplen) != PM3_SUCCESS) {
+        size_t bytes_read = 4096;
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
+            case JSON:
+            case DICTIONARY: {
+                free(dump);
+                PrintAndLogEx(ERR, "Error: Only BIN/EML formats allowed");
+                return PM3_EINVARG;
+            }
+        }
+
+        if (res != PM3_SUCCESS) {
             PrintAndLogEx(ERR, "error, something went wrong when loading file");
             free(dump);
             return PM3_EFILE;
         }
-        res = NDEFDecodeAndPrint(dump, dumplen, verbose);
+
+
+        res = NDEFDecodeAndPrint(dump, bytes_read, verbose);
         if (res != PM3_SUCCESS) {
             PrintAndLogEx(INFO, "Trying to parse NDEF records w/o NDEF header");
-            res = NDEFRecordsDecodeAndPrint(dump, dumplen);
+            res = NDEFRecordsDecodeAndPrint(dump, bytes_read);
         }
         free(dump);
     } else {
