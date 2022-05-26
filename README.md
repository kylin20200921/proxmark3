commit 6da5c9d0f48fcff0177f13f41cc6623229cc3876
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Feb 14 09:20:14 2022 +0100

    calloc should already taken care of the zeroing

diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index dd4636b09..e662e248b 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -4152,7 +4152,6 @@ static int CmdHF14AMfEView(const char *Cmd) {
         PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
         return PM3_EMALLOC;
     }
-    memset(dump, 0, bytes);
 
     PrintAndLogEx(INFO, "downloading emulator memory");
     if (!GetFromDevice(BIG_BUF_EML, dump, bytes, 0, NULL, 0, NULL, 2500, false)) {
