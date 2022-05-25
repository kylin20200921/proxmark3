commit b5e7b6277f124446cad4414d1ad8a1c9ec545376
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jun 20 08:08:41 2021 +0200

    fix coverity CID 349310

diff --git a/client/src/nfc/ndef.c b/client/src/nfc/ndef.c
index 2b2dfc9c3..43f0b085d 100644
--- a/client/src/nfc/ndef.c
+++ b/client/src/nfc/ndef.c
@@ -549,9 +549,9 @@ static int ndefDecodeMime_bt(NDEFHeader_t *ndef) {
         }  
         // Let's check payload[9]. If 0x08 then SHORT_NAME or if 0x09 then COMPLETE_NAME  
         if (ndef->Payload[9] == 0x08 ) {  
-            PrintAndLogEx(INFO, "Short name...... " _YELLOW_("%s"), ndef->Payload + 10, ndef->PayloadLen - 10);
+            PrintAndLogEx(INFO, "Short name...... " _YELLOW_("%.*s"), ndef->PayloadLen - 10, ndef->Payload + 10);
         } else if (ndef->Payload[9] == 0x09 ) {  
-            PrintAndLogEx(INFO, "Complete name... " _YELLOW_("%s"),   ndef->Payload + 10, ndef->PayloadLen - 10);
+            PrintAndLogEx(INFO, "Complete name... " _YELLOW_("%.*s"), ndef->PayloadLen - 10, ndef->Payload + 10);
         } else {  
             PrintAndLogEx(INFO, "[ %02x ]", ndef->Payload[9]);
         }
