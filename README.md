commit f1d45b491cdef3c645eac7bc5e18a286c703fb39
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Feb 12 17:42:18 2022 +0100

    text

diff --git a/client/src/cmdhfmfu.c b/client/src/cmdhfmfu.c
index 03be2214e..f85f67ac9 100644
--- a/client/src/cmdhfmfu.c
+++ b/client/src/cmdhfmfu.c
@@ -2212,7 +2212,7 @@ static int CmdHF14AMfUDump(const char *Cmd) {
 
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfu dump",
-                  "Dump MIFARE Ultralight/NTAG tag to binary/json files.\n"
+                  "Dump MIFARE Ultralight/NTAG tag to binary/eml/json files.\n"
                   "It autodetects card type."
                   "Supports:\n"
                   "Ultralight, Ultralight-C, Ultralight EV1\n"
