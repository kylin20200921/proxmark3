commit a432b49393ab5010edb8a4555a11724d64a1cb51
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Jan 7 21:06:07 2022 +0100

    change gprox ii clone/sim to handle 20b cardnumber for the 36b format

diff --git a/client/src/cmdlfguard.c b/client/src/cmdlfguard.c
index 4a31e8f19..231b0f6f2 100644
--- a/client/src/cmdlfguard.c
+++ b/client/src/cmdlfguard.c
@@ -236,8 +236,8 @@ static int CmdGuardClone(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "lf gproxii clone",
                   "clone a Guardall tag to a T55x7, Q5/T5555 or EM4305/4469 tag.\n"
-                  "The facility-code is 8-bit and the card number is 16-bit.  Larger values are truncated.\n"
-                  "Currently work only on 26bit",
+                  "The facility-code is 8-bit and the card number is 20-bit.  Larger values are truncated.\n"
+                  "Currently work only on 26 | 36 bit format",
                   "lf gproxii clone --fmt 26 --fc 123 --cn 1337\n"
                   "lf gproxii clone --q5 --fmt 26 --fc 123 --cn 1337   -> encode for Q5/T5555 tag\n"
                   "lf gproxii clone --em --fmt 26 --fc 123 --cn 1337   -> encode for EM4305/4469"
@@ -268,7 +268,7 @@ static int CmdGuardClone(const char *Cmd) {
 
     fmtlen &= 0x7f;
     uint32_t facilitycode = (fc & 0x000000FF);
-    uint32_t cardnumber = (cn & 0x0000FFFF);
+    uint32_t cardnumber = (cn & 0x00FFFFFF);
 
     //GuardProxII - compat mode, ASK/Biphase,  data rate 64, 3 data blocks
     uint8_t *bs = calloc(96, sizeof(uint8_t));
@@ -323,7 +323,7 @@ static int CmdGuardSim(const char *Cmd) {
                   "Enables simulation of Guardall card with specified card number.\n"
                   "Simulation runs until the button is pressed or another USB command is issued.\n"
                   "The facility-code is 8-bit and the card number is 16-bit.  Larger values are truncated.\n"
-                  "Currently work only on 26bit",
+                  "Currently work only on 26 | 36 bit format",
                   "lf gproxii sim --fmt 26 --fc 123 --cn 1337\n"
                  );
 
@@ -343,7 +343,7 @@ static int CmdGuardSim(const char *Cmd) {
 
     fmtlen &= 0x7F;
     uint32_t facilitycode = (fc & 0x000000FF);
-    uint32_t cardnumber = (cn & 0x0000FFFF);
+    uint32_t cardnumber = (cn & 0x000FFFFF);
 
     uint8_t bs[96];
     memset(bs, 0x00, sizeof(bs));
