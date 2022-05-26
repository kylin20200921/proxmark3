commit c0ea29ab95d72ae33f222c7742f51b72348ccb09
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Feb 6 21:19:28 2022 +0100

    helptext unify

diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index e67afdff0..8fe003c4c 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -3008,7 +3008,7 @@ static int CmdHF14AMfChk(const char *Cmd) {
         arg_param_begin,
         arg_strx0("k", "key", "<hex>", "Key specified as 12 hex symbols"),
         arg_int0(NULL, "blk", "<dec>", "Input block number"),
-        arg_lit0("a", NULL, "Target Key A, if found also check Key B for duplicate"),
+        arg_lit0("a", NULL, "Target Key A"),
         arg_lit0("b", NULL, "Target Key B"),
         arg_lit0("*", "all", "Target both key A & B (default)"),
         arg_lit0(NULL, "mini", "MIFARE Classic Mini / S20"),
@@ -3017,7 +3017,7 @@ static int CmdHF14AMfChk(const char *Cmd) {
         arg_lit0(NULL, "4k", "MIFARE Classic 4k / S70"),
         arg_lit0(NULL, "emu", "Fill simulator keys from found keys"),
         arg_lit0(NULL, "dump", "Dump found keys to binary file"),
-        arg_str0("f", "file", "<fn>", "filename of dictionary"),
+        arg_str0("f", "file", "<fn>", "Filename of dictionary"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
diff --git a/client/src/cmdhfmfp.c b/client/src/cmdhfmfp.c
index 279d6e270..b90f1b9bc 100644
--- a/client/src/cmdhfmfp.c
+++ b/client/src/cmdhfmfp.c
@@ -461,9 +461,9 @@ static int CmdHFMFPWritePerso(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("v", "verbose", "show internal data."),
-        arg_str1(NULL, "ki",  "<hex>", " key number, 2 hex bytes"),
-        arg_str0(NULL, "key", "<hex>", " key, 16 hex bytes"),
+        arg_lit0("v", "verbose", "Verbose mode"),
+        arg_str1(NULL, "ki",  "<hex>", " Key number, 2 hex bytes"),
+        arg_str0(NULL, "key", "<hex>", " Key, 16 hex bytes"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -527,8 +527,8 @@ static int CmdHFMFPInitPerso(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_litn("v",  "verbose", 0, 2, "show internal data."),
-        arg_str0("k", "key", "<hex>", "key, 16 hex bytes"),
+        arg_litn("v",  "verbose", 0, 2, "Verbose mode"),
+        arg_str0("k", "key", "<hex>", "Key, 16 hex bytes"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -602,7 +602,7 @@ static int CmdHFMFPCommitPerso(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("v",  "verbose", "show internal data."),
+        arg_lit0("v",  "verbose", "Verbose mode"),
 //        arg_int0(NULL,  "sl", "<dec>", "SL mode"),
         arg_param_end
     };
@@ -649,9 +649,9 @@ static int CmdHFMFPAuth(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("v",  "verbose", "show internal data."),
-        arg_str1(NULL, "ki", "<hex>", "key number, 2 hex bytes"),
-        arg_str1(NULL, "key", "<hex>", "key, 16 hex bytes"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_str1(NULL, "ki", "<hex>", "Key number, 2 hex bytes"),
+        arg_str1(NULL, "key", "<hex>", "Key, 16 hex bytes"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -683,11 +683,11 @@ static int CmdHFMFPRdbl(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("v", "verbose", "show internal data"),
-        arg_int0("n", "count", "<dec>", "blocks count (by default 1)"),
-        arg_lit0("b", "keyb", "use key B (by default keyA)"),
-        arg_lit0("p", "plain", "plain communication mode between reader and card"),
-        arg_int1(NULL, "blk", "<dec>", "block number (0..255)"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "count", "<dec>", "Blocks count (def: 1)"),
+        arg_lit0("b",  "keyb", "Use key B (def: keyA)"),
+        arg_lit0("p",  "plain", "Plain communication mode between reader and card"),
+        arg_int1(NULL, "blk", "<0..255>", "Block number"),
         arg_str0(NULL, "key", "<hex>", "Key, 16 hex bytes"),
         arg_param_end
     };
@@ -790,17 +790,17 @@ static int CmdHFMFPRdbl(const char *Cmd) {
 static int CmdHFMFPRdsc(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfp rdsc",
-                  "Reads one sector from Mifare Plus card",
+                  "Reads one sector from MIFARE Plus card",
                   "hf mfp rdsc -s 0 --key 000102030405060708090a0b0c0d0e0f   -> executes authentication and read sector 0 data\n"
                   "hf mfp rdsc -s 1 -v                                       -> executes authentication and shows sector 1 data with default key");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("v",  "verbose", "show internal data."),
-        arg_lit0("b",  "keyb",    "use key B (by default keyA)."),
-        arg_lit0("p",  "plain",   "plain communication mode between reader and card."),
-        arg_int1("s",  "sn",      "<dec>", "sector number (0..255)"),
-        arg_str0("k",  "key",     "<hex>", "key, 16 hex bytes"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_lit0("b",  "keyb",    "Use key B (def: keyA)"),
+        arg_lit0("p",  "plain",   "Plain communication mode between reader and card"),
+        arg_int1("s",  "sn",      "<0..255>", "Sector number"),
+        arg_str0("k",  "key",     "<hex>", "Key, 16 hex bytes"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -893,11 +893,11 @@ static int CmdHFMFPWrbl(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("v",  "verbose", "show internal data."),
-        arg_lit0("b",  "keyb",    "use key B (by default keyA)."),
-        arg_int1(NULL, "blk",     "<dec>", "block number (0..255)"),
-        arg_str1("d",  "data",    "<hex>", "data, 16 hex bytes"),
-        arg_str0("k",  "key",     "<hex>", "key, 16 hex bytes"),
+        arg_lit0("v",  "verbose", "Verbose mode"),        
+        arg_lit0("b",  "keyb",    "Use key B (def: keyA)"),
+        arg_int1(NULL, "blk",     "<0..255>", "Block number"),
+        arg_str1("d",  "data",    "<hex>", "Data, 16 hex bytes"),
+        arg_str0("k",  "key",     "<hex>", "Key, 16 hex bytes"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -1097,7 +1097,7 @@ static int CmdHFMFPChk(const char *Cmd) {
 
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfp chk",
-                  "Checks keys with Mifare Plus card.",
+                  "Checks keys on MIFARE Plus card",
                   "hf mfp chk -k 000102030405060708090a0b0c0d0e0f  -> check key on sector 0 as key A and B\n"
                   "hf mfp chk -s 2 -a                              -> check default key list on sector 2, key A\n"
                   "hf mfp chk -d mfp_default_keys -s0 -e6          -> check keys from dictionary against sectors 0-6\n"
@@ -1106,17 +1106,17 @@ static int CmdHFMFPChk(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "keya",      "check only key A (by default check all keys)."),
-        arg_lit0("b",  "keyb",      "check only key B (by default check all keys)."),
-        arg_int0("s",  "startsec",  "Start sector Num (0..255)", NULL),
-        arg_int0("e",  "endsec",    "End sector Num (0..255)", NULL),
-        arg_str0("k",  "key",       "<Key>", "Key for checking (HEX 16 bytes)"),
-        arg_str0("d",  "dict",      "<file>", "file with keys dictionary"),
-        arg_lit0(NULL, "pattern1b", "check all 1-byte combinations of key (0000...0000, 0101...0101, 0202...0202, ...)"),
-        arg_lit0(NULL, "pattern2b", "check all 2-byte combinations of key (0000...0000, 0001...0001, 0002...0002, ...)"),
-        arg_str0(NULL, "startp2b",  "<Pattern>", "Start key (2-byte HEX) for 2-byte search (use with `--pattern2b`)"),
-        arg_str0("j",  "json",      "<file>",  "json file to save keys"),
-        arg_lit0("v",  "verbose",   "verbose mode."),
+        arg_lit0("a",  "keya",      "Check only key A (def: check all keys)"),
+        arg_lit0("b",  "keyb",      "Check only key B (def: check all keys)"),
+        arg_int0("s",  "startsec",  "<0..255>", "Start sector number"),
+        arg_int0("e",  "endsec",    "<0..255>", "End sector number"),
+        arg_str0("k",  "key",       "<hex>", "Key for checking (HEX 16 bytes)"),
+        arg_str0("d",  "dict",      "<fn>", "Dictionary file with keys"),
+        arg_lit0(NULL, "pattern1b", "Check all 1-byte combinations of key (0000...0000, 0101...0101, 0202...0202, ...)"),
+        arg_lit0(NULL, "pattern2b", "Check all 2-byte combinations of key (0000...0000, 0001...0001, 0002...0002, ...)"),
+        arg_str0(NULL, "startp2b",  "<pattern>", "Start key (2-byte HEX) for 2-byte search (use with `--pattern2b`)"),
+        arg_str0("j",  "json",      "<fn>",  "Json filename to save keys"),
+        arg_lit0("v",  "verbose",   "Verbose mode"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -1339,12 +1339,12 @@ static int CmdHFMFPMAD(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("v",  "verbose",  "show technical data"),
-        arg_str0(NULL, "aid",      "<aid>", "print all sectors with aid"),
-        arg_str0("k",  "key",      "<key>", "key for printing sectors"),
-        arg_lit0("b",  "keyb",     "use key B for access printing sectors (by default: key A)"),
-        arg_lit0(NULL, "be",       "(optional, BigEndian)"),
-        arg_lit0(NULL, "dch",      "decode Card Holder information"),
+        arg_lit0("v",  "verbose",  "Show technical data"),
+        arg_str0(NULL, "aid",      "<hex>", "Print all sectors with aid"),
+        arg_str0("k",  "key",      "<hex>", "Key for printing sectors"),
+        arg_lit0("b",  "keyb",     "Use key B for access printing sectors (def: key A)"),
+        arg_lit0(NULL, "be",       "(optional: BigEndian)"),
+        arg_lit0(NULL, "dch",      "Decode Card Holder information"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
