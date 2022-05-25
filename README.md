commit 6f2cb98ae498830ae63879d4dc43aa5fa8da80cf
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jun 20 11:17:23 2021 +0200

    fix coverity CID 349310

diff --git a/client/src/nfc/ndef.c b/client/src/nfc/ndef.c
index 5ecddc09f..2bc841a23 100644
--- a/client/src/nfc/ndef.c
+++ b/client/src/nfc/ndef.c
@@ -548,9 +548,9 @@ static int ndefDecodeMime_bt(NDEFHeader_t *ndef) {
         }  
         // Let's check payload[9]. If 0x08 then SHORT_NAME or if 0x09 then COMPLETE_NAME  
         if (ndef->Payload[9] == 0x08 ) {  
-            PrintAndLogEx(INFO, "Short name...... " _YELLOW_("%.*s"), ndef->PayloadLen - 10, ndef->Payload + 10);
+            PrintAndLogEx(INFO, "Short name...... " _YELLOW_("%.*s"), (int)(ndef->PayloadLen - 10), ndef->Payload + 10);
         } else if (ndef->Payload[9] == 0x09 ) {  
-            PrintAndLogEx(INFO, "Complete name... " _YELLOW_("%.*s"), ndef->PayloadLen - 10, ndef->Payload + 10);
+            PrintAndLogEx(INFO, "Complete name... " _YELLOW_("%.*s"), (int)(ndef->PayloadLen - 10), ndef->Payload + 10);
         } else {  
             PrintAndLogEx(INFO, "[ %02x ]", ndef->Payload[9]);
         }
