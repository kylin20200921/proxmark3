commit 256565f82035f0ca51a4d1d0cd8b0ebca7fccf81
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 8 13:08:51 2022 +0100

    coverity fixes for mem leaks. added a macro for freeing cliparser context too

diff --git a/client/src/cmdhfgallagher.c b/client/src/cmdhfgallagher.c
index ec89e75c7..5dbc3c4b8 100644
--- a/client/src/cmdhfgallagher.c
+++ b/client/src/cmdhfgallagher.c
@@ -820,7 +820,7 @@ static int CmdGallagherReader(const char *cmd) {
     uint8_t aid_buf[3] = {0};
     CLIGetHexWithReturn(ctx, 1, aid_buf, &aid_len);
     if (aid_len > 0 && aid_len != 3) {
-        PM3_RET_ERR(PM3_EINVARG, "--aid must be 3 bytes");
+        PM3_RET_ERR_FREE(PM3_EINVARG, "--aid must be 3 bytes");
     }
 
     reverse_aid(aid_buf); // PM3 displays AIDs backwards
@@ -831,7 +831,7 @@ static int CmdGallagherReader(const char *cmd) {
     memcpy(site_key, DEFAULT_SITE_KEY, ARRAYLEN(site_key));
     CLIGetHexWithReturn(ctx, 2, site_key, &site_key_len);
     if (site_key_len > 0 && site_key_len != 16) {
-        PM3_RET_ERR(PM3_EINVARG, "--sitekey must be 16 bytes");
+        PM3_RET_ERR_FREE(PM3_EINVARG, "--sitekey must be 16 bytes");
     }
 
     bool continuous_mode = arg_get_lit(ctx, 3);
@@ -886,14 +886,15 @@ static int CmdGallagherClone(const char *cmd) {
 
     int key_algo = T_DES;
     if (CLIGetOptionList(arg_get_str(ctx, 2), DesfireAlgoOpts, &key_algo)) {
-        return PM3_ESOFT;
+        CLIParserFree(ctx);
+        return PM3_EINVARG;
     }
 
     int key_len = 0;
     uint8_t key[DESFIRE_MAX_KEY_SIZE] = {0};
     CLIGetHexWithReturn(ctx, 3, key, &key_len);
     if (key_len && key_len != desfire_get_key_length(key_algo)) {
-        PM3_RET_ERR(PM3_EINVARG, "%s key must have %d bytes length instead of %d",
+        PM3_RET_ERR_FREE(PM3_EINVARG, "%s key must have %d bytes length instead of %d",
                      CLIGetOptionListStr(DesfireAlgoOpts, key_algo),
                      desfire_get_key_length(key_algo),
                      key_len
@@ -915,7 +916,7 @@ static int CmdGallagherClone(const char *cmd) {
     CLIGetHexWithReturn(ctx, 8, aid_buf, &aid_len);
     if (aid_len > 0) {
         if (aid_len != 3) {
-            PM3_RET_ERR(PM3_EINVARG, "--aid must be 3 bytes");
+            PM3_RET_ERR_FREE(PM3_EINVARG, "--aid must be 3 bytes");
         }
         reverse_aid(aid_buf); // PM3 displays AIDs backwards
         aid = DesfireAIDByteToUint(aid_buf);
@@ -925,7 +926,7 @@ static int CmdGallagherClone(const char *cmd) {
             (aid_buf[2] < 0x20) ||
             (aid_buf[2] > 0x2B)) {
                 // TODO: this should probably be a warning, but key diversification will throw an error later even if we don't
-                PM3_RET_ERR(PM3_EINVARG, "Invalid Gallagher AID %06X, expected 2?81F4, where 0 <= ? <= 0xB", aid);
+                PM3_RET_ERR_FREE(PM3_EINVARG, "Invalid Gallagher AID %06X, expected 2?81F4, where 0 <= ? <= 0xB", aid);
             }
     }
 
@@ -934,7 +935,7 @@ static int CmdGallagherClone(const char *cmd) {
     memcpy(site_key, DEFAULT_SITE_KEY, ARRAYLEN(site_key));
     CLIGetHexWithReturn(ctx, 9, site_key, &site_key_len);
     if (site_key_len > 0 && site_key_len != 16) {
-        PM3_RET_ERR(PM3_EINVARG, "--sitekey must be 16 bytes");
+        PM3_RET_ERR_FREE(PM3_EINVARG, "--sitekey must be 16 bytes");
     }
 
     SetAPDULogging(arg_get_lit(ctx, 10));
@@ -1014,7 +1015,7 @@ static int CmdGallagherDelete(const char *cmd) {
     CLIGetHexWithReturn(ctx, 1, aid_buf, &aid_len);
 
     if (aid_len != 3) {
-        PM3_RET_ERR(PM3_EINVARG, "--aid must be 3 bytes");
+        PM3_RET_ERR_FREE(PM3_EINVARG, "--aid must be 3 bytes");
     }
     reverse_aid(aid_buf); // PM3 displays AIDs backwards
     aid = DesfireAIDByteToUint(aid_buf);
@@ -1024,7 +1025,7 @@ static int CmdGallagherDelete(const char *cmd) {
         (aid_buf[2] < 0x20) ||
         (aid_buf[2] > 0x2B)) {
         // TODO: this should probably be a warning, but key diversification will throw an error later even if we don't
-        PM3_RET_ERR(PM3_EINVARG, "Invalid Gallagher AID %06X, expected 2?81F4, where 0 <= ? <= 0xB", aid);
+        PM3_RET_ERR_FREE(PM3_EINVARG, "Invalid Gallagher AID %06X, expected 2?81F4, where 0 <= ? <= 0xB", aid);
     }
 
     int site_key_len = 0;
@@ -1032,7 +1033,7 @@ static int CmdGallagherDelete(const char *cmd) {
     memcpy(site_key, DEFAULT_SITE_KEY, ARRAYLEN(site_key));
     CLIGetHexWithReturn(ctx, 2, site_key, &site_key_len);
     if (site_key_len > 0 && site_key_len != 16) {
-        PM3_RET_ERR(PM3_EINVARG, "--sitekey must be 16 bytes");
+        PM3_RET_ERR_FREE(PM3_EINVARG, "--sitekey must be 16 bytes");
     }
 
     SetAPDULogging(arg_get_lit(ctx, 3));
@@ -1050,7 +1051,7 @@ static int CmdGallagherDelete(const char *cmd) {
 
     // Update Card Application Directory
     res = hfgal_remove_aid_from_cad(&dctx, site_key, aid, verbose);
-    PM3_RET_IF_ERR_WITH_MSG(res, "Failed removing %06X from the Card Application Directory");
+    PM3_RET_IF_ERR_WITH_MSG(res, "Failed removing %06X from the Card Application Directory", aid);
 
     // Delete application
     res = hfgal_delete_app(&dctx, site_key, aid, verbose);
@@ -1086,7 +1087,7 @@ static int CmdGallagherDiversify(const char *cmd) {
     CLIGetHexWithReturn(ctx, 1, aid_buf, &aid_len);
 
     if (aid_len != 3) {
-        PM3_RET_ERR(PM3_EINVARG, "--aid must be 3 bytes");
+        PM3_RET_ERR_FREE(PM3_EINVARG, "--aid must be 3 bytes");
     }
 
     reverse_aid(aid_buf); // PM3 displays AIDs backwards
@@ -1097,7 +1098,7 @@ static int CmdGallagherDiversify(const char *cmd) {
         (aid_buf[2] < 0x20) || 
         (aid_buf[2] > 0x2B)) {
         // TODO: this should probably be a warning, but key diversification will throw an error later even if we don't
-        PM3_RET_ERR(PM3_EINVARG, "Invalid Gallagher AID %06X, expected 2?81F4, where 0 <= ? <= 0xB", aid);
+        PM3_RET_ERR_FREE(PM3_EINVARG, "Invalid Gallagher AID %06X, expected 2?81F4, where 0 <= ? <= 0xB", aid);
     }
 
     int key_num = arg_get_int_def(ctx, 2, 0);
@@ -1106,7 +1107,7 @@ static int CmdGallagherDiversify(const char *cmd) {
     uint8_t uid[7] = {0};
     CLIGetHexWithReturn(ctx, 3, uid, &uid_len);
     if (uid_len > 0 && uid_len != 4 && uid_len != 7) {
-        PM3_RET_ERR(PM3_EINVARG, "--uid must be 4 or 7 bytes");
+        PM3_RET_ERR_FREE(PM3_EINVARG, "--uid must be 4 or 7 bytes");
     }
 
     int site_key_len = 0;
@@ -1114,7 +1115,7 @@ static int CmdGallagherDiversify(const char *cmd) {
     memcpy(site_key, DEFAULT_SITE_KEY, ARRAYLEN(site_key));
     CLIGetHexWithReturn(ctx, 4, site_key, &site_key_len);
     if (site_key_len > 0 && site_key_len != 16) {
-        PM3_RET_ERR(PM3_EINVARG, "--sitekey must be 16 bytes");
+        PM3_RET_ERR_FREE(PM3_EINVARG, "--sitekey must be 16 bytes");
     }
 
     SetAPDULogging(arg_get_lit(ctx, 5));
diff --git a/client/src/util.h b/client/src/util.h
index 34a67ef10..87a54739f 100644
--- a/client/src/util.h
+++ b/client/src/util.h
@@ -39,7 +39,16 @@ extern bool g_pendingPrompt;
 #define PRINTANDLOG_LOG   2
 
 // Return error
-#define PM3_RET_ERR(err, ...)  { PrintAndLogEx(ERR, __VA_ARGS__); return err; }
+#define PM3_RET_ERR(err, ...)  { \
+    PrintAndLogEx(ERR, __VA_ARGS__); \
+    return err; \
+}
+
+#define PM3_RET_ERR_FREE(err, ...)  { \
+    CLIParserFree(ctx); \
+    PrintAndLogEx(ERR, __VA_ARGS__); \
+    return err; \
+}
 
 // RETurn IF ERRor
 #define PM3_RET_IF_ERR(res)                          if (res != PM3_SUCCESS) {                                               return res; }
