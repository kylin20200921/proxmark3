commit 86118525e5fd708c27ada5f401567b215ffc2808
Author: iceman1001 <iceman@iuse.se>
Date:   Sat May 22 14:22:41 2021 +0200

    style

diff --git a/client/src/cmdlfidteck.c b/client/src/cmdlfidteck.c
index ca188bec0..d62d07093 100644
--- a/client/src/cmdlfidteck.c
+++ b/client/src/cmdlfidteck.c
@@ -284,13 +284,16 @@ int detectIdteck(uint8_t *dest, size_t *size) {
     if (getSignalProperties()->isnoise) return -2;
 
     size_t start_idx = 0;
+    //                    4           9           4           4           5           4           4           B
     uint8_t preamble[] = {0, 1, 0, 0, 1, 0, 0, 1, 0, 1, 0, 0, 0, 1, 0, 0, 0, 1, 0, 1, 0, 1, 0, 0, 0, 1, 0, 0, 1, 0, 1, 1};
 
     //preamble not found
-    if (!preambleSearch(dest, preamble, sizeof(preamble), size, &start_idx))
+    if (preambleSearch(dest, preamble, sizeof(preamble), size, &start_idx) == false)
         return -3;
 
     // wrong demoded size
-    if (*size != 64) return -4;
+    if (*size != 64) {
+        return -4;
+    }
     return (int)start_idx;
 }
