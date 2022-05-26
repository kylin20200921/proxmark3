commit ccf23146d6aaa8fa8b626c3c4d82e44f8a7e6486
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Feb 4 21:25:49 2022 +0100

    style

diff --git a/client/src/mifare/desfirecore.c b/client/src/mifare/desfirecore.c
index 2fc1446fc..baf89c480 100644
--- a/client/src/mifare/desfirecore.c
+++ b/client/src/mifare/desfirecore.c
@@ -805,7 +805,8 @@ static void DesfireSplitBytesToBlock(uint8_t *blockdata, size_t *blockdatacount,
     }
 }
 
-int DesfireExchangeEx(bool activate_field, DesfireContext_t *ctx, uint8_t cmd, uint8_t *data, size_t datalen, uint8_t *respcode, uint8_t *resp, size_t *resplen, bool enable_chaining, size_t splitbysize) {
+int DesfireExchangeEx(bool activate_field, DesfireContext_t *ctx, uint8_t cmd, uint8_t *data, size_t datalen, uint8_t *respcode,
+                     uint8_t *resp, size_t *resplen, bool enable_chaining, size_t splitbysize) {
     int res = PM3_SUCCESS;
 
     if (!PrintChannelModeWarning(cmd, ctx->secureChannel, ctx->cmdSet, ctx->commMode))
