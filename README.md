commit 0d2cd089c0ca09fcebf0c888e4fac692ba4f30dc
Author: iceman1001 <iceman@iuse.se>
Date:   Tue Jun 22 10:43:48 2021 +0200

    coverity fix,  textual, style

diff --git a/client/src/cipurse/cipursecore.c b/client/src/cipurse/cipursecore.c
index 03ab558af..c3024a9b2 100644
--- a/client/src/cipurse/cipursecore.c
+++ b/client/src/cipurse/cipursecore.c
@@ -18,23 +18,30 @@
 #include "cmdhf14a.h"
 #include "emv/emvcore.h"
 #include "emv/emvjson.h"
+#include "iso7816/apduinfo.h"
 #include "ui.h"
 #include "util.h"
 
 // context for secure channel
 CipurseContext cipurseContext;
 
-static int CIPURSEExchangeEx(bool ActivateField, bool LeaveFieldON, sAPDU apdu, bool IncludeLe, uint16_t Le, uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw) {
+static int CIPURSEExchangeEx(bool activate_field, bool leave_field_on, sAPDU apdu, bool include_le,
+                             uint16_t le, uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw) {
     uint8_t data[APDU_RES_LEN] = {0};
     uint8_t securedata[APDU_RES_LEN] = {0};
     sAPDU secapdu;
 
-    *ResultLen = 0;
-    if (sw) *sw = 0;
+    if (result_len) {
+        *result_len = 0;
+    }
+
+    if (sw) {
+        *sw = 0;
+    }
     uint16_t isw = 0;
     int res = 0;
 
-    if (ActivateField) {
+    if (activate_field) {
         DropField();
         msleep(50);
     }
@@ -45,11 +52,11 @@ static int CIPURSEExchangeEx(bool ActivateField, bool LeaveFieldON, sAPDU apdu,
 
     // COMPUTE APDU
     int datalen = 0;
-    uint16_t xle = IncludeLe ? 0x100 : 0x00;
-    if (xle == 0x100 && Le != 0)
-        xle = Le;
+    uint16_t xle = include_le ? 0x100 : 0x00;
+    if (xle == 0x100 && le != 0)
+        xle = le;
 
-    CipurseCAPDUReqEncode(&cipurseContext, &apdu, &secapdu, securedata, IncludeLe, Le);
+    CipurseCAPDUReqEncode(&cipurseContext, &apdu, &secapdu, securedata, include_le, le);
 
     if (APDUEncodeS(&secapdu, false, xle, data, &datalen)) {
         PrintAndLogEx(ERR, "APDU encoding error.");
@@ -59,34 +66,36 @@ static int CIPURSEExchangeEx(bool ActivateField, bool LeaveFieldON, sAPDU apdu,
     if (GetAPDULogging())
         PrintAndLogEx(SUCCESS, ">>>> %s", sprint_hex(data, datalen));
 
-    res = ExchangeAPDU14a(data, datalen, ActivateField, LeaveFieldON, Result, (int)MaxResultLen, (int *)ResultLen);
+    res = ExchangeAPDU14a(data, datalen, activate_field, leave_field_on, result, (int)max_result_len, (int *)result_len);
     if (res) {
         return res;
     }
 
     if (GetAPDULogging())
-        PrintAndLogEx(SUCCESS, "<<<< %s", sprint_hex(Result, *ResultLen));
+        PrintAndLogEx(SUCCESS, "<<<< %s", sprint_hex(result, *result_len));
 
-    if (*ResultLen < 2) {
+    if (*result_len < 2) {
         return 200;
     }
 
     size_t rlen = 0;
-    if (*ResultLen == 2) {
+    if (*result_len == 2) {
         if (cipurseContext.RequestSecurity == CPSMACed || cipurseContext.RequestSecurity == CPSEncrypted)
             CipurseCClearContext(&cipurseContext);
 
-        isw = Result[0] * 0x0100 + Result[1];
+        isw = result[0] * 0x0100 + result[1];
     } else {
-        CipurseCAPDURespDecode(&cipurseContext, Result, *ResultLen, securedata, &rlen, &isw);
-        memcpy(Result, securedata, rlen);
+        CipurseCAPDURespDecode(&cipurseContext, result, *result_len, securedata, &rlen, &isw);
+        memcpy(result, securedata, rlen);
     }
 
-    if (ResultLen != NULL)
-        *ResultLen = rlen;
+    if (result_len != NULL) {
+        *result_len = rlen;
+    }
 
-    if (sw != NULL)
+    if (sw != NULL) {
         *sw = isw;
+    }
 
     if (isw != 0x9000) {
         if (GetAPDULogging()) {
@@ -102,69 +111,69 @@ static int CIPURSEExchangeEx(bool ActivateField, bool LeaveFieldON, sAPDU apdu,
     return PM3_SUCCESS;
 }
 
-static int CIPURSEExchange(sAPDU apdu, uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw) {
-    return CIPURSEExchangeEx(false, true, apdu, true, 0, Result, MaxResultLen, ResultLen, sw);
+static int CIPURSEExchange(sAPDU apdu, uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw) {
+    return CIPURSEExchangeEx(false, true, apdu, true, 0, result, max_result_len, result_len, sw);
 }
 
-int CIPURSESelect(bool ActivateField, bool LeaveFieldON, uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw) {
+int CIPURSESelect(bool activate_field, bool leave_field_on, uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw) {
     uint8_t data[] = {0x41, 0x44, 0x20, 0x46, 0x31};
     CipurseCClearContext(&cipurseContext);
 
-    return EMVSelect(CC_CONTACTLESS, ActivateField, LeaveFieldON, data, sizeof(data), Result, MaxResultLen, ResultLen, sw, NULL);
+    return EMVSelect(CC_CONTACTLESS, activate_field, leave_field_on, data, sizeof(data), result, max_result_len, result_len, sw, NULL);
 }
 
-int CIPURSEChallenge(uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw) {
-    return CIPURSEExchangeEx(false, true, (sAPDU) {0x00, 0x84, 0x00, 0x00, 0x00, NULL}, true, 0x16, Result, MaxResultLen, ResultLen, sw);
+int CIPURSEChallenge(uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw) {
+    return CIPURSEExchangeEx(false, true, (sAPDU) {0x00, 0x84, 0x00, 0x00, 0x00, NULL}, true, 0x16, result, max_result_len, result_len, sw);
 }
 
-int CIPURSEMutalAuthenticate(uint8_t keyIndex, uint8_t *params, uint8_t paramslen, uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw) {
-    return CIPURSEExchangeEx(false, true, (sAPDU) {0x00, 0x82, 0x00, keyIndex, paramslen, params}, true, 0x10, Result, MaxResultLen, ResultLen, sw);
+int CIPURSEMutalAuthenticate(uint8_t keyindex, uint8_t *params, uint8_t paramslen, uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw) {
+    return CIPURSEExchangeEx(false, true, (sAPDU) {0x00, 0x82, 0x00, keyindex, paramslen, params}, true, 0x10, result, max_result_len, result_len, sw);
 }
 
-int CIPURSECreateFile(uint8_t *attr, uint16_t attrlen, uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw) {
-    return CIPURSEExchangeEx(false, true, (sAPDU) {0x00, 0xe4, 0x00, 0x00, attrlen, attr}, false, 0, Result, MaxResultLen, ResultLen, sw);
+int CIPURSECreateFile(uint8_t *attr, uint16_t attrlen, uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw) {
+    return CIPURSEExchangeEx(false, true, (sAPDU) {0x00, 0xe4, 0x00, 0x00, attrlen, attr}, false, 0, result, max_result_len, result_len, sw);
 }
 
-int CIPURSEDeleteFile(uint16_t fileID, uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw) {
-    uint8_t fileIdBin[] = {fileID >> 8, fileID & 0xff};
-    return CIPURSEExchangeEx(false, true, (sAPDU) {0x00, 0xe4, 0x00, 0x00, 02, fileIdBin}, false, 0, Result, MaxResultLen, ResultLen, sw);
+int CIPURSEDeleteFile(uint16_t fileid, uint8_t *Result, size_t max_result_len, size_t *result_len, uint16_t *sw) {
+    uint8_t fileIdBin[] = {fileid >> 8, fileid & 0xff};
+    return CIPURSEExchangeEx(false, true, (sAPDU) {0x00, 0xe4, 0x00, 0x00, 02, fileIdBin}, false, 0, Result, max_result_len, result_len, sw);
 }
 
-int CIPURSESelectFile(uint16_t fileID, uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw) {
-    uint8_t fileIdBin[] = {fileID >> 8, fileID & 0xff};
-    return CIPURSEExchange((sAPDU) {0x00, 0xa4, 0x00, 0x00, 02, fileIdBin}, Result, MaxResultLen, ResultLen, sw);
+int CIPURSESelectFile(uint16_t fileid, uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw) {
+    uint8_t fileIdBin[] = {fileid >> 8, fileid & 0xff};
+    return CIPURSEExchange((sAPDU) {0x00, 0xa4, 0x00, 0x00, 02, fileIdBin}, result, max_result_len, result_len, sw);
 }
 
-int CIPURSESelectMFFile(uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw) {
-    return CIPURSEExchange((sAPDU) {0x00, 0xa4, 0x00, 0x00, 0, NULL}, Result, MaxResultLen, ResultLen, sw);
+int CIPURSESelectMFFile(uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw) {
+    return CIPURSEExchange((sAPDU) {0x00, 0xa4, 0x00, 0x00, 0, NULL}, result, max_result_len, result_len, sw);
 }
 
-int CIPURSEReadFileAttributes(uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw) {
-    return CIPURSEExchange((sAPDU) {0x80, 0xce, 0x00, 0x00, 0, NULL}, Result, MaxResultLen, ResultLen, sw);
+int CIPURSEReadFileAttributes(uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw) {
+    return CIPURSEExchange((sAPDU) {0x80, 0xce, 0x00, 0x00, 0, NULL}, result, max_result_len, result_len, sw);
 }
 
-int CIPURSEReadBinary(uint16_t offset, uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw) {
-    return CIPURSEExchange((sAPDU) {0x00, 0xb0, (offset >> 8) & 0x7f, offset & 0xff, 0, NULL}, Result, MaxResultLen, ResultLen, sw);
+int CIPURSEReadBinary(uint16_t offset, uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw) {
+    return CIPURSEExchange((sAPDU) {0x00, 0xb0, (offset >> 8) & 0x7f, offset & 0xff, 0, NULL}, result, max_result_len, result_len, sw);
 }
 
-int CIPURSEUpdateBinary(uint16_t offset, uint8_t *data, uint16_t datalen, uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw) {
-    return CIPURSEExchange((sAPDU) {0x00, 0xd6, (offset >> 8) & 0x7f, offset & 0xff, datalen, data}, Result, MaxResultLen, ResultLen, sw);
+int CIPURSEUpdateBinary(uint16_t offset, uint8_t *data, uint16_t datalen, uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw) {
+    return CIPURSEExchange((sAPDU) {0x00, 0xd6, (offset >> 8) & 0x7f, offset & 0xff, datalen, data}, result, max_result_len, result_len, sw);
 }
 
-bool CIPURSEChannelAuthenticate(uint8_t keyIndex, uint8_t *key, bool verbose) {
+bool CIPURSEChannelAuthenticate(uint8_t keyindex, uint8_t *key, bool verbose) {
     uint8_t buf[APDU_RES_LEN] = {0};
     size_t len = 0;
     uint16_t sw = 0;
 
     CipurseContext cpc = {0};
-    CipurseCSetKey(&cpc, keyIndex, key);
+    CipurseCSetKey(&cpc, keyindex, key);
 
     // get RP, rP
     int res = CIPURSEChallenge(buf, sizeof(buf), &len, &sw);
     if (res != 0 || len != 0x16) {
-        if (verbose)
-            PrintAndLogEx(ERR, "Cipurse get challenge " _RED_("error") ". Card returns 0x%04x.", sw);
-
+        if (verbose) {
+            PrintAndLogEx(ERR, "Cipurse get challenge ( " _RED_("fail") " ). Card returns 0x%04x", sw);
+        }
         return false;
     }
     CipurseCSetRandomFromPICC(&cpc, buf);
@@ -174,17 +183,20 @@ bool CIPURSEChannelAuthenticate(uint8_t keyIndex, uint8_t *key, bool verbose) {
     CipurseCAuthenticateHost(&cpc, authparams);
 
     // authenticate
-    res = CIPURSEMutalAuthenticate(keyIndex, authparams, sizeof(authparams), buf, sizeof(buf), &len, &sw);
+    res = CIPURSEMutalAuthenticate(keyindex, authparams, sizeof(authparams), buf, sizeof(buf), &len, &sw);
     if (res != 0 || sw != 0x9000 || len != 16) {
         if (sw == 0x6988) {
-            if (verbose)
-                PrintAndLogEx(ERR, "Cipurse authentication " _RED_("error") ". Wrong key.");
+            if (verbose) {
+                PrintAndLogEx(WARNING, "Authentication ( " _RED_("fail") " ). Wrong key");
+            }
         } else if (sw == 0x6A88) {
-            if (verbose)
-                PrintAndLogEx(ERR, "Cipurse authentication " _RED_("error") ". Wrong key number.");
+            if (verbose) {
+                PrintAndLogEx(WARNING, "Authentication ( " _RED_("fail") " ). Wrong key number");
+            }
         } else {
-            if (verbose)
-                PrintAndLogEx(ERR, "Cipurse authentication " _RED_("error") ". Card returns 0x%04x.", sw);
+            if (verbose) {
+                PrintAndLogEx(WARNING, "Authentication ( " _RED_("fail") " ). Card returns 0x%04x", sw);
+            }
         }
 
         CipurseCClearContext(&cipurseContext);
@@ -192,15 +204,17 @@ bool CIPURSEChannelAuthenticate(uint8_t keyIndex, uint8_t *key, bool verbose) {
     }
 
     if (CipurseCCheckCT(&cpc, buf)) {
-        if (verbose)
-            PrintAndLogEx(INFO, "Authentication " _GREEN_("OK"));
+        if (verbose) {
+            PrintAndLogEx(SUCCESS, "Authentication ( " _GREEN_("ok") " )");
+        }
 
         CipurseCChannelSetSecurityLevels(&cpc, CPSMACed, CPSMACed);
         memcpy(&cipurseContext, &cpc, sizeof(CipurseContext));
         return true;
     } else {
-        if (verbose)
-            PrintAndLogEx(ERR, "Authentication " _RED_("ERROR") " card returned wrong CT");
+        if (verbose) {
+            PrintAndLogEx(WARNING, "Authentication ( " _RED_("fail") " ) card returned wrong CT");
+        }
 
         CipurseCClearContext(&cipurseContext);
         return false;
@@ -212,21 +226,20 @@ void CIPURSECSetActChannelSecurityLevels(CipurseChannelSecurityLevel req, Cipurs
 }
 
 static void CIPURSEPrintPersoMode(uint8_t data) {
-    if (data & 0x01)
+    if ((data & 0x01) == 0x01)
         PrintAndLogEx(INFO, "Perso: filesystem");
-    if (data & 0x02)
+    if ((data & 0x02) == 0x02)
         PrintAndLogEx(INFO, "Perso: EMV");
-    if (data & 0x04)
+    if ((data & 0x04) == 0x04)
         PrintAndLogEx(INFO, "Perso: transaction supported");
-
 }
 
 static void CIPURSEPrintProfileInfo(uint8_t data) {
-    if (data & 0x01)
+    if ((data & 0x01) == 0x01)
         PrintAndLogEx(INFO, "Profile: L");
-    if (data & 0x02)
+    if ((data & 0x02) == 0x02)
         PrintAndLogEx(INFO, "Profile: S");
-    if (data & 0x04)
+    if ((data & 0x04) == 0x04)
         PrintAndLogEx(INFO, "Profile: T");
 }
 
@@ -239,12 +252,13 @@ static void CIPURSEPrintManufacturerInfo(uint8_t data) {
 
 void CIPURSEPrintInfoFile(uint8_t *data, size_t len) {
     if (len < 2) {
-        PrintAndLogEx(ERR, "Info file length " _RED_("ERROR"));
+        PrintAndLogEx(FAILED, "Info file length too short");
         return;
     }
 
-    PrintAndLogEx(INFO, "------------ INFO ------------");
-    PrintAndLogEx(INFO, "CIPURSE version %d revision %d", data[0], data[1]);
+    PrintAndLogEx(INFO, "--- " _CYAN_("CIPURSE Information") "---------------------");
+    PrintAndLogEx(INFO, "version.... " _YELLOW_("%d"), data[0]);
+    PrintAndLogEx(INFO, "revision... " _YELLOW_("%d"), data[1]);
 
     if (len >= 3)
         CIPURSEPrintPersoMode(data[2]);
@@ -278,101 +292,111 @@ static void CIPURSEPrintFileDescriptor(uint8_t desc) {
 }
 
 static void CIPURSEPrintKeyAttrib(uint8_t *attr) {
-    PrintAndLogEx(INFO, "-------- KEY ATTRIBUTES --------");
-    PrintAndLogEx(INFO, "Additional info: 0x%02x", attr[0]);
-    PrintAndLogEx(INFO, "Key length: %d", attr[1]);
-    PrintAndLogEx(INFO, "Algorithm ID: 0x%02x", attr[2]);
-    PrintAndLogEx(INFO, "Security attr: 0x%02x", attr[3]);
-    PrintAndLogEx(INFO, "KVV: 0x%02x%02x%02x", attr[4], attr[5], attr[6]);
-    PrintAndLogEx(INFO, "-------------------------------");
+    PrintAndLogEx(INFO, "--- " _CYAN_("Key Attributes") "---------------------");
+    PrintAndLogEx(INFO, "Additional info... 0x%02x", attr[0]);
+    PrintAndLogEx(INFO, "Key length........ %d", attr[1]);
+    PrintAndLogEx(INFO, "Algorithm ID...... 0x%02x", attr[2]);
+    PrintAndLogEx(INFO, "Security attr..... 0x%02x", attr[3]);
+    PrintAndLogEx(INFO, "KVV............... 0x%02x%02x%02x", attr[4], attr[5], attr[6]);
+    PrintAndLogEx(NORMAL, "");
 }
 
-void CIPURSEPrintFileAttr(uint8_t *fileAttr, size_t len) {
+void CIPURSEPrintFileAttr(uint8_t *attr, size_t len) {
     if (len < 7) {
-        PrintAndLogEx(ERR, "Attributes length " _RED_("ERROR"));
+        PrintAndLogEx(FAILED, "Attributes length too short");
         return;
     }
 
-    PrintAndLogEx(INFO, "--------- FILE ATTRIBUTES ---------");
-    if (fileAttr[0] == 0x38) {
-        PrintAndLogEx(INFO, "Type: MF, ADF");
-        if (fileAttr[1] == 0x00) {
-            PrintAndLogEx(INFO, "Type: MF");
+    PrintAndLogEx(INFO, "--- " _CYAN_("File Attributes") "---------------------");
+    if (attr[0] == 0x38) {
+        PrintAndLogEx(INFO, "Type... MF, ADF");
+
+        if (attr[1] == 0x00) {
+            PrintAndLogEx(INFO, "Type... MF");
         } else {
-            if ((fileAttr[1] & 0xe0) == 0x00)
-                PrintAndLogEx(INFO, "Type: Unknown");
-            if ((fileAttr[1] & 0xe0) == 0x20)
-                PrintAndLogEx(INFO, "Type: CIPURSE L");
-            if ((fileAttr[1] & 0xe0) == 0x40)
-                PrintAndLogEx(INFO, "Type: CIPURSE S");
-            if ((fileAttr[1] & 0xe0) == 0x60)
-                PrintAndLogEx(INFO, "Type: CIPURSE T");
-            if ((fileAttr[1] & 0x02) == 0x00)
+            if ((attr[1] & 0xe0) == 0x00)
+                PrintAndLogEx(INFO, "Type... Unknown");
+
+            if ((attr[1] & 0xe0) == 0x20)
+                PrintAndLogEx(INFO, "Type... CIPURSE L");
+
+            if ((attr[1] & 0xe0) == 0x40)
+                PrintAndLogEx(INFO, "Type... CIPURSE S");
+
+            if ((attr[1] & 0xe0) == 0x60)
+                PrintAndLogEx(INFO, "Type... CIPURSE T");
+
+            if ((attr[1] & 0x02) == 0x00)
                 PrintAndLogEx(INFO, "Autoselect on PxSE select OFF");
             else
                 PrintAndLogEx(INFO, "Autoselect on PxSE select ON");
-            if ((fileAttr[1] & 0x01) == 0x00)
+
+            if ((attr[1] & 0x01) == 0x00)
                 PrintAndLogEx(INFO, "PxSE select returns FCPTemplate OFF");
             else
                 PrintAndLogEx(INFO, "PxSE select returns FCPTemplate ON");
         }
 
-        PrintAndLogEx(INFO, "File ID: 0x%02x%02x", fileAttr[2], fileAttr[3]);
+        PrintAndLogEx(INFO, "File ID................... 0x%02x%02x", attr[2], attr[3]);
+        PrintAndLogEx(INFO, "Maximum # custom EFs...... %d", attr[4]);
+        PrintAndLogEx(INFO, "Maximum # EFs with SFID... %d", attr[5]);
 
-        PrintAndLogEx(INFO, "Maximum number of custom EFs: %d", fileAttr[4]);
-        PrintAndLogEx(INFO, "Maximum number of EFs with SFID: %d", fileAttr[5]);
-        uint8_t keyNum = fileAttr[6];
-        PrintAndLogEx(INFO, "Keys assigned: %d", keyNum);
+        uint8_t keynum = attr[6];
+        PrintAndLogEx(INFO, "Keys assigned... %d", keynum);
 
         if (len >= 9) {
-            PrintAndLogEx(INFO, "SMR entries: %02x%02x", fileAttr[7], fileAttr[8]);
+            PrintAndLogEx(INFO, "SMR entries... %02x%02x", attr[7], attr[8]);
         }
 
-        if (len >= 10 + keyNum + 1) {
-            PrintAndLogEx(INFO, "ART: %s", sprint_hex(&fileAttr[9], keyNum + 1));
+        if (len >= 10 + keynum + 1) {
+            PrintAndLogEx(INFO, "ART... %s", sprint_hex(&attr[9], keynum + 1));
         }
 
-        if (len >= 11 + keyNum + 1 + keyNum * 7) {
-            for (int i = 0; i < keyNum; i++) {
-                PrintAndLogEx(INFO, "Key %d Attributes: %s", i, sprint_hex(&fileAttr[11 + keyNum + 1 + i * 7], 7));
-                CIPURSEPrintKeyAttrib(&fileAttr[11 + keyNum + 1 + i * 7]);
+        if (len >= 11 + keynum + 1 + keynum * 7) {
+            for (int i = 0; i < keynum; i++) {
+                PrintAndLogEx(INFO, "Key %d Attributes... %s", i, sprint_hex(&attr[11 + keynum + 1 + i * 7], 7));
+                CIPURSEPrintKeyAttrib(&attr[11 + keynum + 1 + i * 7]);
             }
         }
         // MF
-        if (fileAttr[1] == 0x00) {
-            PrintAndLogEx(INFO, "Total memory size: %d", (fileAttr[len - 6] << 16) + (fileAttr[len - 1] << 5) + fileAttr[len - 4]);
-            PrintAndLogEx(INFO, "Free memory size: %d", (fileAttr[len - 3] << 16) + (fileAttr[len - 2] << 8) + fileAttr[len - 1]);
+        if (attr[1] == 0x00) {
+            PrintAndLogEx(INFO, "Total memory size... %d", (attr[len - 6] << 16) + (attr[len - 1] << 5) + attr[len - 4]);
+            PrintAndLogEx(INFO, "Free memory size.... %d", (attr[len - 3] << 16) + (attr[len - 2] << 8) + attr[len - 1]);
 
         } else {
-            int ptr = 11 + keyNum + 1 + keyNum * 7;
-            if (len > ptr)
-                PrintAndLogEx(INFO, "TLV file control: %s", sprint_hex(&fileAttr[ptr], len - ptr));
+            int ptr = 11 + keynum + 1 + keynum * 7;
+            if (len > ptr) {
+                PrintAndLogEx(INFO, "TLV file control... %s", sprint_hex(&attr[ptr], len - ptr));
+            }
         }
     } else {
-        PrintAndLogEx(INFO, "Type: EF");
-        CIPURSEPrintFileDescriptor(fileAttr[0]);
-        if (fileAttr[1] == 0)
-            PrintAndLogEx(INFO, "SFI: not assigned");
+        PrintAndLogEx(INFO, "Type... EF");
+        CIPURSEPrintFileDescriptor(attr[0]);
+
+        if (attr[1] == 0)
+            PrintAndLogEx(INFO, "SFI.... not assigned");
         else
-            PrintAndLogEx(INFO, "SFI: 0x%02x", fileAttr[1]);
+            PrintAndLogEx(INFO, "SFI.... 0x%02x", attr[1]);
 
-        PrintAndLogEx(INFO, "File ID: 0x%02x%02x", fileAttr[2], fileAttr[3]);
+        PrintAndLogEx(INFO, "File ID... 0x%02x%02x", attr[2], attr[3]);
 
-        if (fileAttr[0] == 0x01 || fileAttr[0] == 0x11)
-            PrintAndLogEx(INFO, "File size: %d", (fileAttr[4] << 8) + fileAttr[5]);
+        if (attr[0] == 0x01 || attr[0] == 0x11)
+            PrintAndLogEx(INFO, "File size... %d", (attr[4] << 8) + attr[5]);
         else
-            PrintAndLogEx(INFO, "Record num: %d record size: %d", fileAttr[4], fileAttr[5]);
+            PrintAndLogEx(INFO, "Record num " _YELLOW_("%d") " record size " _YELLOW_("%d"), attr[4], attr[5]);
 
-        PrintAndLogEx(INFO, "Keys assigned: %d", fileAttr[6]);
+        PrintAndLogEx(INFO, "Keys assigned... %d", attr[6]);
 
         if (len >= 9) {
-            PrintAndLogEx(INFO, "SMR entries: %02x%02x", fileAttr[7], fileAttr[8]);
+            PrintAndLogEx(INFO, "SMR entries... %02x%02x", attr[7], attr[8]);
         }
 
         if (len >= 10) {
-            PrintAndLogEx(INFO, "ART: %s", sprint_hex(&fileAttr[9], len - 9));
-            if (fileAttr[6] + 1 != len - 9)
+            PrintAndLogEx(INFO, "ART... %s", sprint_hex(&attr[9], len - 9));
+
+            if (attr[6] + 1 != len - 9) {
                 PrintAndLogEx(WARNING, "ART length is wrong");
+            }
         }
 
     }
diff --git a/client/src/cipurse/cipursecore.h b/client/src/cipurse/cipursecore.h
index 7ace29ea5..e16a931c2 100644
--- a/client/src/cipurse/cipursecore.h
+++ b/client/src/cipurse/cipursecore.h
@@ -11,10 +11,10 @@
 #ifndef __CIPURSECORE_H__
 #define __CIPURSECORE_H__
 
-#include "common.h"
-
 #include <jansson.h>
-#include "iso7816/apduinfo.h" // sAPDU
+#include <stdbool.h>
+#include "common.h"
+#include "iso7816/apduinfo.h"       // sAPDU
 #include "cipurse/cipursecrypto.h"
 
 
@@ -22,23 +22,23 @@
 
 void CIPURSEPrintInfoFile(uint8_t *data, size_t len);
 
-int CIPURSESelect(bool ActivateField, bool LeaveFieldON, uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw);
+int CIPURSESelect(bool activate_field, bool leave_field_on, uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw);
 
-int CIPURSEChallenge(uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw);
-int CIPURSEMutalAuthenticate(uint8_t keyIndex, uint8_t *params, uint8_t paramslen, uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw);
+int CIPURSEChallenge(uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw);
+int CIPURSEMutalAuthenticate(uint8_t keyIndex, uint8_t *params, uint8_t paramslen, uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw);
 
-int CIPURSECreateFile(uint8_t *attr, uint16_t attrlen, uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw);
-int CIPURSEDeleteFile(uint16_t fileID, uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw);
+int CIPURSECreateFile(uint8_t *attr, uint16_t attrlen, uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw);
+int CIPURSEDeleteFile(uint16_t fileid, uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw);
 
-int CIPURSESelectMFFile(uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw) ;
-int CIPURSESelectFile(uint16_t fileID, uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw);
-int CIPURSEReadFileAttributes(uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw);
-int CIPURSEReadBinary(uint16_t offset, uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw);
-int CIPURSEUpdateBinary(uint16_t offset, uint8_t *data, uint16_t datalen, uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw);
+int CIPURSESelectMFFile(uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw) ;
+int CIPURSESelectFile(uint16_t fileid, uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw);
+int CIPURSEReadFileAttributes(uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw);
+int CIPURSEReadBinary(uint16_t offset, uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw);
+int CIPURSEUpdateBinary(uint16_t offset, uint8_t *data, uint16_t datalen, uint8_t *result, size_t max_result_len, size_t *result_len, uint16_t *sw);
 
-bool CIPURSEChannelAuthenticate(uint8_t keyIndex, uint8_t *key, bool verbose);
+bool CIPURSEChannelAuthenticate(uint8_t keyindex, uint8_t *key, bool verbose);
 void CIPURSECSetActChannelSecurityLevels(CipurseChannelSecurityLevel req, CipurseChannelSecurityLevel resp);
 
-void CIPURSEPrintFileAttr(uint8_t *fileAttr, size_t len);
+void CIPURSEPrintFileAttr(uint8_t *attr, size_t len);
 
 #endif /* __CIPURSECORE_H__ */
