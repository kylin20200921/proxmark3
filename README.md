commit 9a746db6f954aee0ad90675311b5f245b3a0ef10
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 1 15:59:02 2022 +0100

    cppcheck fixes

diff --git a/client/src/mifare/desfiresecurechan.c b/client/src/mifare/desfiresecurechan.c
index d7c4b1600..78bb6ebee 100644
--- a/client/src/mifare/desfiresecurechan.c
+++ b/client/src/mifare/desfiresecurechan.c
@@ -267,10 +267,10 @@ static bool DesfireISOChannelValidCmd(uint8_t cmd) {
 }
 
 static void DesfireSecureChannelEncodeD40(DesfireContext_t *ctx, uint8_t cmd, uint8_t *srcdata, size_t srcdatalen, uint8_t *dstdata, size_t *dstdatalen) {
-    uint8_t *data  = calloc(DESFIRE_BUFFER_SIZE, 1);
+
+    uint8_t *data  = calloc(DESFIRE_BUFFER_SIZE, sizeof(uint8_t));
     if (data == NULL)
         return;
-    size_t rlen = 0;
 
     memcpy(dstdata, srcdata, srcdatalen);
     *dstdatalen = srcdatalen;
@@ -279,6 +279,8 @@ static void DesfireSecureChannelEncodeD40(DesfireContext_t *ctx, uint8_t cmd, ui
     if (srcdatalen < hdrlen)
         hdrlen = srcdatalen;
 
+    size_t rlen;
+
     if (ctx->commMode == DCMMACed || (ctx->commMode == DCMEncrypted && srcdatalen <= hdrlen)) {
         if (srcdatalen == 0) {
             free(data);
@@ -330,14 +332,15 @@ static void DesfireSecureChannelEncodeD40(DesfireContext_t *ctx, uint8_t cmd, ui
         *dstdatalen = rlen;
         ctx->commMode = DCMEncrypted;
     }
+
     free(data);
 }
 
 static void DesfireSecureChannelEncodeEV1(DesfireContext_t *ctx, uint8_t cmd, uint8_t *srcdata, size_t srcdatalen, uint8_t *dstdata, size_t *dstdatalen) {
-    uint8_t *data  = calloc(DESFIRE_BUFFER_SIZE, 1);
+
+    uint8_t *data  = calloc(DESFIRE_BUFFER_SIZE, sizeof(uint8_t));
     if (data == NULL)
         return;
-    size_t rlen = 0;
 
     memcpy(dstdata, srcdata, srcdatalen);
     *dstdatalen = srcdatalen;
@@ -346,6 +349,8 @@ static void DesfireSecureChannelEncodeEV1(DesfireContext_t *ctx, uint8_t cmd, ui
     if (srcdatalen < hdrlen)
         hdrlen = srcdatalen;
 
+    size_t rlen;
+
     // we calc MAC anyway
     // if encypted channel and no data - we only calc MAC
     if (ctx->commMode == DCMPlain || ctx->commMode == DCMMACed || (ctx->commMode == DCMEncrypted && srcdatalen <= hdrlen)) {
@@ -360,6 +365,7 @@ static void DesfireSecureChannelEncodeEV1(DesfireContext_t *ctx, uint8_t cmd, ui
             memcpy(&dstdata[srcdatalen], cmac, DesfireGetMACLength(ctx));
             *dstdatalen = srcdatalen + DesfireGetMACLength(ctx);
         }
+
     } else if (ctx->commMode == DCMEncrypted || ctx->commMode == DCMEncryptedWithPadding) {
         uint8_t paddinglen = (ctx->commMode == DCMEncryptedWithPadding) ? 1 : 0;
         rlen = padded_data_length(srcdatalen + 4 + paddinglen - hdrlen, desfire_get_key_block_length(ctx->keyType));
@@ -395,10 +401,10 @@ static void DesfireSecureChannelEncodeEV1(DesfireContext_t *ctx, uint8_t cmd, ui
 }
 
 static void DesfireSecureChannelEncodeEV2(DesfireContext_t *ctx, uint8_t cmd, uint8_t *srcdata, size_t srcdatalen, uint8_t *dstdata, size_t *dstdatalen) {
-    uint8_t *data  = calloc(DESFIRE_BUFFER_SIZE, 1);
+
+    uint8_t *data  = calloc(DESFIRE_BUFFER_SIZE, sizeof(uint8_t));
     if (data == NULL)
         return;
-    size_t rlen = 0;
 
     memcpy(dstdata, srcdata, srcdatalen);
     *dstdatalen = srcdatalen;
@@ -416,6 +422,7 @@ static void DesfireSecureChannelEncodeEV2(DesfireContext_t *ctx, uint8_t cmd, ui
     } else if (ctx->commMode == DCMEncrypted || ctx->commMode == DCMEncryptedWithPadding || ctx->commMode == DCMEncryptedPlain) {
         memcpy(dstdata, srcdata, hdrlen);
 
+        size_t rlen = 0;
         if (srcdatalen > hdrlen) {
             rlen = padded_data_length(srcdatalen + 1 - hdrlen, desfire_get_key_block_length(ctx->keyType));
             memcpy(data, &srcdata[hdrlen], srcdatalen - hdrlen);
@@ -437,10 +444,10 @@ static void DesfireSecureChannelEncodeEV2(DesfireContext_t *ctx, uint8_t cmd, ui
 }
 
 static void DesfireSecureChannelEncodeLRP(DesfireContext_t *ctx, uint8_t cmd, uint8_t *srcdata, size_t srcdatalen, uint8_t *dstdata, size_t *dstdatalen) {
-    uint8_t *data  = calloc(DESFIRE_BUFFER_SIZE, 1);
+
+    uint8_t *data  = calloc(DESFIRE_BUFFER_SIZE, sizeof(uint8_t));
     if (data == NULL)
         return;
-    size_t rlen = 0;
 
     memcpy(dstdata, srcdata, srcdatalen);
     *dstdatalen = srcdatalen;
@@ -458,6 +465,7 @@ static void DesfireSecureChannelEncodeLRP(DesfireContext_t *ctx, uint8_t cmd, ui
     } else if (ctx->commMode == DCMEncrypted || ctx->commMode == DCMEncryptedWithPadding || ctx->commMode == DCMEncryptedPlain) {
         memcpy(dstdata, srcdata, hdrlen);
 
+        size_t rlen = 0;
         if (srcdatalen > hdrlen) {
             rlen = padded_data_length(srcdatalen + 1 - hdrlen, desfire_get_key_block_length(ctx->keyType));
             memcpy(data, &srcdata[hdrlen], srcdatalen - hdrlen);
@@ -502,10 +510,10 @@ void DesfireSecureChannelEncode(DesfireContext_t *ctx, uint8_t cmd, uint8_t *src
 }
 
 static void DesfireSecureChannelDecodeD40(DesfireContext_t *ctx, uint8_t *srcdata, size_t srcdatalen, uint8_t respcode, uint8_t *dstdata, size_t *dstdatalen) {
-    uint8_t *data  = calloc(DESFIRE_BUFFER_SIZE, 1);
+
+    uint8_t *data  = calloc(DESFIRE_BUFFER_SIZE, sizeof(uint8_t));
     if (data == NULL)
         return;
-    size_t rlen = 0;
 
     memcpy(dstdata, srcdata, srcdatalen);
     *dstdatalen = srcdatalen;
@@ -515,7 +523,7 @@ static void DesfireSecureChannelDecodeD40(DesfireContext_t *ctx, uint8_t *srcdat
             size_t maclen = DesfireGetMACLength(ctx);
             if (srcdatalen > maclen && DesfireEV1D40ReceiveMAC(ctx, ctx->lastCommand)) {
                 uint8_t mac[16] = {0};
-                rlen = padded_data_length(srcdatalen - maclen, desfire_get_key_block_length(ctx->keyType));
+                size_t rlen = padded_data_length(srcdatalen - maclen, desfire_get_key_block_length(ctx->keyType));
                 memcpy(data, srcdata, srcdatalen - maclen);
                 DesfireCryptoEncDecEx(ctx, DCOSessionKeyMac, data, rlen, NULL, true, true, mac);
 
@@ -562,7 +570,8 @@ static void DesfireSecureChannelDecodeD40(DesfireContext_t *ctx, uint8_t *srcdat
 }
 
 static void DesfireSecureChannelDecodeEV1(DesfireContext_t *ctx, uint8_t *srcdata, size_t srcdatalen, uint8_t respcode, uint8_t *dstdata, size_t *dstdatalen) {
-    uint8_t *data  = calloc(DESFIRE_BUFFER_SIZE, 1);
+
+    uint8_t *data  = calloc(DESFIRE_BUFFER_SIZE, sizeof(uint8_t));
     if (data == NULL)
         return;
 
@@ -736,13 +745,14 @@ static void DesfireSecureChannelDecodeLRP(DesfireContext_t *ctx, uint8_t *srcdat
 static void DesfireISODecode(DesfireContext_t *ctx, uint8_t *srcdata, size_t srcdatalen, uint8_t *dstdata, size_t *dstdatalen) {
     memcpy(dstdata, srcdata, srcdatalen);
     *dstdatalen = srcdatalen;
-    uint8_t *data  = calloc(DESFIRE_BUFFER_SIZE, 1);
-    if (data == NULL)
-        return;
 
     if (srcdatalen < DesfireGetMACLength(ctx))
         return;
 
+    uint8_t *data  = calloc(DESFIRE_BUFFER_SIZE, 1);
+    if (data == NULL)
+        return;
+
     uint8_t maclen = DesfireGetMACLength(ctx);
     if (DesfireIsAuthenticated(ctx)) {
         memcpy(data, srcdata, srcdatalen - maclen);
