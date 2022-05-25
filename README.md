commit 77f49c8efcbf45c33ebaec315f35fa6edb16ab35
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 1 09:39:05 2022 +0100

    text - unified

diff --git a/client/src/cmdflashmem.c b/client/src/cmdflashmem.c
index e7a42085b..c2e5591a6 100644
--- a/client/src/cmdflashmem.c
+++ b/client/src/cmdflashmem.c
@@ -167,7 +167,7 @@ static int CmdFlashMemLoad(const char *Cmd) {
         arg_lit0("m", "mifare,mfc", "upload 6 bytes keys (mifare key dictionary)"),
         arg_lit0("i", "iclass", "upload 8 bytes keys (iClass key dictionary)"),
         arg_lit0("t", "t55xx", "upload 4 bytes keys (password dictionary)"),
-        arg_str1("f", "file", "<filename>", "file name"),
+        arg_str1("f", "file", "<fn>", "file name"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -327,7 +327,7 @@ static int CmdFlashMemDump(const char *Cmd) {
         arg_int0("o", "offset", "<dec>", "offset in memory"),
         arg_int0("l", "len", "<dec>", "length"),
         arg_lit0("v", "view", "view dump"),
-        arg_str0("f", "file", "<filename>", "file name"),
+        arg_str0("f", "file", "<fn>", "save filename"),
         arg_int0("c", "cols", "<dec>", "column breaks (def 32)"),
         arg_param_end
     };
diff --git a/client/src/cmdflashmemspiffs.c b/client/src/cmdflashmemspiffs.c
index 684165236..407f1e54c 100644
--- a/client/src/cmdflashmemspiffs.c
+++ b/client/src/cmdflashmemspiffs.c
@@ -196,7 +196,7 @@ static int CmdFlashMemSpiFFSRemove(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str1("f", "filename", "<fn>", "file to remove"),
+        arg_str1("f", "file", "<fn>", "file to remove"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
diff --git a/client/src/cmdhf14b.c b/client/src/cmdhf14b.c
index e6503478f..04e0a1a52 100644
--- a/client/src/cmdhf14b.c
+++ b/client/src/cmdhf14b.c
@@ -1276,7 +1276,7 @@ static int CmdHF14BDump(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str0("f", "file", "<filename>", "(optional) filename,  if no <name> UID will be used as filename"),
+        arg_str0("f", "file", "<fn>", "(optional) filename,  if no <name> UID will be used as filename"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
diff --git a/client/src/cmdhfcryptorf.c b/client/src/cmdhfcryptorf.c
index 5edaa4cdd..1f68b9bd4 100644
--- a/client/src/cmdhfcryptorf.c
+++ b/client/src/cmdhfcryptorf.c
@@ -423,7 +423,7 @@ static int CmdHFCryptoRFELoad(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str1("f", "file", "<filename>", "filename of dump"),
+        arg_str1("f", "file", "<fn>", "filename of dump"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
diff --git a/client/src/cmdhflegic.c b/client/src/cmdhflegic.c
index 4a95bd683..d96fa320f 100644
--- a/client/src/cmdhflegic.c
+++ b/client/src/cmdhflegic.c
@@ -762,7 +762,7 @@ static int CmdLegicDump(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str0("f", "file", "<fn>", "specify a filename for dump file"),
+        arg_str0("f", "file", "<fn>", "Dump filename"),
         arg_lit0(NULL, "de", "deobfuscate dump data (xor with MCC)"),
         arg_param_end
     };
@@ -859,7 +859,7 @@ static int CmdLegicRestore(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str1("f", "file", "<filename>", "specify a filename to restore"),
+        arg_str1("f", "file", "<fn>", "Filename to restore"),
         arg_lit0(NULL, "ob", "obfuscate dump data (xor with MCC)"),
         arg_param_end
     };
@@ -963,7 +963,7 @@ static int CmdLegicELoad(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str1("f", "file", "<filename>", "Specify a filename to restore"),
+        arg_str1("f", "file", "<fn>", "Filename to restore"),
         arg_int0("t", "type", "<dec>", "Tag type to simulate."),
         arg_lit0(NULL, "obfuscate", "Obfuscate dump data (xor with MCC)"),
         arg_param_end
@@ -1032,7 +1032,7 @@ static int CmdLegicESave(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str0("f", "file", "<fn>", "Specify a filename to save"),
+        arg_str0("f", "file", "<fn>", "Filename to save"),
         arg_int0("t", "type", "<dec>", "Tag type"),
         arg_lit0(NULL, "deobfuscate", "De-obfuscate dump data (xor with MCC)"),
         arg_param_end
diff --git a/client/src/cmdhflto.c b/client/src/cmdhflto.c
index 727da2548..71eea185b 100644
--- a/client/src/cmdhflto.c
+++ b/client/src/cmdhflto.c
@@ -729,7 +729,7 @@ static int CmdHfLTRestore(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str1("f", "file", "<filename>", "specify a filename for dumpfile"),
+        arg_str1("f", "file", "<fn>", "specify a filename for dumpfile"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
diff --git a/client/src/cmdhfwaveshare.c b/client/src/cmdhfwaveshare.c
index e99830d49..6e6a4fd30 100644
--- a/client/src/cmdhfwaveshare.c
+++ b/client/src/cmdhfwaveshare.c
@@ -1006,7 +1006,7 @@ static int CmdHF14AWSLoadBmp(const char *Cmd) {
         arg_param_begin,
         arg_int1("m", NULL, "<nr>", modeldesc),
         arg_lit0("s", "save", "save dithered version in filename-[n].bmp, only for RGB BMP"),
-        arg_str1("f", "file", "<filename>", "filename[.bmp] to upload to tag"),
+        arg_str1("f", "file", "<fn>", "specify filename[.bmp] to upload to tag"),
         arg_param_end
     };
 
diff --git a/client/src/cmdlfem4x50.c b/client/src/cmdlfem4x50.c
index 9427a5148..f70dc1de0 100644
--- a/client/src/cmdlfem4x50.c
+++ b/client/src/cmdlfem4x50.c
@@ -200,7 +200,7 @@ int CmdEM4x50ELoad(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str1("f", "filename", "<fn>", "dump filename (bin/eml/json)"),
+        arg_str1("f", "file", "<fn>", "dump filename (bin/eml/json)"),
         arg_param_end
     };
 
@@ -237,7 +237,7 @@ int CmdEM4x50ESave(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str0("f", "file", "<fn>", "save filename"),
+        arg_str0("f", "file", "<fn>", "specifiy filename"),
         arg_param_end
     };
 
@@ -441,7 +441,7 @@ int CmdEM4x50Chk(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str0("f", "file", "<fn>", "dictionary filename"),
+        arg_str0("f", "file", "<fn>", "specify dictionary filename"),
         arg_param_end
     };
 
@@ -749,7 +749,7 @@ int CmdEM4x50Dump(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str0("f", "file", "<fn>", "dump filename (bin/eml/json)"),
+        arg_str0("f", "file", "<fn>", "specify dump filename (bin/eml/json)"),
         arg_str0("p", "pwd", "<hex>", "password, 4 hex bytes, lsb"),
         arg_param_end
     };
@@ -1060,7 +1060,7 @@ int CmdEM4x50Restore(const char *Cmd) {
     void *argtable[] = {
         arg_param_begin,
         arg_str0("u", "uid", "<hex>", "uid, 4 hex bytes, msb"),
-        arg_str0("f", "file", "<fn>", "dump filename (bin/eml/json)"),
+        arg_str0("f", "file", "<fn>", "specify dump filename (bin/eml/json)"),
         arg_str0("p", "pwd", "<hex>", "password, 4 hex bytes, lsb"),
         arg_param_end
     };
diff --git a/client/src/cmdlfhitag.c b/client/src/cmdlfhitag.c
index 92402fc36..fee643c06 100644
--- a/client/src/cmdlfhitag.c
+++ b/client/src/cmdlfhitag.c
@@ -215,7 +215,7 @@ static int CmdLFHitagEload(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str1("f", "file", "<filename>", "filename of dump"),
+        arg_str1("f", "file", "<fn>", "Specfiy dump filename"),
         arg_lit0("1", NULL, "Card type Hitag1"),
         arg_lit0("2", NULL, "Card type Hitag2"),
         arg_lit0("s", NULL, "Card type HitagS"),
@@ -832,7 +832,7 @@ static int CmdLFHitag2Dump(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str0("f", "file", "<fn>", "file name"),
+        arg_str0("f", "file", "<fn>", "specify file name"),
         arg_str0("k", "key", "<hex>", "key, 4 or 6 hex bytes"),
         arg_str0(NULL, "nrar", "<hex>", "nonce / answer reader, 8 hex bytes"),
         arg_param_end
diff --git a/client/src/cmdsmartcard.c b/client/src/cmdsmartcard.c
index f0764bbf5..7384d5a15 100644
--- a/client/src/cmdsmartcard.c
+++ b/client/src/cmdsmartcard.c
@@ -499,7 +499,7 @@ static int CmdSmartUpgrade(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str1("f", "file", "<filename>", "firmware file name"),
+        arg_str1("f", "file", "<fn>", "Specify firmware file name"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
diff --git a/client/src/cmdtrace.c b/client/src/cmdtrace.c
index 55e270d84..dca4920ec 100644
--- a/client/src/cmdtrace.c
+++ b/client/src/cmdtrace.c
@@ -590,7 +590,7 @@ static int CmdTraceLoad(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str1("f", "file", "<filename>", "trace file to load"),
+        arg_str1("f", "file", "<fn>", "Specify trace file to load"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -629,7 +629,7 @@ static int CmdTraceSave(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str1("f", "file", "<filename>", "trace file to save"),
+        arg_str1("f", "file", "<fn>", "Specify trace file to save"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
