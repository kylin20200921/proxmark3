commit c78459a572002d0d26b94420ec75a8499e9e21f5
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Apr 30 21:05:20 2022 +0200

    move detection outside SAK 02 check

diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index 30cc86e82..26e0d5d22 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -1597,20 +1597,6 @@ static int detect_nxp_card(uint8_t sak, uint16_t atqa, uint64_t select_status) {
         } else if ((sak & 0x04) == 0x04) {
             printTag("Any MIFARE CL1");
             type |= MTDESFIRE;
-        } else if ((sak & 0x0A) == 0x0A) {
-
-            if ((atqa & 0x0003) == 0x0003) {
-                // Uses Shanghai algo
-                printTag("FM11RF005SH (FUDAN Shanghai Metro)");
-                type |= MTFUDAN;
-            } else if ((atqa & 0x0005) == 0x0005) {
-                printTag("FM11RF005M (FUDAN MIFARE Classic clone)");
-                type |= MTFUDAN;
-            }
-        } 
-        else if ((sak & 0x53) == 0x53) {
-            printTag("FM11RF08SH (FUDAN)");
-            type |= MTFUDAN;
         } else {
             printTag("MIFARE Ultralight");
             printTag("MIFARE Ultralight C");
@@ -1620,6 +1606,19 @@ static int detect_nxp_card(uint8_t sak, uint16_t atqa, uint64_t select_status) {
             printTag("NTAG 2xx");
             type |= MTULTRALIGHT;
         }
+    } else if ((sak & 0x0A) == 0x0A) {
+
+        if ((atqa & 0x0003) == 0x0003) {
+            // Uses Shanghai algo
+            printTag("FM11RF005SH (FUDAN Shanghai Metro)");
+            type |= MTFUDAN;
+        } else if ((atqa & 0x0005) == 0x0005) {
+            printTag("FM11RF005M (FUDAN MIFARE Classic clone)");
+            type |= MTFUDAN;
+        }
+    } else if ((sak & 0x53) == 0x53) {
+        printTag("FM11RF08SH (FUDAN)");
+        type |= MTFUDAN;
     }
 
     if (type == MTNONE) {
