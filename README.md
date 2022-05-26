commit bb1a51988cf08d3bef272aad1df61c2b09db82d4
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Feb 24 17:24:42 2022 +0100

    fixes to "hf cipurse info"

diff --git a/client/src/cipurse/cipursecore.c b/client/src/cipurse/cipursecore.c
index cce2d0484..76b9d9b8d 100644
--- a/client/src/cipurse/cipursecore.c
+++ b/client/src/cipurse/cipursecore.c
@@ -332,8 +332,7 @@ void CIPURSEPrintInfoFile(uint8_t *data, size_t len) {
     }
 
     PrintAndLogEx(INFO, "--- " _CYAN_("CIPURSE Information") "---------------------");
-    PrintAndLogEx(INFO, "version........ " _YELLOW_("%d"), data[0]);
-    PrintAndLogEx(INFO, "revision....... " _YELLOW_("%d"), data[1]);
+    PrintAndLogEx(INFO, "Version........ " _YELLOW_("v%d.%d"), data[0], data[1]);
 
     if (len >= 3)
         CIPURSEPrintPersoMode(data[2]);
