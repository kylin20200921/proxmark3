commit b8a04d6d69bf1c46d5f04b9ff5e54a627ffca957
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Sep 3 21:12:02 2021 +0200

    text

diff --git a/client/src/cmdlfnedap.c b/client/src/cmdlfnedap.c
index 98a6d2d31..41f9f3bbf 100644
--- a/client/src/cmdlfnedap.c
+++ b/client/src/cmdlfnedap.c
@@ -459,7 +459,7 @@ static int CmdLFNedapClone(const char *Cmd) {
 
     if (res == PM3_SUCCESS) {
         PrintAndLogEx(INFO, "The block 0 was changed (eXtended) which can be hard to detect.");
-        PrintAndLogEx(INFO,  "Configure it manually " _YELLOW_("`lf t55xx config -b 64 --BI -i -o 32`"));
+        PrintAndLogEx(INFO,  "Configure it manually " _YELLOW_("`lf t55xx config --rate 64 --BI -i -o 32`"));
     } else {
         PrintAndLogEx(NORMAL, "");
     }
