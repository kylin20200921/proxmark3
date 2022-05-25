commit abacf673dced8638c3a39f02a7f6b126dcd9414a
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jan 9 13:35:16 2022 +0100

    cppcheck fixes

diff --git a/client/src/cmdanalyse.c b/client/src/cmdanalyse.c
index 716107956..5de3bc063 100644
--- a/client/src/cmdanalyse.c
+++ b/client/src/cmdanalyse.c
@@ -1189,11 +1189,11 @@ static int CmdAnalyseUnits(const char *Cmd) {
         PrintAndLogEx(INFO, "  32 SSP = %u ETU (expect 1) " _GREEN_("ok"), SSP_TO_ETU(32));
     } else if (etu) {
 
-        PrintAndLogEx(INFO, " %d ETU = %u us ", ETU_TO_US(etu));
-        PrintAndLogEx(INFO, " %d ETU = %u SSP ", ETU_TO_SSP(etu));
+        PrintAndLogEx(INFO, " %d ETU = %u us ", ETU_TO_US(etu), 0);
+        PrintAndLogEx(INFO, " %d ETU = %u SSP ", ETU_TO_SSP(etu), 0);
     } else if (us) {
-        PrintAndLogEx(INFO, " %d us = %u ETU ", US_TO_ETU(us));
-        PrintAndLogEx(INFO, " %d us = %u SSP ", US_TO_SSP(us));
+        PrintAndLogEx(INFO, " %d us = %u ETU ", US_TO_ETU(us), 0);
+        PrintAndLogEx(INFO, " %d us = %u SSP ", US_TO_SSP(us), 0);
     }
 
     return PM3_SUCCESS;
