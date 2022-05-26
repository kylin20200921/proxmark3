commit 85f16fd471947f0ddd87731a72fe2aa1cb0d0244
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Mar 13 04:45:32 2022 +0100

    textual

diff --git a/client/src/cmddata.c b/client/src/cmddata.c
index a5920ed5f..a8f65a388 100644
--- a/client/src/cmddata.c
+++ b/client/src/cmddata.c
@@ -3092,17 +3092,19 @@ static int CmdDiff(const char *Cmd) {
         PrintAndLogEx(INFO, "inB null");
 
     int hdr_sln = (width * 4) + 2;
-    PrintAndLogEx(INFO, "");
 
-    char hdr0[200] = " #  | " _CYAN_("A");
+    char hdr0[200] = " #  | " _CYAN_("a");
     memset(hdr0 + strlen(hdr0), ' ', hdr_sln - 2);
-    strcat(hdr0 + strlen(hdr0), "| " _CYAN_("B"));
-    PrintAndLogEx(INFO, hdr0);
+    strcat(hdr0 + strlen(hdr0), "| " _CYAN_("b"));
 
     char hdr1[200] = "----+";
     memset(hdr1 + strlen(hdr1), '-', hdr_sln);
     memset(hdr1 + strlen(hdr1), '+', 1);
     memset(hdr1 + strlen(hdr1), '-', hdr_sln);
+
+    PrintAndLogEx(INFO, "");
+    PrintAndLogEx(INFO, hdr1);
+    PrintAndLogEx(INFO, hdr0);
     PrintAndLogEx(INFO, hdr1);
 
     // index 4bytes, spaces, bar, bytes with ansi codes
