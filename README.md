commit 08b583e2bbbe06a979284325ca31f36e9f1d1b4f
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jun 20 21:28:33 2021 +0200

    fix param call in lf keri demod

diff --git a/client/src/cmdlfkeri.c b/client/src/cmdlfkeri.c
index 8bd471049..f5df3b392 100644
--- a/client/src/cmdlfkeri.c
+++ b/client/src/cmdlfkeri.c
@@ -154,7 +154,7 @@ int demodKeri(bool verbose) {
         raw1 = bytebits_to_byte(DemodBuffer, 32);
         raw2 = bytebits_to_byte(DemodBuffer + 32, 32);
 
-        CmdPrintDemodBuff("x");
+        CmdPrintDemodBuff("-x");
     }
 
     //get internal id
