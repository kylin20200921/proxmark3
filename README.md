commit 0254b2a63ad4a9f2855f0d85aa7bedfe72582b38
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 8 15:29:11 2022 +0100

    cppcheck fixes

diff --git a/client/src/mifare/desfirecrypto.c b/client/src/mifare/desfirecrypto.c
index 185c176f3..96a7caa79 100644
--- a/client/src/mifare/desfirecrypto.c
+++ b/client/src/mifare/desfirecrypto.c
@@ -72,10 +72,17 @@ void DesfireClearIV(DesfireContext_t *ctx) {
 
 void DesfireSetKey(DesfireContext_t *ctx, uint8_t keyNum, DesfireCryptoAlgorithm keyType, uint8_t *key) {
     DesfireClearContext(ctx);
+    if (key == NULL)
+        return;
+
     DesfireSetKeyNoClear(ctx, keyNum, keyType, key);
 }
 
 void DesfireSetKeyNoClear(DesfireContext_t *ctx, uint8_t keyNum, DesfireCryptoAlgorithm keyType, uint8_t *key) {
+
+    if (key == NULL)
+        return;
+
     ctx->keyNum = keyNum;
     ctx->keyType = keyType;
     memcpy(ctx->key, key, desfire_get_key_length(keyType));
@@ -93,8 +100,9 @@ void DesfireSetCommMode(DesfireContext_t *ctx, DesfireCommunicationMode commMode
 void DesfireSetKdf(DesfireContext_t *ctx, uint8_t kdfAlgo, uint8_t *kdfInput, uint8_t kdfInputLen) {
     ctx->kdfAlgo = kdfAlgo;
     ctx->kdfInputLen = kdfInputLen;
-    if (kdfInputLen)
+    if (kdfInputLen) {
         memcpy(ctx->kdfInput, kdfInput, kdfInputLen);
+    }
 }
 
 bool DesfireIsAuthenticated(DesfireContext_t *dctx) {
diff --git a/client/src/ui.c b/client/src/ui.c
index 746ac7396..5dab29d1f 100644
--- a/client/src/ui.c
+++ b/client/src/ui.c
@@ -648,6 +648,10 @@ void print_progress(size_t count, uint64_t max, barMode_t style) {
     int rows;
     rl_reset_screen_size(); // refresh Readline idea of the actual screen width
     rl_get_screen_size(&rows, &cols);
+
+    if (cols < 36)
+        return;
+
     (void) rows;
     if (prev_cols > cols) {
         PrintAndLogEx(NORMAL, _CLEAR_ _TOP_ "");
@@ -655,8 +659,6 @@ void print_progress(size_t count, uint64_t max, barMode_t style) {
     prev_cols = cols;
 #endif
     int width = cols - 35;
-    if (width < 1)
-        return;
 
 #define PERCENTAGE(V, T)   ((V * width) / T)
     // x/8 fractional part of the percentage
diff --git a/client/src/wiegand_formats.c b/client/src/wiegand_formats.c
index bcedea90d..db2223a0b 100644
--- a/client/src/wiegand_formats.c
+++ b/client/src/wiegand_formats.c
@@ -1422,7 +1422,7 @@ int HIDFindCardFormat(const char *format) {
 bool HIDPack(int format_idx, wiegand_card_t *card, wiegand_message_t *packed, bool preamble) {
     memset(packed, 0, sizeof(wiegand_message_t));
 
-    if (format_idx < 0 || format_idx >= ARRAYLEN(FormatTable) - 1)
+    if ((format_idx < 0) || (format_idx >= ARRAYLEN(FormatTable) - 1) )
         return false;
 
     return FormatTable[format_idx].Pack(card, packed, preamble);
