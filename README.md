commit dc3944ab65fa2c909f5c21f3b7028f000e6aae40
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Apr 30 20:48:45 2022 +0200

    wrong crc

diff --git a/armsrc/iso14443a.c b/armsrc/iso14443a.c
index 040bb3904..6fc04a4cd 100644
--- a/armsrc/iso14443a.c
+++ b/armsrc/iso14443a.c
@@ -2526,8 +2526,8 @@ int iso14443a_select_cardEx(uint8_t *uid_ptr, iso14a_card_select_t *p_card, uint
 
         if ((p_card->atqa[0]==0x03) || (p_card->atqa[0]==0x05)) {
 
-            // Read real UID
-            uint8_t fudan_read[] = { 0x30, 0x01, 0x02, 0xA8};
+            // Read real UID 
+            uint8_t fudan_read[] = { 0x30, 0x01, 0x8B, 0xB9};
             ReaderTransmit(fudan_read, sizeof(fudan_read), NULL); 
             ReaderReceive(resp, resp_par);
 
