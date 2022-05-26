commit 732a47944cd18b6cac28281db70fc4b51a62ce47
Author: iceman1001 <iceman@iuse.se>
Date:   Wed Mar 23 22:39:36 2022 +0100

    remove verbose output.  less crap on lf search

diff --git a/client/src/cmdlfmotorola.c b/client/src/cmdlfmotorola.c
index 8897eff16..f57b96079 100644
--- a/client/src/cmdlfmotorola.c
+++ b/client/src/cmdlfmotorola.c
@@ -37,7 +37,7 @@ static int CmdHelp(const char *Cmd);
 int demodMotorola(bool verbose) {
     (void) verbose; // unused so far
     //PSK1
-    if (PSKDemod(32, 1, 100, true) != PM3_SUCCESS) {
+    if (PSKDemod(32, 1, 100, false) != PM3_SUCCESS) {
         PrintAndLogEx(DEBUG, "DEBUG: Error - Motorola: PSK Demod failed");
         return PM3_ESOFT;
     }
