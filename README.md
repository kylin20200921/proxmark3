commit 1953cd953bca6849682a1168cf341bf83b942a52
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Mar 13 03:54:37 2022 +0100

    codeQL fix

diff --git a/client/src/cmddata.c b/client/src/cmddata.c
index 2479752e4..1745e62da 100644
--- a/client/src/cmddata.c
+++ b/client/src/cmddata.c
@@ -2943,11 +2943,11 @@ static int CmdDiff(const char *Cmd) {
     }
 
     if (splenA > 32) {
-        PrintAndLogEx(WARNING, "SPIFFS filname A length is large than 32 bytes, got %zu", splenA);
+        PrintAndLogEx(WARNING, "SPIFFS filname A length is large than 32 bytes, got %d", splenA);
         return PM3_EINVARG;
     }
     if (splenB > 32) {
-        PrintAndLogEx(WARNING, "SPIFFS filname B length is large than 32 bytes, got %zu", splenB);
+        PrintAndLogEx(WARNING, "SPIFFS filname B length is large than 32 bytes, got %d", splenB);
         return PM3_EINVARG;
     }
 
