commit 60b0742941eaf7ffe6c2ea0b28ff7deedffca25c
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Feb 12 17:50:44 2022 +0100

    make style

diff --git a/client/src/cmdhfcipurse.c b/client/src/cmdhfcipurse.c
index 27c29c191..ffbed5bd4 100644
--- a/client/src/cmdhfcipurse.c
+++ b/client/src/cmdhfcipurse.c
@@ -1447,10 +1447,10 @@ static int CmdHFCipurseDeleteFile(const char *Cmd) {
 
 static int CmdHFCipurseUpdateKey(const char *Cmd) {
     CLIParserContext *ctx;
-    CLIParserInit(&ctx, "hf cipurse updakey",
-                  "Update key. ",
-                  "hf cipurse updkey --aid 4144204631 --newkeyn 2 --newkeya 00 --newkey 73737373737373737373737373737373 -> update default application key 2 with default value 73..73\n"
-                  "hf cipurse updkey --newkeyn 1 --newkeya 00 --newkey 0102030405060708090a0b0c0d0e0f10 --commit ->  for key 1");
+    CLIParserInit(&ctx, "hf cipurse updkey",
+                  "Update key",
+                  "hf cipurse updkey --aid 4144204631 --newkeyn 2 --newkeya 00 --newkey 73737373737373737373737373737373   -> update default application key 2 with default value 73..73\n"
+                  "hf cipurse updkey --newkeyn 1 --newkeya 00 --newkey 0102030405060708090a0b0c0d0e0f10 --commit           ->  for key 1");
 
     void *argtable[] = {
         arg_param_begin,
diff --git a/doc/commands.json b/doc/commands.json
index 85723c4c9..972730c6e 100644
--- a/doc/commands.json
+++ b/doc/commands.json
@@ -10417,6 +10417,6 @@
     "metadata": {
         "commands_extracted": 606,
         "extracted_by": "PM3Help2JSON v1.00",
-        "extracted_on": "2022-02-12T16:44:39"
+        "extracted_on": "2022-02-12T16:50:14"
     }
 }
\ No newline at end of file
