commit 03a7109efde85634e6f8834762d84d801e1d788a
Author: iceman1001 <iceman@iuse.se>
Date:   Wed May 19 10:20:30 2021 +0200

    fix coverity CID 321717

diff --git a/client/src/wiegand_formats.c b/client/src/wiegand_formats.c
index 0daf1dd5d..75f9ea987 100644
--- a/client/src/wiegand_formats.c
+++ b/client/src/wiegand_formats.c
@@ -1383,7 +1383,7 @@ void HIDListFormats(void) {
         ++i;
     }
     PrintAndLogEx(INFO, "------------------------------------------------------------");
-    PrintAndLogEx(INFO, "Available card formats: " _YELLOW_("%" PRIu32), ARRAYLEN(FormatTable));
+    PrintAndLogEx(INFO, "Available card formats: " _YELLOW_("%" PRIu64), ARRAYLEN(FormatTable));
     PrintAndLogEx(NORMAL, "");
     return;
 }
