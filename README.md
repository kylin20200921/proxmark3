commit 5b8b4b295c93249955e61e3f97ab459136f2c78a
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 29 10:07:53 2022 +0100

    remove reduntant

diff --git a/client/src/cmdlfpyramid.c b/client/src/cmdlfpyramid.c
index bd986d68c..da263468f 100644
--- a/client/src/cmdlfpyramid.c
+++ b/client/src/cmdlfpyramid.c
@@ -388,13 +388,11 @@ static int CmdPyramidSim(const char *Cmd) {
             return PM3_EINVARG;
         }
     } else {
+        // --raw and --fc/cn are mutually exclusive
         if (use_raw) {
-            // --raw and --fc/cn are mutually exclusive
-            if (use_raw) {
-                PrintAndLogEx(FAILED, "Can't specify both raw and fc/cn at the same time");
-                return PM3_EINVARG;
-            }  
-        }
+            PrintAndLogEx(FAILED, "Can't specify both raw and fc/cn at the same time");
+            return PM3_EINVARG;
+        }  
     }
 
     uint8_t bs[sizeof(raw) * 8];
