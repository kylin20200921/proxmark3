commit 5ddfedbad020fc4e8aad59876a2d60220cb67631
Author: iceman1001 <iceman@iuse.se>
Date:   Tue Jan 4 07:32:58 2022 +0100

    cppcheck fix const

diff --git a/client/src/cmdlfnexwatch.c b/client/src/cmdlfnexwatch.c
index 3bc05db7b..3b02b798e 100644
--- a/client/src/cmdlfnexwatch.c
+++ b/client/src/cmdlfnexwatch.c
@@ -72,7 +72,7 @@ static uint8_t nexwatch_checksum(uint8_t magic, uint32_t id, uint8_t parity) {
 static int nexwatch_scamble(NexWatchScramble_t action, uint32_t *id, uint32_t *scambled) {
 
     // 255 = Not used/Unknown other values are the bit offset in the ID/FC values
-    uint8_t hex_2_id [] = {
+    const uint8_t hex_2_id [] = {
         31, 27, 23, 19, 15, 11, 7, 3,
         30, 26, 22, 18, 14, 10, 6, 2,
         29, 25, 21, 17, 13, 9, 5, 1,
