commit cb5cd18ff21409a7ac5e97576ef8de4027195159
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Dec 31 16:14:11 2021 +0100

    make even more obvious

diff --git a/client/src/flash.c b/client/src/flash.c
index 869cd1630..c0ef1d25b 100644
--- a/client/src/flash.c
+++ b/client/src/flash.c
@@ -411,11 +411,15 @@ static void flash_suggest_update_bootloader(void) {
 
     PrintAndLogEx(ERR, _RED_("It is recommended that you first" _YELLOW_(" update your bootloader") _RED_(" alone,")));
     PrintAndLogEx(ERR, _RED_("reboot the Proxmark3 then only update the main firmware") "\n");
-    PrintAndLogEx(ERR, "Follow these steps :");
+    PrintAndLogEx(NORMAL, "");
+    PrintAndLogEx(ERR, "------------- " _CYAN_("Follow these steps") " -------------------");
+    PrintAndLogEx(NORMAL, "");
     PrintAndLogEx(ERR, " 1)   ./pm3-flash-bootrom");
     PrintAndLogEx(ERR, " 2)   ./pm3-flash-all");
     PrintAndLogEx(ERR, " 3)   ./pm3");
-    PrintAndLogEx(INFO, "--------------------------------------------------------");
+    PrintAndLogEx(NORMAL, "");
+    PrintAndLogEx(INFO, "---------------------------------------------------");
+    PrintAndLogEx(NORMAL, "");
     gs_printed_msg = true;
 }
 
