commit e0fc80c539c9579805ae209410fbda80f428e7ce
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jun 20 08:01:01 2021 +0200

    fix coverity CID 349315

diff --git a/client/src/cmdhfcipurse.c b/client/src/cmdhfcipurse.c
index bc1651b6a..6fb3d42a3 100644
--- a/client/src/cmdhfcipurse.c
+++ b/client/src/cmdhfcipurse.c
@@ -73,7 +73,7 @@ static int CmdHFCipurseInfo(const char *Cmd) {
         return PM3_SUCCESS;
     }
 
-    PrintAndLogEx(INFO, "Cipurse card: " _GREEN_("OK"));
+    PrintAndLogEx(INFO, "Cipurse card ( " _GREEN_("ok") " )");
 
     res = CIPURSESelectFile(0x2ff7, buf, sizeof(buf), &len, &sw);
     if (res != 0 || sw != 0x9000) {
@@ -88,8 +88,8 @@ static int CmdHFCipurseInfo(const char *Cmd) {
     }
 
     if (len > 0) {
-        PrintAndLogEx(INFO, "Info file: " _GREEN_("OK"));
-        PrintAndLogEx(INFO, "[%d]: %s", len, sprint_hex(buf, len));
+        PrintAndLogEx(INFO, "Info file ( " _GREEN_("ok") " )");
+        PrintAndLogEx(INFO, "[%zu]: %s", len, sprint_hex(buf, len));
         CIPURSEPrintInfoFile(buf, len);
     }
 
