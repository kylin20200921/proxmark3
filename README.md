commit ef7c30121715a4123101d8239458748e525946d9
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Dec 12 22:34:06 2021 +0100

    cppcheck - missing return statement

diff --git a/armsrc/hitag2crack.c b/armsrc/hitag2crack.c
index 640e897a4..b6db19741 100644
--- a/armsrc/hitag2crack.c
+++ b/armsrc/hitag2crack.c
@@ -731,6 +731,8 @@ bool hitag2crack_consume_keystream(uint8_t *keybits, int kslen, int *ksoffset, u
 
     // update ksoffset with command length and response
     *ksoffset += (numcmds * 10) + 32;
+
+    return true;
 }
 
 // hitag2crack_extend_keystream sends an extended command to retrieve more keybits.
