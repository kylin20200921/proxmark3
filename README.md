commit a5b5b2f08a498770f26544b106dc3d320de198fe
Author: iceman1001 <iceman@iuse.se>
Date:   Mon May 30 22:35:33 2022 +0200

    fix alway defaulting to freq even if divisor was applied.  Will still have issue when addressing both standard values..

diff --git a/client/src/cmdlf.c b/client/src/cmdlf.c
index 39167163f..986d15cc1 100644
--- a/client/src/cmdlf.c
+++ b/client/src/cmdlf.c
@@ -137,7 +137,15 @@ static int CmdLFTune(const char *Cmd) {
         PrintAndLogEx(ERR, "freq must be between 47 and 600");
         return PM3_EINVARG;
     }
-    divisor = LF_FREQ2DIV(freq);
+
+    if (divisor != LF_DIVISOR_125 && freq != 125) {
+        PrintAndLogEx(ERR, "Select either `divisor` or `frequency`");
+        return PM3_EINVARG;
+    }
+
+    if (freq != 125)
+        divisor = LF_FREQ2DIV(freq);
+
 
     if ((is_bar + is_mix + is_value) > 1) {
         PrintAndLogEx(ERR, "Select only one output style");
