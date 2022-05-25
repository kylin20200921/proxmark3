commit fb9f7d6562a1632f8c0ffb2d504a61b0a11c446a
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 29 10:11:15 2022 +0100

    cppcheck bad logic

diff --git a/client/src/cmdhffelica.c b/client/src/cmdhffelica.c
index 6b2d3e92e..715cd89c5 100644
--- a/client/src/cmdhffelica.c
+++ b/client/src/cmdhffelica.c
@@ -1777,13 +1777,11 @@ static int CmdHFFelicaSniff(const char *Cmd) {
     if (payload.samples > 9999) {
         payload.samples = 9999;
         PrintAndLogEx(INFO, "Too large samples to skip value, using max value 9999");
-        return PM3_EINVARG;
     }
 
     if (payload.triggers  > 9999) {
         payload.triggers  = 9999;
         PrintAndLogEx(INFO, "Too large trigger to skip value, using max value 9999");
-        return PM3_EINVARG;
     }
 
 
