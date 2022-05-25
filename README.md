commit 7e3c13fa0d6b9cdb96e86902e9e6347ba543f7bf
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Sep 10 18:12:12 2021 +0200

    hf mf sim - textual

diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index aa133db52..0db1c36c8 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -3452,7 +3452,7 @@ static int CmdHF14AMfSim(const char *Cmd) {
         }
         showSectorTable(k_sector, k_sectorsCount);
     } else {
-        PrintAndLogEx(INFO, "Press pm3-button or abort simulation");
+        PrintAndLogEx(INFO, "Press pm3-button to abort simulation");
     }
     return PM3_SUCCESS;
 }
