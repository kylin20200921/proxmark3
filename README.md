commit 8fa51075fb39885aae87a84fac76e0d7cc41edb1
Author: iceman1001 <iceman@iuse.se>
Date:   Sat May 15 15:33:11 2021 +0200

    fix cppchecker unsigned warning

diff --git a/client/src/cmdlfem4x05.c b/client/src/cmdlfem4x05.c
index f43e163bb..1a2fc8255 100644
--- a/client/src/cmdlfem4x05.c
+++ b/client/src/cmdlfem4x05.c
@@ -1852,14 +1852,11 @@ int CmdEM4x05Unlock(const char *Cmd) {
         PrintAndLogEx(INFO, "Old protection word => " _YELLOW_("%08X"), search_value);
         char bitstring[9] = {0};
         for (int i = 0; i < 8; i++) {
-            bitstring[i] = (bitflips & (0xF << ((7 - i) * 4))) ? 'x' : '.';
+            bitstring[i] = (bitflips & (0xFU << ((7 - i) * 4))) ? 'x' : '.';
         }
         // compute number of bits flipped
-
         PrintAndLogEx(INFO, "Bitflips: %2u events => %s", bitcount32(bitflips), bitstring);
         PrintAndLogEx(INFO, "New protection word => " _CYAN_("%08X") "\n", word14b);
-
-
         PrintAndLogEx(INFO, "Try " _YELLOW_("`lf em 4x05_dump`"));
     }
 
