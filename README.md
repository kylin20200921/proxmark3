commit 26f88b68de6b48adc92e74c8eae05449959e617a
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Feb 13 12:56:05 2022 +0100

    daddy got a new style

diff --git a/client/src/cmdhw.c b/client/src/cmdhw.c
index 5e462ea8d..6b1a8b9b8 100644
--- a/client/src/cmdhw.c
+++ b/client/src/cmdhw.c
@@ -987,7 +987,7 @@ void pm3_version(bool verbose, bool oneliner) {
     if (!verbose)
         return;
 
-    PrintAndLogEx(NORMAL, "\n [ " _CYAN_("Proxmark3 RFID instrument") " ]");
+    PrintAndLogEx(NORMAL, "\n [ " _YELLOW_("Proxmark3 RFID instrument") " ]");
     PrintAndLogEx(NORMAL, "\n [ " _YELLOW_("CLIENT") " ]");
     FormatVersionInformation(temp, sizeof(temp), "  ", &g_version_information);
     PrintAndLogEx(NORMAL, "%s", temp);
diff --git a/client/src/proxmark3.c b/client/src/proxmark3.c
index 0a74e52a3..f9e101469 100644
--- a/client/src/proxmark3.c
+++ b/client/src/proxmark3.c
@@ -73,21 +73,25 @@ static void showBanner_logo(LogoMode mode) {
             break;
         }
         case ANSI: {
-            PrintAndLogEx(NORMAL, "  " _BLUE_("██████╗ ███╗   ███╗█████╗ "));
-            PrintAndLogEx(NORMAL, "  " _BLUE_("██╔══██╗████╗ ████║╚═══██╗"));
-            PrintAndLogEx(NORMAL, "  " _BLUE_("██████╔╝██╔████╔██║ ████╔╝"));
-            PrintAndLogEx(NORMAL, "  " _BLUE_("██╔═══╝ ██║╚██╔╝██║ ╚══██╗"));
-            PrintAndLogEx(NORMAL, "  " _BLUE_("██║     ██║ ╚═╝ ██║█████╔╝") " " BANNERMSG1);
-            PrintAndLogEx(NORMAL, "  " _BLUE_("╚═╝     ╚═╝     ╚═╝╚════╝ ") " " BANNERMSG2);
+            PrintAndLogEx(NORMAL, "  " _CYAN_("8888888b.  888b     d888  .d8888b.   "));
+            PrintAndLogEx(NORMAL, "  " _CYAN_("888   Y88b 8888b   d8888 d88P  Y88b  "));
+            PrintAndLogEx(NORMAL, "  " _CYAN_("888    888 88888b.d88888      .d88P  "));
+            PrintAndLogEx(NORMAL, "  " _CYAN_("888   d88P 888Y88888P888     8888\"  "));
+            PrintAndLogEx(NORMAL, "  " _CYAN_("8888888P\"  888 Y888P 888      \"Y8b.  "));
+            PrintAndLogEx(NORMAL, "  " _CYAN_("888        888  Y8P  888 888    888  "));
+            PrintAndLogEx(NORMAL, "  " _CYAN_("888        888   \"   888 Y88b  d88P") " " BANNERMSG1);
+            PrintAndLogEx(NORMAL, "  " _CYAN_("888        888       888  \"Y8888P\"") " " BANNERMSG2);
             break;
         }
         case ASCII: {
-            PrintAndLogEx(NORMAL, "  ######. ###.   ###.#####. ");
-            PrintAndLogEx(NORMAL, "  ##...##.####. ####. ...##.");
-            PrintAndLogEx(NORMAL, "  ######..##.####.##. ####..");
-            PrintAndLogEx(NORMAL, "  ##..... ##..##..##.  ..##.");
-            PrintAndLogEx(NORMAL, "  ##.     ##.  .. ##.#####.. " BANNERMSG1);
-            PrintAndLogEx(NORMAL, "   ..      ..      .. ..... " BANNERMSG2);
+            PrintAndLogEx(NORMAL, "  8888888b.  888b     d888  .d8888b.     ");
+            PrintAndLogEx(NORMAL, "  888   Y88b 8888b   d8888 d88P  Y88b    ");
+            PrintAndLogEx(NORMAL, "  888    888 88888b.d88888      .d88P    ");
+            PrintAndLogEx(NORMAL, "  888   d88P 888Y88888P888     8888\"    ");
+            PrintAndLogEx(NORMAL, "  8888888P\"  888 Y888P 888      \"Y8b.  ");
+            PrintAndLogEx(NORMAL, "  888        888  Y8P  888 888    888    ");
+            PrintAndLogEx(NORMAL, "  888        888   \"   888 Y88b  d88P " BANNERMSG1);
+            PrintAndLogEx(NORMAL, "  888        888       888  \"Y8888P\" " BANNERMSG2);
             break;
         }
     }
@@ -115,7 +119,7 @@ static void showBanner(void) {
 //    PrintAndLogEx(NORMAL, "\nSupport iceman on patreon - https://www.patreon.com/iceman1001/");
 //    PrintAndLogEx(NORMAL, "                 on paypal - https://www.paypal.me/iceman1001");
 //    PrintAndLogEx(NORMAL, "\nMonero: 43mNJLpgBVaTvyZmX9ajcohpvVkaRy1kbZPm8tqAb7itZgfuYecgkRF36rXrKFUkwEGeZedPsASRxgv4HPBHvJwyJdyvQuP");
-    PrintAndLogEx(NORMAL, "");
+//    PrintAndLogEx(NORMAL, "");
     fflush(stdout);
     g_printAndLog = old_printAndLog;
 }
