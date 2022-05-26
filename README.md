commit a7d099bc2b2012ba3a074e8dce2d348bde9a9421
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Mar 13 03:08:46 2022 +0100

    remove debug statement

diff --git a/client/src/cmddata.c b/client/src/cmddata.c
index 3df4d3193..2479752e4 100644
--- a/client/src/cmddata.c
+++ b/client/src/cmddata.c
@@ -3082,7 +3082,7 @@ static int CmdDiff(const char *Cmd) {
     */
 
     size_t n = (datalenA > datalenB) ? datalenB : datalenA;
-    PrintAndLogEx(INFO, "data len:  %zu   A %zu  B %zu", n, datalenA, datalenB);
+    PrintAndLogEx(DEBUG, "data len:  %zu   A %zu  B %zu", n, datalenA, datalenB);
 
     if (inA == NULL)
         PrintAndLogEx(INFO, "inA null");
