commit 4facebb34d6f474f578fdf566940ad6326100303
Author: iceman1001 <iceman@iuse.se>
Date:   Tue Jun 22 10:52:08 2021 +0200

    input sanitizing

diff --git a/client/src/cipurse/cipursecore.c b/client/src/cipurse/cipursecore.c
index 3d5cd7e8b..a72011717 100644
--- a/client/src/cipurse/cipursecore.c
+++ b/client/src/cipurse/cipursecore.c
@@ -27,14 +27,14 @@ CipurseContext cipurseContext;
 
 static int CIPURSEExchangeEx(bool activate_field, bool leave_field_on, sAPDU apdu, bool include_le,
                              uint16_t le, uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw) {
-    uint8_t data[APDU_RES_LEN] = {0};
-    uint8_t securedata[APDU_RES_LEN] = {0};
-    sAPDU secapdu;
 
-    if (result_len) {
-        *result_len = 0;
+    if (result_len == NULL) {
+        PrintAndLogEx(FAILED, "CIPURSEExchangeEx, result_len is NULL");
+        return PM3_EINVARG;
     }
 
+    *result_len = 0;
+
     if (sw) {
         *sw = 0;
     }
@@ -53,26 +53,32 @@ static int CIPURSEExchangeEx(bool activate_field, bool leave_field_on, sAPDU apd
     // COMPUTE APDU
     int datalen = 0;
     uint16_t xle = include_le ? 0x100 : 0x00;
-    if (xle == 0x100 && le != 0)
+    if (xle == 0x100 && le != 0) {
         xle = le;
+    }
 
+    sAPDU secapdu;
+    uint8_t securedata[APDU_RES_LEN] = {0};
     CipurseCAPDUReqEncode(&cipurseContext, &apdu, &secapdu, securedata, include_le, le);
 
+    uint8_t data[APDU_RES_LEN] = {0};
     if (APDUEncodeS(&secapdu, false, xle, data, &datalen)) {
         PrintAndLogEx(ERR, "APDU encoding error.");
         return 201;
     }
 
-    if (GetAPDULogging())
+    if (GetAPDULogging()) {
         PrintAndLogEx(SUCCESS, ">>>> %s", sprint_hex(data, datalen));
+    }
 
     res = ExchangeAPDU14a(data, datalen, activate_field, leave_field_on, result, (int)max_result_len, (int *)result_len);
     if (res) {
         return res;
     }
 
-    if (GetAPDULogging())
+    if (GetAPDULogging()) {
         PrintAndLogEx(SUCCESS, "<<<< %s", sprint_hex(result, *result_len));
+    }
 
     if (*result_len < 2) {
         return 200;
@@ -80,10 +86,12 @@ static int CIPURSEExchangeEx(bool activate_field, bool leave_field_on, sAPDU apd
 
     size_t rlen = 0;
     if (*result_len == 2) {
-        if (cipurseContext.RequestSecurity == CPSMACed || cipurseContext.RequestSecurity == CPSEncrypted)
+        if (cipurseContext.RequestSecurity == CPSMACed || cipurseContext.RequestSecurity == CPSEncrypted) {
             CipurseCClearContext(&cipurseContext);
+        }
 
         isw = result[0] * 0x0100 + result[1];
+
     } else {
         CipurseCAPDURespDecode(&cipurseContext, result, *result_len, securedata, &rlen, &isw);
         memcpy(result, securedata, rlen);
