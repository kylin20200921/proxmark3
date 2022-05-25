commit 7bbca123354c189758c79fa69a23d1d4a9bec2dc
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 22 16:52:32 2022 +0100

    desfire atqa should be 0x44

diff --git a/armsrc/iso14443a.c b/armsrc/iso14443a.c
index eff1d5b20..ab69c41fc 100644
--- a/armsrc/iso14443a.c
+++ b/armsrc/iso14443a.c
@@ -1049,7 +1049,7 @@ bool SimulateIso14443aInit(int tagType, int flags, uint8_t *data, tag_response_i
         }
         break;
         case 3: { // MIFARE DESFire
-            rATQA[0] = 0x04;
+            rATQA[0] = 0x44;
             rATQA[1] = 0x03;
             sak = 0x20;
             memcpy(rRATS, "\x06\x75\x77\x81\x02\x80\x00\x00", 8);
