commit 9a063d75fe2e0f9f2ab61d0029a6b7442157c8c8
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jun 20 10:32:08 2021 +0200

    style,  the return codes needs to be adjusted to follow the PM3_E* styled defines.

diff --git a/client/src/iso7816/iso7816core.c b/client/src/iso7816/iso7816core.c
index fae43b9dd..9c336c0bc 100644
--- a/client/src/iso7816/iso7816core.c
+++ b/client/src/iso7816/iso7816core.c
@@ -37,7 +37,11 @@ static isodep_state_t isodep_state = ISODEP_INACTIVE;
 void SetISODEPState(isodep_state_t state) {
     isodep_state = state;
     if (APDULogging) {
-        PrintAndLogEx(SUCCESS, ">>>> ISODEP -> %s%s%s", isodep_state == ISODEP_INACTIVE ? "inactive" : "", isodep_state == ISODEP_NFCA ? "NFC-A" : "", isodep_state == ISODEP_NFCB ? "NFC-B" : "");
+        PrintAndLogEx(SUCCESS, "Setting ISODEP -> %s%s%s"
+            , isodep_state == ISODEP_INACTIVE ? "inactive" : ""
+            , isodep_state == ISODEP_NFCA ? _GREEN_("NFC-A") : ""
+            , isodep_state == ISODEP_NFCB ? _GREEN_("NFC-B") : ""
+            );
     }
 }
 
@@ -51,46 +55,50 @@ int Iso7816Connect(Iso7816CommandChannel channel) {
     }
     // Try to 14a
     // select with no disconnect and set frameLength
