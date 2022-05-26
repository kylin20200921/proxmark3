commit 65b9a9fb769541f5d3e255ccf2c17d1cb77ac126
Author: iceman1001 <iceman@iuse.se>
Date:   Sat May 21 09:28:38 2022 +0200

    widen mfp detection to include 0004

diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index 26e0d5d22..0a9f3d22c 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -1579,9 +1579,6 @@ static int detect_nxp_card(uint8_t sak, uint16_t atqa, uint64_t select_status) {
                     if ((atqa & 0x0001) == 0x0001) {
                         printTag("HID SEOS (smartmx / javacard)");
                         type |= HID_SEOS;
-                    } else if ((atqa & 0x0004) == 0x0004) {
-                        printTag("EMV");
-                        type |= MTEMV;
                     } else {
                         printTag("MIFARE Plus EV1 2K/4K in SL3");
                         printTag("MIFARE Plus S 2K/4K in SL3");
@@ -1589,6 +1586,11 @@ static int detect_nxp_card(uint8_t sak, uint16_t atqa, uint64_t select_status) {
                         printTag("MIFARE Plus SE 1K");
                         type |= MTPLUS;
                     }
+
+                    if ((atqa & 0x0004) == 0x0004) {
+                        printTag("EMV");
+                        type |= MTEMV;
+                    }
                 }
 
                 printTag("NTAG 4xx");
