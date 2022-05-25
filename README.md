commit 071a171af9c5e5b1c2639944171bc17276745118
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jan 16 20:06:44 2022 +0100

    remove text

diff --git a/client/src/proxmark3.c b/client/src/proxmark3.c
index a7cc7f446..548d35ef8 100644
--- a/client/src/proxmark3.c
+++ b/client/src/proxmark3.c
@@ -401,9 +401,7 @@ check_script:
                 // process cmd
                 g_pendingPrompt = false;
                 mainret = CommandReceived(cmd);
-#if defined ICOPYX
-                PrintAndLogEx(NORMAL, "\nNikola.D: %d", ret);
-#endif
+
                 // exit or quit
                 if (mainret == PM3_EFATAL)
                     break;
