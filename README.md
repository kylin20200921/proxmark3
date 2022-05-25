commit a7f3a42aff1a65d8a101a31d5d26e3818bec169e
Author: iceman1001 <iceman@iuse.se>
Date:   Tue Jan 4 07:32:21 2022 +0100

    cppcheck fix const

diff --git a/client/src/cmdlfkeri.c b/client/src/cmdlfkeri.c
index e4c16fe2a..ec57d5bf8 100644
--- a/client/src/cmdlfkeri.c
+++ b/client/src/cmdlfkeri.c
@@ -29,11 +29,11 @@ typedef enum  {Scramble = 0, Descramble = 1} KeriMSScramble_t;
 
 static int CmdKeriMSScramble(KeriMSScramble_t Action, uint32_t *FC, uint32_t *ID, uint32_t *CardID) {
     // 255 = Not used/Unknown other values are the bit offset in the ID/FC values
-    uint8_t CardToID [] = { 255, 255, 255, 255, 13, 12, 20,  5, 16,  6, 21, 17,  8, 255,  0,  7,
+    const uint8_t CardToID [] = { 255, 255, 255, 255, 13, 12, 20,  5, 16,  6, 21, 17,  8, 255,  0,  7,
                             10, 15, 255, 11,  4,  1, 255, 18, 255, 19,  2, 14,  3,  9, 255, 255
                           };
 
-    uint8_t CardToFC [] = { 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255,  0, 255, 255,
+    const uint8_t CardToFC [] = { 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255, 255,  0, 255, 255,
                             255, 255,  2, 255, 255, 255,  3, 255,  4, 255, 255, 255, 255, 255,  1, 255
                           };
 
