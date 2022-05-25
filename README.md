commit 224e2f68b21947e1fd3e4468233587b246a38c0f
Author: iceman1001 <iceman@iuse.se>
Date:   Tue Jan 4 07:36:31 2022 +0100

    cppcheck fix

diff --git a/armsrc/Standalone/lf_nexid.c b/armsrc/Standalone/lf_nexid.c
index c921fb2bd..f255a79c9 100644
--- a/armsrc/Standalone/lf_nexid.c
+++ b/armsrc/Standalone/lf_nexid.c
@@ -109,7 +109,7 @@ static uint8_t nexwatch_checksum(uint8_t magic, uint32_t id, uint8_t parity) {
 static int nexwatch_scamble(NexWatchScramble_t action, uint32_t *id, uint32_t *scambled) {
 
     // 255 = Not used/Unknown other values are the bit offset in the ID/FC values
-    uint8_t hex_2_id [] = {
+    const uint8_t hex_2_id [] = {
         31, 27, 23, 19, 15, 11, 7, 3,
         30, 26, 22, 18, 14, 10, 6, 2,
         29, 25, 21, 17, 13, 9, 5, 1,
