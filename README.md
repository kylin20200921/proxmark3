commit 38694a3fea3d91da0e9399a40ed6ae754ad63215
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Sep 3 21:19:12 2021 +0200

    text

diff --git a/client/src/cmdhfmfp.c b/client/src/cmdhfmfp.c
index facd784ee..83423320b 100644
--- a/client/src/cmdhfmfp.c
+++ b/client/src/cmdhfmfp.c
@@ -449,13 +449,13 @@ static int CmdHFMFPWritePerso(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfp wrp",
                   "Executes Write Perso command. Can be used in SL0 mode only.",
-                  "hf mfp wrp --ki 4000 --key 000102030405060708090a0b0c0d0e0f -> write key (00..0f) to key number 4000 \n"
-                  "hf mfp wrp --ki 4000 -> write default key(0xff..0xff) to key number 4000");
+                  "hf mfp wrp --ki 4000 --key 000102030405060708090a0b0c0d0e0f  -> write key (00..0f) to key number 4000 \n"
+                  "hf mfp wrp --ki 4000                                         -> write default key(0xff..0xff) to key number 4000");
 
     void *argtable[] = {
         arg_param_begin,
         arg_lit0("v", "verbose", "show internal data."),
-        arg_str1("i", "ki",  "<hex>", " key number, 2 hex bytes"),
+        arg_str1(NULL, "ki",  "<hex>", " key number, 2 hex bytes"),
         arg_strx0(NULL, "key", "<hex>", " key, 16 hex bytes"),
         arg_param_end
     };
@@ -515,8 +515,8 @@ static int CmdHFMFPInitPerso(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfp initp",
                   "Executes Write Perso command for all card's keys. Can be used in SL0 mode only.",
-                  "hf mfp initp --key 000102030405060708090a0b0c0d0e0f -> fill all the keys with key (00..0f)\n"
-                  "hf mfp initp -vv -> fill all the keys with default key(0xff..0xff) and show all the data exchange");
+                  "hf mfp initp --key 000102030405060708090a0b0c0d0e0f  -> fill all the keys with key (00..0f)\n"
+                  "hf mfp initp -vv                                     -> fill all the keys with default key(0xff..0xff) and show all the data exchange");
 
     void *argtable[] = {
         arg_param_begin,
@@ -637,8 +637,8 @@ static int CmdHFMFPAuth(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfp auth",
                   "Executes AES authentication command for Mifare Plus card",
-                  "hf mfp auth --ki 4000 --key 000102030405060708090a0b0c0d0e0f -> executes authentication\n"
-                  "hf mfp auth --ki 9003 --key FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF -v -> executes authentication and shows all the system data");
+                  "hf mfp auth --ki 4000 --key 000102030405060708090a0b0c0d0e0f      -> executes authentication\n"
+                  "hf mfp auth --ki 9003 --key FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF -v   -> executes authentication and shows all the system data");
 
     void *argtable[] = {
         arg_param_begin,
@@ -671,8 +671,8 @@ static int CmdHFMFPRdbl(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfp rdbl",
                   "Reads several blocks from Mifare Plus card",
-                  "hf mfp rdbl --blk 0 --key 000102030405060708090a0b0c0d0e0f -> executes authentication and read block 0 data\n"
-                  "hf mfp rdbl --blk 1 -v -> executes authentication and shows sector 1 data with default key 0xFF..0xFF");
+                  "hf mfp rdbl --blk 0 --key 000102030405060708090a0b0c0d0e0f   -> executes authentication and read block 0 data\n"
+                  "hf mfp rdbl --blk 1 -v                                       -> executes authentication and shows sector 1 data with default key 0xFF..0xFF");
 
     void *argtable[] = {
         arg_param_begin,
@@ -784,15 +784,15 @@ static int CmdHFMFPRdsc(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfp rdsc",
                   "Reads one sector from Mifare Plus card",
-                  "hf mfp rdsc --sn 0 --key 000102030405060708090a0b0c0d0e0f -> executes authentication and read sector 0 data\n"
-                  "hf mfp rdsc --sn 1 -v -> executes authentication and shows sector 1 data with default key");
+                  "hf mfp rdsc -s 0 --key 000102030405060708090a0b0c0d0e0f   -> executes authentication and read sector 0 data\n"
+                  "hf mfp rdsc -s 1 -v                                       -> executes authentication and shows sector 1 data with default key");
 
     void *argtable[] = {
         arg_param_begin,
         arg_lit0("v",  "verbose", "show internal data."),
         arg_lit0("b",  "keyb",    "use key B (by default keyA)."),
         arg_lit0("p",  "plain",   "plain communication mode between reader and card."),
-        arg_int1(NULL, "sn",      "<dec>", "sector number (0..255)"),
+        arg_int1("s",  "sn",      "<dec>", "sector number (0..255)"),
         arg_str0("k",  "key",     "<hex>", "key, 16 hex bytes"),
         arg_param_end
     };
