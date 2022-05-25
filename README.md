commit 89becb8d9f13b463129728a0dc511dfcb720a835
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 1 17:43:12 2022 +0100

    cppcheck

diff --git a/client/src/cmdlfvisa2000.c b/client/src/cmdlfvisa2000.c
index 07bdcff30..2a829f2af 100644
--- a/client/src/cmdlfvisa2000.c
+++ b/client/src/cmdlfvisa2000.c
@@ -44,12 +44,11 @@ static uint8_t visa_chksum(uint32_t id) {
 
 static uint8_t visa_parity(uint32_t id) {
     // 4bit parity LUT
-    uint8_t par_lut[] = {
-        0, 1, 1, 0
-        , 1, 0, 0, 1
-        , 1, 0, 0, 1
-        , 0, 1, 1, 0
+    const uint8_t par_lut[] = {
+        0, 1, 1, 0, 1, 0, 0, 1,
+        1, 0, 0, 1, 0, 1, 1, 0
     };
+
     uint8_t par = 0;
     par |= par_lut[(id >> 28) & 0xF ] << 7;
     par |= par_lut[(id >> 24) & 0xF ] << 6;
