commit a37fdb1ccf57f1b129c214be999c3d48b7d77dfa
Author: iceman1001 <iceman@iuse.se>
Date:   Wed Jan 26 06:51:34 2022 +0100

    added getversion response for 14a -t 3,  (simulation of desfire anticollision)

diff --git a/armsrc/iso14443a.c b/armsrc/iso14443a.c
index bb0f56693..17eb156f6 100644
--- a/armsrc/iso14443a.c
+++ b/armsrc/iso14443a.c
@@ -1610,6 +1610,8 @@ void SimulateIso14443aTag(uint8_t tagType, uint16_t flags, uint8_t *data, uint8_
             order = ORDER_HALTED;
         } else if (receivedCmd[0] == MIFARE_ULEV1_VERSION && len == 3 && (tagType == 2 || tagType == 7)) {
             p_response = &responses[RESP_INDEX_VERSION];
+        } else if (receivedCmd[0] == MFDES_GET_VERSION && len == 4 && (tagType == 3)) {
+            p_response = &responses[RESP_INDEX_VERSION];
         } else if ((receivedCmd[0] == MIFARE_AUTH_KEYA || receivedCmd[0] == MIFARE_AUTH_KEYB) && len == 4 && tagType != 2 && tagType != 7) {    // Received an authentication request
             cardAUTHKEY = receivedCmd[0] - 0x60;
             cardAUTHSC = receivedCmd[1] / 4; // received block num