-    int selres = SelectCard14443A_4(false, NULL);
-    if (selres == PM3_SUCCESS) {
+    int res = SelectCard14443A_4(false, NULL);
+    if (res == PM3_SUCCESS) {
         SetISODEPState(ISODEP_NFCA);
         return PM3_SUCCESS;
     }
 
     PrintAndLogEx(DEBUG, "No 14a tag spotted, trying 14b");
     // If not 14a, try to 14b
-    selres = select_card_14443b_4(false, NULL);
-    if (selres == PM3_SUCCESS) {
+    res = select_card_14443b_4(false, NULL);
+    if (res == PM3_SUCCESS) {
         SetISODEPState(ISODEP_NFCB);
         return PM3_SUCCESS;
     }
+
     PrintAndLogEx(DEBUG, "No 14b tag spotted, failed to find any tag.");
-    return selres;
+    return res;
 }
 
-int Iso7816ExchangeEx(Iso7816CommandChannel channel, bool ActivateField, bool LeaveFieldON, sAPDU apdu, bool includeLe, uint16_t Le, uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw) {
-    uint8_t data[APDU_RES_LEN] = {0};
+int Iso7816ExchangeEx(Iso7816CommandChannel channel, bool activate_field, bool leave_field_on,
+                      sAPDU apdu, bool include_le, uint16_t le, uint8_t *result,
+                      size_t max_result_len, size_t *result_len, uint16_t *sw) {
 
-    *ResultLen = 0;
-    if (sw) *sw = 0;
-    uint16_t isw = 0;
-    int res = 0;
+    *result_len = 0;
+    if (sw) {
+         *sw = 0;
+    }
 
-    if (ActivateField) {
+    if (activate_field) {
         DropFieldEx(channel);
         msleep(50);
     }
 
     // COMPUTE APDU
     int datalen = 0;
-    if (includeLe) {
-        if (Le == 0) {
-            Le = 0x100;
+    if (include_le) {
+        if (le == 0) {
+            le = 0x100;
         }
     } else {
-        Le = 0;
+        le = 0;
     }
-    if (APDUEncodeS(&apdu, false, Le, data, &datalen)) {
+
+    uint8_t data[APDU_RES_LEN] = {0};
+    if (APDUEncodeS(&apdu, false, le, data, &datalen)) {
         PrintAndLogEx(ERR, "APDU encoding error.");
         return 201;
     }
@@ -98,59 +106,68 @@ int Iso7816ExchangeEx(Iso7816CommandChannel channel, bool ActivateField, bool Le
     if (APDULogging)
         PrintAndLogEx(SUCCESS, ">>>> %s", sprint_hex(data, datalen));
 
+    int res = 0;
+
     switch (channel) {
-        case CC_CONTACTLESS:
+        case CC_CONTACTLESS: {
+
             switch (GetISODEPState()) {
                 case ISODEP_NFCA:
-                    res = ExchangeAPDU14a(data, datalen, ActivateField, LeaveFieldON, Result, (int)MaxResultLen, (int *)ResultLen);
+                    res = ExchangeAPDU14a(data, datalen, activate_field, leave_field_on, result, (int)max_result_len, (int *)result_len);
                     break;
                 case ISODEP_NFCB:
-                    res = exchange_14b_apdu(data, datalen, ActivateField, LeaveFieldON, Result, (int)MaxResultLen, (int *)ResultLen, 4000);
+                    res = exchange_14b_apdu(data, datalen, activate_field, leave_field_on, result, (int)max_result_len, (int *)result_len, 4000);
                     break;
                 case ISODEP_INACTIVE:
-                    if (! ActivateField) {
+                    if (activate_field == false) {
                         PrintAndLogEx(FAILED, "Field currently inactive, cannot send an APDU");
                         return PM3_EIO;
                     }
-                    res = ExchangeAPDU14a(data, datalen, ActivateField, LeaveFieldON, Result, (int)MaxResultLen, (int *)ResultLen);
+                    res = ExchangeAPDU14a(data, datalen, activate_field, leave_field_on, result, (int)max_result_len, (int *)result_len);
                     if (res != PM3_SUCCESS) {
-                        res = exchange_14b_apdu(data, datalen, ActivateField, LeaveFieldON, Result, (int)MaxResultLen, (int *)ResultLen, 4000);
+                        res = exchange_14b_apdu(data, datalen, activate_field, leave_field_on, result, (int)max_result_len, (int *)result_len, 4000);
                     }
                     break;
             }
+
             if (res != PM3_SUCCESS) {
                 return res;
             }
             break;
-        case CC_CONTACT:
+        }
+        case CC_CONTACT: {
             res = 1;
-            if (IfPm3Smartcard())
-                res = ExchangeAPDUSC(false, data, datalen, ActivateField, LeaveFieldON, Result, (int)MaxResultLen, (int *)ResultLen);
+            if (IfPm3Smartcard()) {
+                res = ExchangeAPDUSC(false, data, datalen, activate_field, leave_field_on, result, (int)max_result_len, (int *)result_len);
+            }
 
             if (res) {
                 return res;
             }
             break;
+        }
     }
 
     if (APDULogging)
-        PrintAndLogEx(SUCCESS, "<<<< %s", sprint_hex(Result, *ResultLen));
+        PrintAndLogEx(SUCCESS, "<<<< %s", sprint_hex(result, *result_len));
 
-    if (*ResultLen < 2) {
+    if (*result_len < 2) {
         return 200;
     }
 
-    *ResultLen -= 2;
-    isw = Result[*ResultLen] * 0x0100 + Result[*ResultLen + 1];
-    if (sw)
+    *result_len -= 2;
+    uint16_t isw = (result[*result_len] * 0x0100) + result[*result_len + 1];
+
+    if (sw) {
         *sw = isw;
+    }
 
     if (isw != 0x9000) {
         if (APDULogging) {
             if (*sw >> 8 == 0x61) {
-                PrintAndLogEx(ERR, "APDU chaining len:%02x -->", *sw & 0xff);
+                PrintAndLogEx(ERR, "APDU chaining len %02x", *sw & 0xFF);
             } else {
-                PrintAndLogEx(ERR, "APDU(%02x%02x) ERROR: [%4X] %s", apdu.CLA, apdu.INS, isw, GetAPDUCodeDescription(*sw >> 8, *sw & 0xff));
+                PrintAndLogEx(ERR, "APDU(%02x%02x) ERROR: [%4X] %s", apdu.CLA, apdu.INS, isw, GetAPDUCodeDescription(*sw >> 8, *sw & 0xFF));
                 return 5;
             }
         }
@@ -158,10 +175,32 @@ int Iso7816ExchangeEx(Iso7816CommandChannel channel, bool ActivateField, bool Le
     return PM3_SUCCESS;
 }
 
-int Iso7816Exchange(Iso7816CommandChannel channel, bool LeaveFieldON, sAPDU apdu, uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw) {
-    return Iso7816ExchangeEx(channel, false, LeaveFieldON, apdu, false, 0, Result, MaxResultLen, ResultLen, sw);
+int Iso7816Exchange(Iso7816CommandChannel channel, bool leave_field_on, sAPDU apdu, uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw) {
+    return Iso7816ExchangeEx(channel
+            , false
+            , leave_field_on
+            , apdu
+            , false
+            , 0
+            , result
+            , max_result_len
+            , result_len
+            , sw
+            );
 }
 
-int Iso7816Select(Iso7816CommandChannel channel, bool ActivateField, bool LeaveFieldON, uint8_t *AID, size_t AIDLen, uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw) {
-    return Iso7816ExchangeEx(channel, ActivateField, LeaveFieldON, (sAPDU) {0x00, 0xa4, 0x04, 0x00, AIDLen, AID}, (channel == CC_CONTACTLESS), 0, Result, MaxResultLen, ResultLen, sw);
+int Iso7816Select(Iso7816CommandChannel channel, bool activate_field, bool leave_field_on, uint8_t *aid, size_t aid_len,
+                  uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw) {
+
+    return Iso7816ExchangeEx(channel
+            , activate_field
+            , leave_field_on
+            , (sAPDU) {0x00, 0xa4, 0x04, 0x00, aid_len, aid}
+            , (channel == CC_CONTACTLESS)
+            , 0
+            , result
+            , max_result_len
+            , result_len
+            , sw
+            );
 }
diff --git a/client/src/iso7816/iso7816core.h b/client/src/iso7816/iso7816core.h
index 8c0b44629..beb5e3e90 100644
--- a/client/src/iso7816/iso7816core.h
+++ b/client/src/iso7816/iso7816core.h
@@ -12,9 +12,7 @@
 #define ISO7816CORE_H__
 
 #include "common.h"
-
 #include <inttypes.h>
-
 #include "apduinfo.h"
 
 #define APDU_RES_LEN 260
@@ -41,9 +39,14 @@ isodep_state_t GetISODEPState(void);
 int Iso7816Connect(Iso7816CommandChannel channel);
 
 // exchange
-int Iso7816Exchange(Iso7816CommandChannel channel, bool LeaveFieldON, sAPDU apdu, uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw);
-int Iso7816ExchangeEx(Iso7816CommandChannel channel, bool ActivateField, bool LeaveFieldON, sAPDU apdu, bool IncludeLe, uint16_t Le, uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw);
+int Iso7816Exchange(Iso7816CommandChannel channel, bool leave_field_on, sAPDU apdu, uint8_t *result, size_t max_result_len,
+                       size_t *result_len, uint16_t *sw);
+
+int Iso7816ExchangeEx(Iso7816CommandChannel channel, bool activate_field, bool leave_field_on, sAPDU apdu, bool include_le,
+                       uint16_t le, uint8_t *result,  size_t max_result_len, size_t *result_len, uint16_t *sw);
 
 // search application
-int Iso7816Select(Iso7816CommandChannel channel, bool ActivateField, bool LeaveFieldON, uint8_t *AID, size_t AIDLen, uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw);
+int Iso7816Select(Iso7816CommandChannel channel, bool activate_field, bool leave_field_on, uint8_t *aid, size_t aid_len,
+                       uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw);
+
 #endif
