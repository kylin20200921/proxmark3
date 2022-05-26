commit 099abfedcf2d3c2492119b3ff8fcc247820f6c73
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Feb 6 05:28:59 2022 +0100

    reworked it to fit style

diff --git a/client/src/cmdhfmfu.c b/client/src/cmdhfmfu.c
index 7e94619f4..3a19c4ade 100644
--- a/client/src/cmdhfmfu.c
+++ b/client/src/cmdhfmfu.c
@@ -125,29 +125,37 @@ static char *getProductTypeStr(uint8_t id) {
 
 static int ul_print_nxp_silicon_info(uint8_t *card_uid) {
 
-    uint8_t uid[7];
-    memcpy(&uid, card_uid, 7);
-
-    if (uid[0] != 0x04) {
+    if (card_uid[0] != 0x04) {
         return PM3_SUCCESS;
     }
 
-    uint8_t maskedBit1 = uid[6] & -1;
-    uint8_t maskedBit2 = uid[3] & -1;
-    unsigned long waferCoordX = ((maskedBit1 & 3) << 8) | (uid[1] & -1);
-    unsigned long waferCoordY = ((maskedBit1 & 12) << 6) | (uid[2] & -1);
-    unsigned long waferCounter = ((uid[4] & -1) << 5) | ((maskedBit1 & 240) << 17) | ((uid[5] & -1) << 13) | (maskedBit2 >> 3);
-    unsigned long testSite = maskedBit2 & 7;
+    uint8_t uid[7];
+    memcpy(&uid, card_uid, 7);
+
+    uint16_t waferCoordX = ((uid[6] & 3) << 8) | uid[1];
+    uint16_t waferCoordY = ((uid[6] & 12) << 6) | uid[2];
+    uint32_t waferCounter = (
+                (uid[4] << 5) |
+                ((uid[6] & 0xF0) << 17) |
+                (uid[5] << 13) |
+                (uid[3] >> 3)
+            );
+    uint8_t testSite = uid[3] & 7;
 
     PrintAndLogEx(NORMAL, "");
     PrintAndLogEx(INFO, "--- " _CYAN_("Tag Silicon Information"));
-    PrintAndLogEx(INFO, "       Wafer Counter: %ld (0x%02lX)", waferCounter, waferCounter);
-    PrintAndLogEx(INFO, "   Wafer Coordinates: [%ld, %ld] (0x%02lX, 0x%02lX)", waferCoordX, waferCoordY, waferCoordX, waferCoordY);
-    PrintAndLogEx(INFO, "           Test Site: %ld", testSite);
-
+    PrintAndLogEx(INFO, "       Wafer Counter: %" PRId32 " ( 0x%02" PRIX32 " )", waferCounter, waferCounter);
+    PrintAndLogEx(INFO, "   Wafer Coordinates: x %" PRId16 ", y %" PRId16 " (0x%02" PRIX16 ", 0x%02" PRIX16 ")"
+            , waferCoordX
+            , waferCoordY
+            , waferCoordX
+            , waferCoordY
+        );
+    PrintAndLogEx(INFO, "           Test Site: %u", testSite);
     return PM3_SUCCESS;
 }
 
+
 /*
   The 7 MSBits (=n) code the storage size itself based on 2^n,
   the LSBit is set to '0' if the size is exactly 2^n
@@ -481,16 +489,16 @@ static int ul_print_default(uint8_t *data, uint8_t *real_uid) {
         // CT (cascade tag byte) 0x88 xor SN0 xor SN1 xor SN2
         int crc0 = 0x88 ^ uid[0] ^ uid[1] ^ uid[2];
         if (data[3] == crc0)
-            PrintAndLogEx(SUCCESS, "      BCC0: %02X (" _GREEN_("ok") ")", data[3]);
+            PrintAndLogEx(SUCCESS, "      BCC0: %02X ( " _GREEN_("ok") " )", data[3]);
         else
             PrintAndLogEx(NORMAL, "      BCC0: %02X, crc should be %02X", data[3], crc0);
 
         int crc1 = uid[3] ^ uid[4] ^ uid[5] ^ uid[6];
         if (data[8] == crc1)
-            PrintAndLogEx(SUCCESS, "      BCC1: %02X (" _GREEN_("ok") ")", data[8]);
+            PrintAndLogEx(SUCCESS, "      BCC1: %02X ( " _GREEN_("ok") " )", data[8]);
         else
             PrintAndLogEx(NORMAL, "      BCC1: %02X, crc should be %02X", data[8], crc1);
-        PrintAndLogEx(SUCCESS, "  Internal: %02X (%s)", data[9], (data[9] == 0x48) ? _GREEN_("default") : _RED_("not default"));
+        PrintAndLogEx(SUCCESS, "  Internal: %02X ( %s )", data[9], (data[9] == 0x48) ? _GREEN_("default") : _RED_("not default"));
     } else {
         PrintAndLogEx(SUCCESS, "Blocks 0-2: %s", sprint_hex(data + 0, 12));
     }
@@ -604,10 +612,10 @@ static int ndef_print_CC(uint8_t *data) {
     PrintAndLogEx(SUCCESS, "  Additional feature information");
     PrintAndLogEx(SUCCESS, "  %02X", data[3]);
     PrintAndLogEx(SUCCESS, "  00000000");
-    PrintAndLogEx(SUCCESS, "  xxx      - %02X: RFU (%s)", msb3, (msb3 == 0) ? _GREEN_("ok") : _RED_("fail"));
+    PrintAndLogEx(SUCCESS, "  xxx      - %02X: RFU ( %s )", msb3, (msb3 == 0) ? _GREEN_("ok") : _RED_("fail"));
     PrintAndLogEx(SUCCESS, "     x     - %02X: %s special frame", sf, (sf) ? "support" : "don\'t support");
     PrintAndLogEx(SUCCESS, "      x    - %02X: %s lock block", lb, (lb) ? "support" : "don\'t support");
-    PrintAndLogEx(SUCCESS, "       xx  - %02X: RFU (%s)", mlrule, (mlrule == 0) ? _GREEN_("ok") : _RED_("fail"));
+    PrintAndLogEx(SUCCESS, "       xx  - %02X: RFU ( %s )", mlrule, (mlrule == 0) ? _GREEN_("ok") : _RED_("fail"));
     PrintAndLogEx(SUCCESS, "         x - %02X: IC %s multiple block reads", mbread, (mbread) ? "support" : "don\'t support");
     return PM3_SUCCESS;
 }
