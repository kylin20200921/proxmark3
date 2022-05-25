commit ed10409bfd7114ec6ec99c8a68372f9eb64c386e
Author: iceman1001 <iceman@iuse.se>
Date:   Tue Jan 4 07:31:10 2022 +0100

    cppcheck fix uninitvar

diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index 123f3d929..d127010d5 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -743,13 +743,16 @@ int CmdHF14ASim(const char *Cmd) {
         keypress = kbd_enter_pressed();
     }
 
-    if (keypress && (flags & FLAG_NR_AR_ATTACK) == FLAG_NR_AR_ATTACK) {
-        // inform device to break the sim loop since client has exited
-        SendCommandNG(CMD_BREAK_LOOP, NULL, 0);
-    }
+    if (keypress) {
+        if ((flags & FLAG_NR_AR_ATTACK) == FLAG_NR_AR_ATTACK) {
+            // inform device to break the sim loop since client has exited
+            SendCommandNG(CMD_BREAK_LOOP, NULL, 0);
+        }
 
-    if (resp.status == PM3_EOPABORTED && ((flags & FLAG_NR_AR_ATTACK) == FLAG_NR_AR_ATTACK))
-        showSectorTable(k_sector, k_sectorsCount);
+        if (resp.status == PM3_EOPABORTED && ((flags & FLAG_NR_AR_ATTACK) == FLAG_NR_AR_ATTACK)) {
+            showSectorTable(k_sector, k_sectorsCount);
+        }
+    }
 
     PrintAndLogEx(INFO, "Done");
     return PM3_SUCCESS;
