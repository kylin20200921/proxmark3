commit b17a311c66c9342e9cc8537cb774d76a53976dd2
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jun 20 07:58:29 2021 +0200

    various coverity fixes

diff --git a/client/src/cmdhfcipurse.c b/client/src/cmdhfcipurse.c
index 6ce7c8895..bc1651b6a 100644
--- a/client/src/cmdhfcipurse.c
+++ b/client/src/cmdhfcipurse.c
@@ -155,9 +155,9 @@ static int CmdHFCipurseAuth(const char *Cmd) {
 
     if (verbose == false) {
         if (bres)
-            PrintAndLogEx(INFO, "Authentication " _GREEN_("OK"));
+            PrintAndLogEx(INFO, "Authentication ( " _GREEN_("ok") " )");
         else
-            PrintAndLogEx(ERR, "Authentication " _RED_("ERROR"));
+            PrintAndLogEx(ERR, "Authentication ( " _RED_("fail") " )");
     }
 
     DropField();
@@ -170,7 +170,6 @@ static int CLIParseKeyAndSecurityLevels(CLIParserContext *ctx, size_t keyid, siz
     CLIGetHexWithReturn(ctx, keyid, hdata, &hdatalen);
     if (hdatalen && hdatalen != 16) {
         PrintAndLogEx(ERR, _RED_("ERROR:") " key length for AES128 must be 16 bytes only.");
-        CLIParserFree(ctx);
         return PM3_EINVARG;
     }
     if (hdatalen)
@@ -292,7 +291,7 @@ static int CmdHFCipurseReadFile(const char *Cmd) {
         bool bres = CIPURSEChannelAuthenticate(keyId, key, verbose);
         if (bres == false) {
             if (verbose == false)
-                PrintAndLogEx(ERR, "Authentication " _RED_("ERROR"));
+                PrintAndLogEx(ERR, "Authentication ( " _RED_("fail") " )");
             DropField();
             return PM3_ESOFT;
         }
@@ -321,9 +320,9 @@ static int CmdHFCipurseReadFile(const char *Cmd) {
     }
 
     if (len == 0)
-        PrintAndLogEx(INFO, "File id: %x is empty", fileId);
+        PrintAndLogEx(INFO, "File id " _YELLOW_("%x") " is empty", fileId);
     else
-        PrintAndLogEx(INFO, "File id: %x data[%d]: %s", fileId, len, sprint_hex(buf, len));
+        PrintAndLogEx(INFO, "File id " _YELLOW_("%x") " data[%d]: %s", fileId, len, sprint_hex(buf, len));
 
     DropField();
     return PM3_SUCCESS;
@@ -414,7 +413,7 @@ static int CmdHFCipurseWriteFile(const char *Cmd) {
         bool bres = CIPURSEChannelAuthenticate(keyId, key, verbose);
         if (bres == false) {
             if (verbose == false)
-                PrintAndLogEx(ERR, "Authentication " _RED_("ERROR"));
+                PrintAndLogEx(ERR, "Authentication ( " _RED_("fail") " )");
             DropField();
             return PM3_ESOFT;
         }
@@ -442,7 +441,7 @@ static int CmdHFCipurseWriteFile(const char *Cmd) {
         return PM3_ESOFT;
     }
 
-    PrintAndLogEx(INFO, "File id: %x successfully written.", fileId);
+    PrintAndLogEx(INFO, "File id " _YELLOW_("%x") " successfully written.", fileId);
 
     DropField();
     return PM3_SUCCESS;
@@ -524,7 +523,7 @@ static int CmdHFCipurseReadFileAttr(const char *Cmd) {
         bool bres = CIPURSEChannelAuthenticate(keyId, key, verbose);
         if (bres == false) {
             if (verbose == false)
-                PrintAndLogEx(ERR, "Authentication " _RED_("ERROR"));
+                PrintAndLogEx(ERR, "Authentication ( " _RED_("fail") " )");
             DropField();
             return PM3_ESOFT;
         }
@@ -559,13 +558,13 @@ static int CmdHFCipurseReadFileAttr(const char *Cmd) {
     }
 
     if (len == 0) {
-        PrintAndLogEx(WARNING, "File id: %x attributes is empty", fileId);
+        PrintAndLogEx(WARNING, "File id " _YELLOW_("%x") " attributes is empty", fileId);
         DropField();
         return PM3_SUCCESS;
     }
 
     if (verbose)
-        PrintAndLogEx(INFO, "File id: %x attributes[%d]: %s", fileId, len, sprint_hex(buf, len));
+        PrintAndLogEx(INFO, "File id " _YELLOW_("%x") " attributes[%d]: %s", fileId, len, sprint_hex(buf, len));
 
     CIPURSEPrintFileAttr(buf, len);
 
@@ -640,7 +639,7 @@ static int CmdHFCipurseDeleteFile(const char *Cmd) {
     bool bres = CIPURSEChannelAuthenticate(keyId, key, verbose);
     if (bres == false) {
         if (verbose == false)
-            PrintAndLogEx(ERR, "Authentication " _RED_("ERROR"));
+            PrintAndLogEx(ERR, "Authentication ( " _RED_("fail") " )");
         DropField();
         return PM3_ESOFT;
     }
@@ -656,7 +655,7 @@ static int CmdHFCipurseDeleteFile(const char *Cmd) {
         return PM3_ESOFT;
     }
 
-    PrintAndLogEx(INFO, "File id: 04x deleted " _GREEN_("succesfully"), fileId);
+    PrintAndLogEx(INFO, "File id " _YELLOW_("%04x") " deleted " _GREEN_("succesfully"), fileId);
 
     DropField();
     return PM3_SUCCESS;
