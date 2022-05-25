commit 0d017908b4ddbb0abbcdd3222c94156e9249f32f
Author: iceman1001 <iceman@iuse.se>
Date:   Thu May 20 10:31:21 2021 +0200

    style

diff --git a/client/src/cmdhfmfdes.c b/client/src/cmdhfmfdes.c
index 121108c62..4fb04b324 100644
--- a/client/src/cmdhfmfdes.c
+++ b/client/src/cmdhfmfdes.c
@@ -4974,8 +4974,6 @@ static int CmdHF14aDesNDEF(const char *Cmd) {
         }
     }
 
-//    PrintAndLogEx(INFO, "reading data from tag");
-
     if (!datalen) {
         PrintAndLogEx(ERR, "no NDEF data");
         return PM3_SUCCESS;
