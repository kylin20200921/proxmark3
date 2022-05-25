commit 2f432788cfe6f0ebc39a6dd6442320286d2f0108
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jun 20 07:49:41 2021 +0200

    fix out-of-bounds access

diff --git a/client/src/cmdhfmfdes.c b/client/src/cmdhfmfdes.c
index e2e315727..bec41af0b 100644
--- a/client/src/cmdhfmfdes.c
+++ b/client/src/cmdhfmfdes.c
@@ -2445,7 +2445,7 @@ static int CmdHF14ADesGetUID(const char *Cmd) {
     uint8_t uidlen = 16;
 
     // Get datalen <uid len> + <crclen> by removing padding.
-    while ((uidlen > 0) && (uid[uidlen] == 0x00))
+    while ((uidlen > 0) && (uid[uidlen - 1] == 0x00))
         uidlen--;
 
     if (tag->authentication_scheme == AS_LEGACY)
@@ -2455,10 +2455,9 @@ static int CmdHF14ADesGetUID(const char *Cmd) {
 
     if (uidlen <= 4) // < incase we trimmed a CRC 00 or more
         uidlen = 4;
-    else    
+    else
         uidlen = 7;
 
-//    PrintAndLogEx(SUCCESS, "    UID: " _GREEN_("%s"), sprint_hex(uid, sizeof(uid)));
     PrintAndLogEx(SUCCESS, "    UID: " _GREEN_("%s"), sprint_hex(uid, uidlen));
     return res;
 }
