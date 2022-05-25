commit e6cc0ed83644a085204d11c6d0d6ce7e1fc83596
Author: iceman1001 <iceman@iuse.se>
Date:   Wed Nov 24 20:23:34 2021 +0100

    nfc decode - detects and prints application/json

diff --git a/client/src/nfc/ndef.c b/client/src/nfc/ndef.c
index 7e698eb80..7e07639ad 100644
--- a/client/src/nfc/ndef.c
+++ b/client/src/nfc/ndef.c
@@ -25,9 +25,11 @@
 
 #define NDEF_WIFIAPPL   "application/vnd.wfa"
 #define NDEF_BLUEAPPL   "application/vnd.bluetooth"
+#define NDEF_JSONAPPL   "application/json"
 #define NDEF_VCARDTEXT  "text/vcard"
 #define NDEF_XVCARDTEXT "text/x-vcard"
 
+
 static const char *TypeNameFormat_s[] = {
     "Empty Record",
     "Well Known Record",
@@ -517,6 +519,14 @@ static int ndefDecodeMime_vcard(NDEFHeader_t *ndef) {
     }
     return PM3_SUCCESS;
 }
+static int ndefDecodeMime_json(NDEFHeader_t *ndef) {
+    PrintAndLogEx(INFO, _CYAN_("JSON details"));
+    if (ndef->PayloadLen > 1) {
+        PrintAndLogEx(INFO, "");
+        PrintAndLogEx(INFO, _GREEN_("%.*s"), (int)ndef->PayloadLen, ndef->Payload);
+    }
+    return PM3_SUCCESS;
+}
 
 static int ndefDecodeMime_bt(NDEFHeader_t *ndef) {
     PrintAndLogEx(INFO, "Type............ " _YELLOW_("%.*s"), (int)ndef->TypeLen, ndef->Type);
@@ -651,6 +661,9 @@ static int ndefDecodePayload(NDEFHeader_t *ndef) {
             if (str_startswith(begin, NDEF_BLUEAPPL)) {
                 ndefDecodeMime_bt(ndef);
             }
+            if (str_startswith(begin, NDEF_JSONAPPL)) {
+                ndefDecodeMime_json(ndef);
+            }
 
             free(begin);
             begin = NULL;
@@ -673,6 +686,7 @@ static int ndefDecodePayload(NDEFHeader_t *ndef) {
             PrintAndLogEx(INFO, "- decoder to be impl -");
             break;
     }
+    PrintAndLogEx(INFO, "");
     return PM3_SUCCESS;
 }
 