@@ -880,8 +880,8 @@ static int CmdHFMFPWrbl(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfp wrbl",
                   "Writes one block to Mifare Plus card",
-                  "hf mfp wrbl --blk 1 -d ff0000000000000000000000000000ff --key 000102030405060708090a0b0c0d0e0f -> writes block 1 data\n"
-                  "hf mfp wrbl --blk 2 -d ff0000000000000000000000000000ff -v -> writes block 2 data with default key 0xFF..0xFF"
+                  "hf mfp wrbl --blk 1 -d ff0000000000000000000000000000ff --key 000102030405060708090a0b0c0d0e0f -> write block 1 data\n"
+                  "hf mfp wrbl --blk 2 -d ff0000000000000000000000000000ff -v                                     -> write block 2 data with default key 0xFF..0xFF"
                  );
 
     void *argtable[] = {
@@ -1091,11 +1091,11 @@ static int CmdHFMFPChk(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfp chk",
                   "Checks keys with Mifare Plus card.",
-                  "hf mfp chk -k 000102030405060708090a0b0c0d0e0f -> check key on sector 0 as key A and B\n"
-                  "hf mfp chk -s 2 -a -> check default key list on sector 2, key A\n"
-                  "hf mfp chk -d mfp_default_keys -s0 -e6 -> check keys from dictionary against sectors 0-6\n"
-                  "hf mfp chk --pattern1b -j keys -> check all 1-byte keys pattern and save found keys to json\n"
-                  "hf mfp chk --pattern2b --startp2b FA00 -> check all 2-byte keys pattern. Start from key FA00FA00...FA00");
+                  "hf mfp chk -k 000102030405060708090a0b0c0d0e0f  -> check key on sector 0 as key A and B\n"
+                  "hf mfp chk -s 2 -a                              -> check default key list on sector 2, key A\n"
+                  "hf mfp chk -d mfp_default_keys -s0 -e6          -> check keys from dictionary against sectors 0-6\n"
+                  "hf mfp chk --pattern1b -j keys                  -> check all 1-byte keys pattern and save found keys to json\n"
+                  "hf mfp chk --pattern2b --startp2b FA00          -> check all 2-byte keys pattern. Start from key FA00FA00...FA00");
 
     void *argtable[] = {
         arg_param_begin,
@@ -1323,8 +1323,8 @@ static int CmdHFMFPMAD(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfp mad",
                   "Checks and prints Mifare Application Directory (MAD)",
-                  "hf mfp mad -> shows MAD if exists\n"
-                  "hf mfp mad --aid e103 -k d3f7d3f7d3f7d3f7d3f7d3f7d3f7d3f7 -> read and print NDEF data from mad aid if exists");
+                  "hf mfp mad\n"
+                  "hf mfp mad --aid e103 -k d3f7d3f7d3f7d3f7d3f7d3f7d3f7d3f7  -> read and print NDEF data from MAD aid");
 
     void *argtable[] = {
         arg_param_begin,
@@ -1463,8 +1463,8 @@ int CmdHFMFPNDEFRead(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfp ndefread",
                   "Prints NFC Data Exchange Format (NDEF)",
-                  "hf mfp ndefread -> shows NDEF data\n"
-                  "hf mfp ndefread -vv -> shows NDEF parsed and raw data\n"
+                  "hf mfp ndefread \n"
+                  "hf mfp ndefread -vv                                            -> shows NDEF parsed and raw data\n"
                   "hf mfp ndefread --aid e103 -k d3f7d3f7d3f7d3f7d3f7d3f7d3f7d3f7 -> shows NDEF data with custom AID and key");
 
     void *argtable[] = {
