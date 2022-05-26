commit a45082e045a22aff7178282da302b1602eb2e947
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Mar 13 03:57:10 2022 +0100

    increase string line array

diff --git a/client/src/cmddata.c b/client/src/cmddata.c
index 1745e62da..fd48f2f1e 100644
--- a/client/src/cmddata.c
+++ b/client/src/cmddata.c
@@ -3106,7 +3106,7 @@ static int CmdDiff(const char *Cmd) {
 
     // index 4bytes, spaces, bar, bytes with ansi codes
     // (16 * 2 * 8 ) + 32 
-    char line[800] = {0};
+    char line[880] = {0};
 
     // print data diff loop
     for (int i = 0; i < n;  i += width ) {
