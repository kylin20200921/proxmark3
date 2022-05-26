commit 758e7d9d2b347b137b0fd6332440639afb3cd70b
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Mar 24 12:30:48 2022 +0100

    improve hf mf wrbl with text and a new param --force  to make sure user understands when writing to block 0.

diff --git a/CHANGELOG.md b/CHANGELOG.md
index 9e5899629..53c932db2 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,7 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Changed `hf mf wrbl` - added more helptext and new param --force to point out block0 writes (@iceman1001)
  - Changed `hf 15 raw` - it now uses NG frame for response (@iceman1001)
  - Added `hf_14a_i2crevive` Script, revives soft bricked i2c (@equipter)
  - Changed `hf mf view` - now prints all keys and all value blocks under the ´verbose´ param (@iceman1001)
diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index 5d0f580cc..8c873d122 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -427,7 +427,12 @@ static int CmdHF14AMfWrBl(const char *Cmd) {
 
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mf wrbl",
-                  "Write MIFARE Classic block",
+                  "Write MIFARE Classic block with 16 hex bytes of data\n"
+                  " \n"
+                  "Sector 0 / Block 0 - Manufacturer block\n"
+                  "When writing to block 0 you must use a VALID block 0 data (UID, BCC, SAK, ATQA)\n"
+                  "Writing an invalid block 0 means rendering your Magic GEN2 card undetectable. \n"
+                  "Look in the magic_cards_notes.md file for help to resolve it.",
                   "hf mf wrbl --blk 1 -k FFFFFFFFFFFF -d 000102030405060708090a0b0c0d0e0f"
                  );
     void *argtable[] = {
@@ -435,6 +440,7 @@ static int CmdHF14AMfWrBl(const char *Cmd) {
         arg_int1(NULL, "blk", "<dec>", "block number"),
         arg_lit0("a", NULL, "input key type is key A (def)"),
         arg_lit0("b", NULL, "input key type is key B"),
+        arg_lit0(NULL, "force", "enforce block0 writes"),
         arg_str0("k", "key", "<hex>", "key, 6 hex bytes"),
         arg_str0("d", "data", "<hex>", "bytes to write, 16 hex bytes"),
 
@@ -442,7 +448,7 @@ static int CmdHF14AMfWrBl(const char *Cmd) {
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
 
-    int b = arg_get_int_def(ctx, 1, 0);
+    int b = arg_get_int_def(ctx, 1, 1);
 
     uint8_t keytype = MF_KEY_A;
     if (arg_get_lit(ctx, 2) && arg_get_lit(ctx, 3)) {
@@ -453,13 +459,15 @@ static int CmdHF14AMfWrBl(const char *Cmd) {
         keytype = MF_KEY_B;;
     }
 
+    bool force = arg_get_lit(ctx, 4);
+
     int keylen = 0;
     uint8_t key[6] = {0};
-    CLIGetHexWithReturn(ctx, 4, key, &keylen);
+    CLIGetHexWithReturn(ctx, 5, key, &keylen);
 
     uint8_t block[MFBLOCK_SIZE] = {0x00};
     int blen = 0;
-    CLIGetHexWithReturn(ctx, 5, block, &blen);
+    CLIGetHexWithReturn(ctx, 6, block, &blen);
     CLIParserFree(ctx);
 
     if (blen != MFBLOCK_SIZE) {
@@ -470,6 +478,15 @@ static int CmdHF14AMfWrBl(const char *Cmd) {
     if (b > 255) {
         return PM3_EINVARG;
     }
+    if (b == 0 && force == false) {
+        PrintAndLogEx(NORMAL, "");
+        PrintAndLogEx(INFO, "Targeting Sector 0 / Block 0 - Manufacturer block");
+        PrintAndLogEx(INFO, "Read the helptext for details before writing to this block");
+        PrintAndLogEx(INFO, "You must use param `" _YELLOW_("--force") "` to write to this block");
+        PrintAndLogEx(NORMAL, "");
+        return PM3_EINVARG;
+    }
+
     uint8_t blockno = (uint8_t)b;
 
     PrintAndLogEx(INFO, "Writing block no %d, key %c - %s", blockno, (keytype == MF_KEY_B) ? 'B' : 'A', sprint_hex_inrow(key, sizeof(key)));
diff --git a/client/src/cmdlfcotag.c b/client/src/cmdlfcotag.c
index f5be08ee5..7c4bf1541 100644
--- a/client/src/cmdlfcotag.c
+++ b/client/src/cmdlfcotag.c
@@ -133,7 +133,6 @@ static int CmdCOTAGReader(const char *Cmd) {
     int res = PM3_SUCCESS;
     while (!WaitForResponseTimeout(CMD_LF_COTAG_READ, &resp, 1000)) {
         timeout--;
-        PrintAndLogEx(NORMAL, "." NOLF);
         if (timeout == 0) {
             PrintAndLogEx(NORMAL, "");
             PrintAndLogEx(WARNING, "command execution time out");
diff --git a/client/src/cmdlfindala.c b/client/src/cmdlfindala.c
index a2bdddc9c..9b174c74a 100644
--- a/client/src/cmdlfindala.c
+++ b/client/src/cmdlfindala.c
@@ -162,14 +162,14 @@ int demodIndalaEx(int clk, int invert, int maxErr, bool verbose) {
     // to reduce false_positives
     // let's check the ratio of zeros in the demod buffer.
     size_t cnt_zeros = 0;
-    for (size_t i=0; i< g_DemodBufferLen; i++) {
+    for (size_t i = 0; i < g_DemodBufferLen; i++) {
         if (g_DemodBuffer[i] == 0x00)
             ++cnt_zeros;
     }
 
     // if more than 95% zeros in the demodbuffer then assume its wrong
     int32_t stats = (int32_t)((cnt_zeros * 100 / g_DemodBufferLen));
-    if ( stats > 95) {
+    if (stats > 95) {
         return PM3_ESOFT;
     }
 
diff --git a/doc/commands.json b/doc/commands.json
index 410a63835..cef3438bb 100644
--- a/doc/commands.json
+++ b/doc/commands.json
@@ -4430,7 +4430,7 @@
         },
         "hf mf wrbl": {
             "command": "hf mf wrbl",
-            "description": "write mifare classic block",
+            "description": "write mifare classic block with 16 hex bytes of data sector 0 / block 0 - manufacturer block when writing to block 0 you must use a valid block 0 data (uid, bcc, sak, atqa) writing an invalid block 0 means rendering your magic gen2 card undetectable. look in the magic_cards_notes.md file for help to resolve it.",
             "notes": [
                 "hf mf wrbl --blk 1 -k ffffffffffff -d 000102030405060708090a0b0c0d0e0f"
             ],
@@ -4440,10 +4440,11 @@
                 "--blk <dec> block number",
                 "-a input key type is key a (def)",
                 "-b input key type is key b",
+                "--force enforce block0 writes",
                 "-k, --key <hex> key, 6 hex bytes",
                 "-d, --data <hex> bytes to write, 16 hex bytes"
             ],
-            "usage": "hf mf wrbl [-hab] --blk <dec> [-k <hex>] [-d <hex>]"
+            "usage": "hf mf wrbl [-hab] --blk <dec> [--force] [-k <hex>] [-d <hex>]"
         },
         "hf mfdes auth": {
             "command": "hf mfdes auth",
@@ -10933,6 +10934,6 @@
     "metadata": {
         "commands_extracted": 689,
         "extracted_by": "PM3Help2JSON v1.00",
-        "extracted_on": "2022-03-23T16:50:35"
+        "extracted_on": "2022-03-24T10:21:12"
     }
 }
\ No newline at end of file
