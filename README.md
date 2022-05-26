commit 1916fa31b5ac341743e0077c5d8385ba2ffb9fb2
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Mar 12 20:05:27 2022 +0100

    build error

diff --git a/client/src/cmddata.c b/client/src/cmddata.c
index 77d90fc0b..0afb4a557 100644
--- a/client/src/cmddata.c
+++ b/client/src/cmddata.c
@@ -2958,7 +2958,7 @@ static int CmdDiff(const char *Cmd) {
     }
 
     int res = PM3_SUCCESS;
-    uint8_t *inA, *inB;
+    uint8_t *inA = NULL, *inB = NULL;
     size_t datalenA = 0, datalenB = 0;
     // read file A
     if (fnlenA) {
