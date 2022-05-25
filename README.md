commit ea80ea21ad5bfba83a8c63d30b1bbfc4d8adde2b
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jun 20 08:02:33 2021 +0200

    fix coverity CID 349314

diff --git a/client/src/cmdhfcipurse.c b/client/src/cmdhfcipurse.c
index 6fb3d42a3..c4d2df247 100644
--- a/client/src/cmdhfcipurse.c
+++ b/client/src/cmdhfcipurse.c
@@ -285,7 +285,7 @@ static int CmdHFCipurseReadFile(const char *Cmd) {
     }
 
     if (verbose)
-        PrintAndLogEx(INFO, "File id: %x offset %d key id: %d key: %s", fileId, offset, keyId, sprint_hex(key, CIPURSE_AES_KEY_LENGTH));
+        PrintAndLogEx(INFO, "File id " _YELLOW_("%x") " offset " _YELLOW_("%zu") " key id " _YELLOW_("%d") " key " _YELLOW_("%s"), fileId, offset, keyId, sprint_hex(key, CIPURSE_AES_KEY_LENGTH));
 
     if (noAuth == false) {
         bool bres = CIPURSEChannelAuthenticate(keyId, key, verbose);
