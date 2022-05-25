commit 2dadba9ceef080f2084eaddf4cb7bd8f10c5bbcf
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Oct 10 15:33:39 2021 +0200

    null checks

diff --git a/client/src/cmdcrc.c b/client/src/cmdcrc.c
index 4f241dcee..9d1353d3c 100644
--- a/client/src/cmdcrc.c
+++ b/client/src/cmdcrc.c
@@ -453,9 +453,17 @@ static int CmdrevengSearch(const char *Cmd) {
 
         memset(result, 0, 30);
         char *inCRC = calloc(crcChars + 1, sizeof(char));
+        if (inCRC == NULL) {
+            return 0;
+        }
+
         memcpy(inCRC, inHexStr + (dataLen - crcChars), crcChars);
 
         char *outHex = calloc(dataLen - crcChars + 1, sizeof(char));
+        if (outHex == NULL) {
+            return 0;
+        }
+
         memcpy(outHex, inHexStr, dataLen - crcChars);
 
         ans = RunModel(Models[i], outHex, false, 0, result);
