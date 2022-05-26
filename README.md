commit 492ea52dfa93d7f0ac377b1f4e4f3ad2799a9e65
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Mar 25 02:24:52 2022 +0100

    adding clone option

diff --git a/client/src/cmdlft55xx.c b/client/src/cmdlft55xx.c
index e06d8a228..b47d3c533 100644
--- a/client/src/cmdlft55xx.c
+++ b/client/src/cmdlft55xx.c
@@ -122,11 +122,14 @@ static int CmdT55xxCloneHelp(const char *Cmd) {
     PrintAndLogEx(NORMAL, _GREEN_("lf em 410x clone"));
 // todo:  implement restore
 //    PrintAndLogEx(NORMAL, _GREEN_("lf em 4x05 write"));
-//    PrintAndLogEx(NORMAL, _GREEN_("lf em 4x50 write"));
+//    PrintAndLogEx(NORMAL, _GREEN_("lf em 4x50 restore"));
     PrintAndLogEx(NORMAL, _GREEN_("lf fdxb clone"));
     PrintAndLogEx(NORMAL, _GREEN_("lf gallagher clone"));
     PrintAndLogEx(NORMAL, _GREEN_("lf gproxii clone"));
     PrintAndLogEx(NORMAL, _GREEN_("lf hid clone"));
+// todo:  implement restore
+//    PrintAndLogEx(NORMAL, _GREEN_("lf hitag clone"));
+    PrintAndLogEx(NORMAL, _GREEN_("lf idteck clone"));
     PrintAndLogEx(NORMAL, _GREEN_("lf indala clone"));
     PrintAndLogEx(NORMAL, _GREEN_("lf io clone"));
     PrintAndLogEx(NORMAL, _GREEN_("lf jablotron clone"));
