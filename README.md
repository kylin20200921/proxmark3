commit 869089c239b16a4cdde0bda3241169f0452bb20b
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Feb 4 21:39:01 2022 +0100

    text

diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index e0e6249b4..961c7146f 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -1451,22 +1451,23 @@ jumptoend:
 static int CmdHF14AMfNestedStatic(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mf staticnested",
-                  "Execute Nested attack against MIFARE Classic card with static nonce for key recovery",
-                  "hf mf staticnested --mini --blk 0 -a -k FFFFFFFFFFFF                   --> Key recovery against MIFARE Mini\n"
-                  "hf mf staticnested --1k --blk 0 -a -k FFFFFFFFFFFF                     --> Key recovery against MIFARE Classic 1k\n"
-                  "hf mf staticnested --2k --blk 0 -a -k FFFFFFFFFFFF                     --> Key recovery against MIFARE 2k\n"
-                  "hf mf staticnested --4k --blk 0 -a -k FFFFFFFFFFFF                     --> Key recovery against MIFARE 4k\n");
+                  "Execute Nested attack against MIFARE Classic card with static nonce for key recovery.\n"
+                  "Supply a known key from one block to recover all keys",
+                  "hf mf staticnested --mini --blk 0 -a -k FFFFFFFFFFFF\n"
+                  "hf mf staticnested --1k --blk 0 -a -k FFFFFFFFFFFF\n"
+                  "hf mf staticnested --2k --blk 0 -a -k FFFFFFFFFFFF\n"
+                  "hf mf staticnested --4k --blk 0 -a -k FFFFFFFFFFFF\n");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str0("k", "key", "<hex>", "Key specified as 12 hex symbols"),
+        arg_str0("k", "key", "<hex>", "Known key (12 hex symbols)"),
         arg_lit0(NULL, "mini", "MIFARE Classic Mini / S20"),
         arg_lit0(NULL, "1k", "MIFARE Classic 1k / S50"),
         arg_lit0(NULL, "2k", "MIFARE Classic/Plus 2k"),
         arg_lit0(NULL, "4k", "MIFARE Classic 4k / S70"),
         arg_int0(NULL, "blk", "<dec>", "Input block number"),
-        arg_lit0("a", NULL, "Input key specified is A key (default)"),
-        arg_lit0("b", NULL, "Input key specified is B key"),
+        arg_lit0("a", NULL, "Input key specified is keyA (def)"),
+        arg_lit0("b", NULL, "Input key specified is keyB"),
         arg_lit0("e", "emukeys", "Fill simulator keys from found keys"),
         arg_lit0(NULL, "dumpkeys", "Dump found keys to file"),
         arg_param_end
