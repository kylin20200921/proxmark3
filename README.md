commit 045f30d00db40a84f8c0bbe8d89a30ce813fed3c
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Mar 12 18:57:06 2022 +0100

    refactoring function to download file from spiffs

diff --git a/client/src/cmdflashmemspiffs.c b/client/src/cmdflashmemspiffs.c
index b3234ba1a..af84d277b 100644
--- a/client/src/cmdflashmemspiffs.c
+++ b/client/src/cmdflashmemspiffs.c
@@ -89,6 +89,41 @@ out:
     return ret_val;
 }
 
+int flashmem_spiffs_download(char *fn, uint8_t fnlen, void **pdest, size_t *destlen) {
+       // get size from spiffs itself !
+    clearCommandBuffer();
+    SendCommandNG(CMD_SPIFFS_STAT, (uint8_t *)fn, fnlen);
+    PacketResponseNG resp;
+    if (WaitForResponseTimeout(CMD_SPIFFS_STAT, &resp, 2000) == false) {
+        PrintAndLogEx(WARNING, "timeout while waiting for reply.");
+        return PM3_ETIMEOUT;
+    }
+
+    uint32_t len = resp.data.asDwords[0];
+    if (len == 0) {
+        PrintAndLogEx(ERR, "error, failed to retrieve file stats on SPIFFSS");
+        return PM3_EFAILED;
+    }
+
+    *pdest = calloc(len, sizeof(uint8_t));
+    if (*pdest == false) {
+        PrintAndLogEx(ERR, "error, cannot allocate memory ");
+        return PM3_EMALLOC;
+    }
+
+    uint32_t start_index = 0;
+    PrintAndLogEx(INFO, "downloading "_YELLOW_("%u") " bytes from `" _YELLOW_("%s") "` (spiffs)", len, fn);
+
+    if (GetFromDevice(SPIFFS, *pdest, len, start_index, (uint8_t *)fn, fnlen, NULL, -1, true) == 0) {
+        PrintAndLogEx(FAILED, "error, downloading from spiffs");
+        free(*pdest);
+        return PM3_EFLASH;
+    }
+
+    *destlen = len;
+    return PM3_SUCCESS;
+}
+
 static int CmdFlashMemSpiFFSMount(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "mem spiffs mount",
@@ -501,45 +536,22 @@ static int CmdFlashMemSpiFFSView(const char *Cmd) {
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
 
-    int slen = 0;
-    char src[32] = {0};
-    CLIParamStrToBuf(arg_get_str(ctx, 1), (uint8_t *)src, 32, &slen);
+    int fnlen = 0;
+    char fn[32] = {0};
+    CLIParamStrToBuf(arg_get_str(ctx, 1), (uint8_t *)fn, 32, &fnlen);
 
     int breaks = arg_get_int_def(ctx, 2, 32);
     CLIParserFree(ctx);
-
-    // get size from spiffs itself !
-    clearCommandBuffer();
-    SendCommandNG(CMD_SPIFFS_STAT, (uint8_t *)src, slen);
-    PacketResponseNG resp;
-    if (WaitForResponseTimeout(CMD_SPIFFS_STAT, &resp, 2000) == false) {
-        PrintAndLogEx(WARNING, "timeout while waiting for reply.");
-        return PM3_ETIMEOUT;
-    }
-
-    uint32_t len = resp.data.asDwords[0];
-    if (len == 0) {
-        PrintAndLogEx(ERR, "error, failed to retrieve file stats on SPIFFSS");
-        return PM3_EFAILED;
-    }
-
-    uint8_t *dump = calloc(len, sizeof(uint8_t));
-    if (!dump) {
-        PrintAndLogEx(ERR, "error, cannot allocate memory ");
-        return PM3_EMALLOC;
-    }
-
-    uint32_t start_index = 0;
-    PrintAndLogEx(INFO, "downloading "_YELLOW_("%u") " bytes from `" _YELLOW_("%s") "` (spiffs)", len, src);
-
-    if (!GetFromDevice(SPIFFS, dump, len, start_index, (uint8_t *)src, slen, NULL, -1, true)) {
-        PrintAndLogEx(FAILED, "error, downloading from spiffs");
-        free(dump);
-        return PM3_EFLASH;
+ 
+    uint8_t *dump = NULL;
+    size_t dumplen = 0;
+    int res = flashmem_spiffs_download(fn, fnlen, (void **)&dump, &dumplen);
+    if (res != PM3_SUCCESS) {
+        return res;
     }
 
     PrintAndLogEx(NORMAL, "");
-    print_hex_break(dump, len, breaks);
+    print_hex_break(dump, dumplen, breaks);
     PrintAndLogEx(NORMAL, "");
     free(dump);
     return PM3_SUCCESS;
diff --git a/client/src/cmdflashmemspiffs.h b/client/src/cmdflashmemspiffs.h
index 042704b11..5a85aa6f4 100644
--- a/client/src/cmdflashmemspiffs.h
+++ b/client/src/cmdflashmemspiffs.h
@@ -23,5 +23,6 @@
 
 int CmdFlashMemSpiFFS(const char *Cmd);
 int flashmem_spiffs_load(char *destfn, uint8_t *data, size_t datalen);
+int flashmem_spiffs_download(char *fn, uint8_t fnlen, void **pdest, size_t *destlen);
 
 #endif
