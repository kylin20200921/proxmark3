commit 8fee7e914e49d4b7098da5dddbcf80b80fb7aaa2
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Sep 10 18:11:19 2021 +0200

    hf mf sim - textual

diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index 318854e3f..aa133db52 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -3451,6 +3451,8 @@ static int CmdHF14AMfSim(const char *Cmd) {
             readerAttack(k_sector, k_sectorsCount, data[0], setEmulatorMem, verbose);
         }
         showSectorTable(k_sector, k_sectorsCount);
+    } else {
+        PrintAndLogEx(INFO, "Press pm3-button or abort simulation");
     }
     return PM3_SUCCESS;
 }
