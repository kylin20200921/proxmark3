commit 5b8ddfc47ea2c374e8e1f2c5175e1b1dc6fff794
Author: iceman1001 <iceman@iuse.se>
Date:   Tue Jun 22 07:53:43 2021 +0200

    little vs big endian,  forgot one

diff --git a/armsrc/em4x50.c b/armsrc/em4x50.c
index 4ed10bd1e..d775f02cc 100644
--- a/armsrc/em4x50.c
+++ b/armsrc/em4x50.c
@@ -1313,7 +1313,7 @@ static bool em4x50_sim_read_word(uint32_t *word) {
         }
     }
 
-    *word = BYTES2UINT32(bytes);
+    *word = BYTES2UINT32_BE(bytes);
 
     // check parities
     if ((parities == parities_calculated) && (stop_bit == 0)) {
