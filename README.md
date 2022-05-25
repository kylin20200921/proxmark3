commit 1b00c8911578e545fed8791c3b7e2508e86fea71
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Dec 25 18:51:23 2021 +0100

    fixing hf 15 dump

diff --git a/CHANGELOG.md b/CHANGELOG.md
index 16e3d08cc..0fcb5eb0f 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,7 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Fixed `hf 15 dump` - now correctly dumps 256 blocks w/o crashing the client (@iceman1001)
  - Changed `hf 14a sim -t 3` - anticollision for DESFire simulation now uses different RATS (@mosci)
  - Fixed `hf mfdes` - works with dynamic apdu and encode/decode buffers (@merlokk)
  - Added luascript `hf_ntag_bruteforce.lua` - ntag password bruteforce with the option to do NFC Tools MD5 versions of passwords (@keldnorman)
diff --git a/client/src/cmdhf15.c b/client/src/cmdhf15.c
index 54eeb4b02..92db238e9 100644
--- a/client/src/cmdhf15.c
+++ b/client/src/cmdhf15.c
@@ -660,7 +660,7 @@ static int NxpSysInfo(uint8_t *uid) {
     PacketResponseNG resp;
     clearCommandBuffer();
     SendCommandMIX(CMD_HF_ISO15693_COMMAND, reqlen, fast, reply, req, reqlen);
-    if (!WaitForResponseTimeout(CMD_ACK, &resp, 2000)) {
+    if (WaitForResponseTimeout(CMD_ACK, &resp, 2000) == false) {
         PrintAndLogEx(WARNING, "iso15693 timeout");
         DropField();
         return PM3_ETIMEOUT;
@@ -1344,8 +1344,7 @@ static int CmdHF15Dump(const char *Cmd) {
     uint8_t data[256 * 4] = {0};
     memset(data, 0, sizeof(data));
 
-    PrintAndLogEx(INFO, "." NOLF);
-    for (int retry = 0; retry < 5; retry++) {
+    for (int retry = 0; (retry < 5 && blocknum < 0x100); retry++) {
 
         req[10] = blocknum;
         AddCrc15(req, 11);
@@ -1392,14 +1391,13 @@ static int CmdHF15Dump(const char *Cmd) {
             retry = 0;
             blocknum++;
 
-            PrintAndLogEx(NORMAL, "." NOLF);
-            fflush(stdout);
+            PrintAndLogEx(INPLACE, "blk %3d", blocknum );
         }
     }
 
     DropField();
 
-    PrintAndLogEx(NORMAL, "");
+    PrintAndLogEx(NORMAL, "\n");
     PrintAndLogEx(INFO, "block#   | data         |lck| ascii");
     PrintAndLogEx(INFO, "---------+--------------+---+----------");
     for (int i = 0; i < blocknum; i++) {
