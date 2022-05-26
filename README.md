commit 77e6626c9c3c86e25b5ea0902bbc4114ebc08757
Author: iceman1001 <iceman@iuse.se>
Date:   Wed Mar 23 22:40:53 2022 +0100

    textual

diff --git a/client/src/cmdlf.c b/client/src/cmdlf.c
index 0252c4736..e8b14e611 100644
--- a/client/src/cmdlf.c
+++ b/client/src/cmdlf.c
@@ -1506,7 +1506,7 @@ int CmdLFfind(const char *Cmd) {
                 }
             }
 
-            PrintAndLogEx(INPLACE, "Searching for COTAG tag...");
+            PrintAndLogEx(INPLACE, "Searching for COTAG tag...    ");
             if (readCOTAGUid()) {
                 PrintAndLogEx(SUCCESS, "\nValid " _GREEN_("COTAG ID") " found!");
                 if (search_cont) {
