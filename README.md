commit f59f8dd5288ef40adc0747c7b53193a06a0ece71
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Mar 20 07:47:27 2022 +0100

    hf mf restore accepts 4,7,10 hex bytes UID

diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index 001031e19..cfbe2f549 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -962,7 +962,7 @@ static int CmdHF14AMfRestore(const char *Cmd) {
         arg_lit0(NULL, "1k", "MIFARE Classic 1k / S50 (def)"),
         arg_lit0(NULL, "2k", "MIFARE Classic/Plus 2k"),
         arg_lit0(NULL, "4k", "MIFARE Classic 4k / S70"),
-        arg_str0("u", "uid",  "<hex>", "uid, 6 hex bytes"),
+        arg_str0("u", "uid",  "<hex>", "uid, (4|7|10 hex bytes)"),
         arg_str0("f", "file", "<fn>", "specify dump filename (bin/eml/json)"),
         arg_str0("k", "kfn",  "<fn>", "key filename"),
         arg_lit0(NULL, "ka",  "use specified keyfile to authenticate"),
@@ -976,7 +976,7 @@ static int CmdHF14AMfRestore(const char *Cmd) {
     bool m4 = arg_get_lit(ctx, 4);
 
     int uidlen = 0;
-    char uid[14] = {0};
+    char uid[20] = {0};
     CLIParamStrToBuf(arg_get_str(ctx, 5), (uint8_t *)uid, sizeof(uid), &uidlen);
 
     int datafnlen = 0;
