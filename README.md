commit b1566bd9ae442eca21effa7b4202a186b4836e65
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jun 20 08:38:33 2021 +0200

    fix coverity CID 349305 and textual changes

diff --git a/client/src/cmdhfcipurse.c b/client/src/cmdhfcipurse.c
index c4d2df247..faeea6dab 100644
--- a/client/src/cmdhfcipurse.c
+++ b/client/src/cmdhfcipurse.c
@@ -65,7 +65,7 @@ static int CmdHFCipurseInfo(const char *Cmd) {
 
     if (sw != 0x9000) {
         if (sw)
-            PrintAndLogEx(INFO, "Not a CIPURSE card! APDU response: %04x - %s", sw, GetAPDUCodeDescription(sw >> 8, sw & 0xff));
+            PrintAndLogEx(INFO, "Not a CIPURSE card. APDU response: %04x - %s", sw, GetAPDUCodeDescription(sw >> 8, sw & 0xff));
         else
             PrintAndLogEx(ERR, "APDU exchange error. Card returns 0x0000.");
 
@@ -98,40 +98,36 @@ static int CmdHFCipurseInfo(const char *Cmd) {
 }
 
 static int CmdHFCipurseAuth(const char *Cmd) {
-    uint8_t buf[APDU_RES_LEN] = {0};
-    size_t len = 0;
-    uint16_t sw = 0;
-    uint8_t keyId = 1;
-    uint8_t key[] = {0x73, 0x73, 0x73, 0x73, 0x73, 0x73, 0x73, 0x73, 0x73, 0x73, 0x73, 0x73, 0x73, 0x73, 0x73, 0x73};
-
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf cipurse auth",
-                  "Authenticate with key ID and key",
-                  "hf cipurse auth      -> Authenticate with keyID=1 and key = 7373...7373\n"
-                  "hf cipurse auth -n 2 -k 65656565656565656565656565656565 -> Authenticate with key\n");
+                  "Authenticate with key ID and key. If no key is supplied, default key of 737373...7373 will be used",
+                  "hf cipurse auth      -> Authenticate with keyID 1, default key\n"
+                  "hf cipurse auth -n 2 -k 65656565656565656565656565656565 -> Authenticate keyID 2 with key\n");
 
     void *argtable[] = {
         arg_param_begin,
         arg_lit0("a",  "apdu",    "show APDU requests and responses"),
         arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyid",   "<dec>", "key id"),
-        arg_str0("k",  "key",     "<hex>", "key for authenticate"),
+        arg_int0("n",  NULL,      "<dec>", "key ID"),
+        arg_str0("k",  "key",     "<hex>", "Auth key"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
 
     bool APDULogging = arg_get_lit(ctx, 1);
     bool verbose = arg_get_lit(ctx, 2);
-    keyId = arg_get_int_def(ctx, 3, 1);
+    uint8_t keyId = arg_get_int_def(ctx, 3, 1);
 
     uint8_t hdata[250] = {0};
     int hdatalen = sizeof(hdata);
     CLIGetHexWithReturn(ctx, 4, hdata, &hdatalen);
     if (hdatalen && hdatalen != 16) {
-        PrintAndLogEx(ERR, _RED_("ERROR:") " key length for AES128 must be 16 bytes only.");
+        PrintAndLogEx(ERR, _RED_("ERROR:") " key length for AES128 must be 16 bytes only");
         CLIParserFree(ctx);
         return PM3_EINVARG;
     }
+    
+    uint8_t key[] = {0x73, 0x73, 0x73, 0x73, 0x73, 0x73, 0x73, 0x73, 0x73, 0x73, 0x73, 0x73, 0x73, 0x73, 0x73, 0x73};
     if (hdatalen)
         memcpy(key, hdata, CIPURSE_AES_KEY_LENGTH);
 
@@ -139,17 +135,26 @@ static int CmdHFCipurseAuth(const char *Cmd) {
 
     CLIParserFree(ctx);
 
+    size_t len = 0;
+    uint16_t sw = 0;
+    uint8_t buf[APDU_RES_LEN] = {0};
+
     int res = CIPURSESelect(true, true, buf, sizeof(buf), &len, &sw);
     if (res != 0 || sw != 0x9000) {
-        PrintAndLogEx(ERR, "Cipurse select " _RED_("error") ". Card returns 0x%04x.", sw);
+        PrintAndLogEx(ERR, "Cipurse select " _RED_("error") ". Card returns 0x%04x", sw);
         DropField();
         return PM3_ESOFT;
     }
 
     uint8_t kvv[CIPURSE_KVV_LENGTH] = {0};
     CipurseCGetKVV(key, kvv);
-    if (verbose)
-        PrintAndLogEx(INFO, "Key id: %d key: %s KVV: %s", keyId, sprint_hex(key, CIPURSE_AES_KEY_LENGTH), sprint_hex_inrow(kvv, CIPURSE_KVV_LENGTH));
+    if (verbose) {
+        PrintAndLogEx(INFO, "Key id" _YELLOW_("%d") " key " _YELLOW_("%s") " KVV " _YELLOW_("%s")
+            , keyId
+            , sprint_hex(key, CIPURSE_AES_KEY_LENGTH)
+            , sprint_hex_inrow(kvv, CIPURSE_KVV_LENGTH)
+            );
+    }
 
     bool bres = CIPURSEChannelAuthenticate(keyId, key, verbose);
 
@@ -161,7 +166,7 @@ static int CmdHFCipurseAuth(const char *Cmd) {
     }
 
     DropField();
-    return bres ? PM3_SUCCESS : PM3_ESOFT;
+    return (bres) ? PM3_SUCCESS : PM3_ESOFT;
 }
 
 static int CLIParseKeyAndSecurityLevels(CLIParserContext *ctx, size_t keyid, size_t sreqid, size_t srespid, uint8_t *key, CipurseChannelSecurityLevel *sreq, CipurseChannelSecurityLevel *sresp) {
@@ -169,7 +174,7 @@ static int CLIParseKeyAndSecurityLevels(CLIParserContext *ctx, size_t keyid, siz
     int hdatalen = sizeof(hdata);
     CLIGetHexWithReturn(ctx, keyid, hdata, &hdatalen);
     if (hdatalen && hdatalen != 16) {
-        PrintAndLogEx(ERR, _RED_("ERROR:") " key length for AES128 must be 16 bytes only.");
+        PrintAndLogEx(ERR, _RED_("ERROR:") " key length for AES128 must be 16 bytes only");
         return PM3_EINVARG;
     }
     if (hdatalen)
@@ -191,7 +196,7 @@ static int CLIParseKeyAndSecurityLevels(CLIParserContext *ctx, size_t keyid, siz
         else if (strcmp(cdata, "enc") == 0 || strcmp(cdata, "encode") == 0 || strcmp(cdata, "encrypted") == 0)
             *sreq = CPSEncrypted;
         else {
-            PrintAndLogEx(ERR, _RED_("ERROR:") " security level can be only: plain|mac|encode.");
+            PrintAndLogEx(ERR, _RED_("ERROR:") " security level can be only: plain | mac | encode");
             return PM3_EINVARG;
         }
     }
@@ -209,7 +214,7 @@ static int CLIParseKeyAndSecurityLevels(CLIParserContext *ctx, size_t keyid, siz
         else if (strcmp(cdata, "enc") == 0 || strcmp(cdata, "encode") == 0 || strcmp(cdata, "encrypted") == 0)
             *sresp = CPSEncrypted;
         else {
-            PrintAndLogEx(ERR, _RED_("ERROR:") " security level can be only: plain|mac|encode.");
+            PrintAndLogEx(ERR, _RED_("ERROR:") " security level can be only: plain | mac | encode");
             return PM3_EINVARG;
         }
     }
@@ -218,24 +223,19 @@ static int CLIParseKeyAndSecurityLevels(CLIParserContext *ctx, size_t keyid, siz
 }
 
 static int CmdHFCipurseReadFile(const char *Cmd) {
-    uint8_t buf[APDU_RES_LEN] = {0};
-    size_t len = 0;
-    uint16_t sw = 0;
-    uint8_t key[] = CIPURSE_DEFAULT_KEY;
-
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf cipurse read",
-                  "Read file by file ID with key ID and key",
-                  "hf cipurse read -f 2ff7   -> Authenticate with keyID=1 and key = 7373...7373 and read file with id 2ff7\n"
-                  "hf cipurse read -n 2 -k 65656565656565656565656565656565 -f 2ff7 -> Authenticate with specified key and read file\n");
+                  "Read file by file ID with key ID and key. If no key is supplied, default key of 737373...7373 will be used",
+                  "hf cipurse read -f 2ff7   -> Authenticate with keyID 1, read file with id 2ff7\n"
+                  "hf cipurse read -n 2 -k 65656565656565656565656565656565 --fid 2ff7 -> Authenticate keyID 2 and read file\n");
 
     void *argtable[] = {
         arg_param_begin,
         arg_lit0("a",  "apdu",    "show APDU requests and responses"),
         arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyid",   "<dec>", "key id"),
-        arg_str0("k",  "key",     "<hex>", "key for authenticate"),
-        arg_str0("f",  "file",    "<hex>", "file ID"),
+        arg_int0("n",  NULL,      "<dec>", "key ID"),
+        arg_str0("k",  "key",     "<hex>", "Auth key"),
+        arg_str0(NULL,  "fid",    "<hex>", "file ID"),
         arg_int0("o",  "offset",  "<dec>", "offset for reading data from file"),
         arg_lit0(NULL, "noauth",  "read file without authentication"),
         arg_str0(NULL, "sreq",    "<plain|mac(default)|encode>", "communication reader-PICC security level"),
@@ -244,28 +244,30 @@ static int CmdHFCipurseReadFile(const char *Cmd) {
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
 
+
     bool APDULogging = arg_get_lit(ctx, 1);
     bool verbose = arg_get_lit(ctx, 2);
     uint8_t keyId = arg_get_int_def(ctx, 3, 1);
 
     CipurseChannelSecurityLevel sreq = CPSMACed;
     CipurseChannelSecurityLevel sresp = CPSMACed;
+    uint8_t key[] = CIPURSE_DEFAULT_KEY;
     int res = CLIParseKeyAndSecurityLevels(ctx, 4, 8, 9, key, &sreq, &sresp);
     if (res) {
         CLIParserFree(ctx);
         return PM3_EINVARG;
     }
 
-    uint16_t fileId = 0x2ff7;
-
     uint8_t hdata[250] = {0};
     int hdatalen = sizeof(hdata);
     CLIGetHexWithReturn(ctx, 5, hdata, &hdatalen);
     if (hdatalen && hdatalen != 2) {
-        PrintAndLogEx(ERR, _RED_("ERROR:") " file id length must be 2 bytes only.");
+        PrintAndLogEx(ERR, _RED_("ERROR:") " file id length must be 2 bytes only");
         CLIParserFree(ctx);
         return PM3_EINVARG;
     }
+
+    uint16_t fileId = 0x2ff7;
     if (hdatalen)
         fileId = (hdata[0] << 8) + hdata[1];
 
@@ -277,9 +279,13 @@ static int CmdHFCipurseReadFile(const char *Cmd) {
 
     CLIParserFree(ctx);
 
+    size_t len = 0;
+    uint16_t sw = 0;
+    uint8_t buf[APDU_RES_LEN] = {0};
+
     res = CIPURSESelect(true, true, buf, sizeof(buf), &len, &sw);
     if (res != 0 || sw != 0x9000) {
-        PrintAndLogEx(ERR, "Cipurse select " _RED_("error") ". Card returns 0x%04x.", sw);
+        PrintAndLogEx(ERR, "Cipurse select " _RED_("error") ". Card returns 0x%04x", sw);
         DropField();
         return PM3_ESOFT;
     }
@@ -303,18 +309,18 @@ static int CmdHFCipurseReadFile(const char *Cmd) {
     res = CIPURSESelectFile(fileId, buf, sizeof(buf), &len, &sw);
     if (res != 0 || sw != 0x9000) {
         if (verbose == false)
-            PrintAndLogEx(ERR, "File select " _RED_("ERROR") ". Card returns 0x%04x.", sw);
+            PrintAndLogEx(ERR, "File select " _RED_("ERROR") ". Card returns 0x%04x", sw);
         DropField();
         return PM3_ESOFT;
     }
 
     if (verbose)
-        PrintAndLogEx(INFO, "Select file 0x%x " _GREEN_("OK"), fileId);
+        PrintAndLogEx(INFO, "Select file 0x%x ( " _GREEN_("ok") " )", fileId);
 
     res = CIPURSEReadBinary(offset, buf, sizeof(buf), &len, &sw);
     if (res != 0 || sw != 0x9000) {
         if (verbose == false)
-            PrintAndLogEx(ERR, "File read " _RED_("ERROR") ". Card returns 0x%04x.", sw);
+            PrintAndLogEx(ERR, "File read " _RED_("ERROR") ". Card returns 0x%04x", sw);
         DropField();
         return PM3_ESOFT;
     }
@@ -322,36 +328,31 @@ static int CmdHFCipurseReadFile(const char *Cmd) {
     if (len == 0)
         PrintAndLogEx(INFO, "File id " _YELLOW_("%x") " is empty", fileId);
     else
-        PrintAndLogEx(INFO, "File id " _YELLOW_("%x") " data[%d]: %s", fileId, len, sprint_hex(buf, len));
+        PrintAndLogEx(INFO, "File id " _YELLOW_("%x") " data[%zu]: %s", fileId, len, sprint_hex(buf, len));
 
     DropField();
     return PM3_SUCCESS;
 }
 
 static int CmdHFCipurseWriteFile(const char *Cmd) {
-    uint8_t buf[APDU_RES_LEN] = {0};
-    size_t len = 0;
-    uint16_t sw = 0;
-    uint8_t key[] = CIPURSE_DEFAULT_KEY;
-
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf cipurse write",
-                  "Write file by file ID with key ID and key",
-                  "hf cipurse write -f 2ff7   -> Authenticate with keyID=1 and key = 7373...7373 and write file with id 2ff7\n"
-                  "hf cipurse write -n 2 -k 65656565656565656565656565656565 -f 2ff7 -> Authenticate with specified key and write file\n");
+                  "Write file by file ID with key ID and key. If no key is supplied, default key of 737373...7373 will be used",
+                  "hf cipurse write -f 2ff7   -> Authenticate with keyID 1, write file with id 2ff7\n"
+                  "hf cipurse write -n 2 -k 65656565656565656565656565656565 --fid 2ff7 -> Authenticate keyID 2 and write file\n");
 
     void *argtable[] = {
         arg_param_begin,
         arg_lit0("a",  "apdu",    "show APDU requests and responses"),
         arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyid",   "<dec>", "key id"),
-        arg_str0("k",  "key",     "<hex>", "key for authenticate"),
-        arg_str0("f",  "file",    "<hex>", "file ID"),
+        arg_int0("n",  NULL,      "<dec>", "key ID"),
+        arg_str0("k",  "key",     "<hex>", "Auth key"),
+        arg_str0(NULL,  "fid",    "<hex>", "file ID"),
         arg_int0("o",  "offset",  "<dec>", "offset for reading data from file"),
         arg_lit0(NULL, "noauth",  "read file without authentication"),
         arg_str0(NULL, "sreq",    "<plain|mac(default)|encode>", "communication reader-PICC security level"),
         arg_str0(NULL, "sresp",   "<plain|mac(default)|encode>", "communication PICC-reader security level"),
-        arg_str0("c",  "content", "<hex>", "new file content"),
+        arg_str0("d",  "data",    "<hex>", "hex data to write to new file"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -362,6 +363,8 @@ static int CmdHFCipurseWriteFile(const char *Cmd) {
 
     CipurseChannelSecurityLevel sreq = CPSMACed;
     CipurseChannelSecurityLevel sresp = CPSMACed;
+
+    uint8_t key[] = CIPURSE_DEFAULT_KEY;
     int res = CLIParseKeyAndSecurityLevels(ctx, 4, 8, 9, key, &sreq, &sresp);
     if (res) {
         CLIParserFree(ctx);
@@ -374,7 +377,7 @@ static int CmdHFCipurseWriteFile(const char *Cmd) {
     int hdatalen = sizeof(hdata);
     CLIGetHexWithReturn(ctx, 5, hdata, &hdatalen);
     if (hdatalen && hdatalen != 2) {
-        PrintAndLogEx(ERR, _RED_("ERROR:") " file id length must be 2 bytes only.");
+        PrintAndLogEx(ERR, _RED_("ERROR:") " file id length must be 2 bytes only");
         CLIParserFree(ctx);
         return PM3_EINVARG;
     }
@@ -388,7 +391,7 @@ static int CmdHFCipurseWriteFile(const char *Cmd) {
     hdatalen = sizeof(hdata);
     CLIGetHexWithReturn(ctx, 10, hdata, &hdatalen);
     if (hdatalen == 0) {
-        PrintAndLogEx(ERR, _RED_("ERROR:") " file content length must be more 0.");
+        PrintAndLogEx(ERR, _RED_("ERROR:") " file content length must be more 0");
         CLIParserFree(ctx);
         return PM3_EINVARG;
     }
@@ -397,15 +400,24 @@ static int CmdHFCipurseWriteFile(const char *Cmd) {
 
     CLIParserFree(ctx);
 
+    size_t len = 0;
+    uint16_t sw = 0;
+    uint8_t buf[APDU_RES_LEN] = {0};
+
     res = CIPURSESelect(true, true, buf, sizeof(buf), &len, &sw);
     if (res != 0 || sw != 0x9000) {
-        PrintAndLogEx(ERR, "Cipurse select " _RED_("error") ". Card returns 0x%04x.", sw);
+        PrintAndLogEx(ERR, "Cipurse select " _RED_("error") ". Card returns 0x%04x", sw);
         DropField();
         return PM3_ESOFT;
     }
 
     if (verbose) {
-        PrintAndLogEx(INFO, "File id: %x offset %d key id: %d key: %s", fileId, offset, keyId, sprint_hex(key, CIPURSE_AES_KEY_LENGTH));
+        PrintAndLogEx(INFO, "File id " _YELLOW_("%x") " offset " _YELLOW_("%zu") " key id " _YELLOW_("%d") " key " _YELLOW_("%s")
+            , fileId
+            , offset
+            , keyId
+            , sprint_hex(key, CIPURSE_AES_KEY_LENGTH)
+            );
         PrintAndLogEx(INFO, "data[%d]: %s", hdatalen, sprint_hex(hdata, hdatalen));
     }
 
@@ -425,52 +437,47 @@ static int CmdHFCipurseWriteFile(const char *Cmd) {
     res = CIPURSESelectFile(fileId, buf, sizeof(buf), &len, &sw);
     if (res != 0 || sw != 0x9000) {
         if (verbose == false)
-            PrintAndLogEx(ERR, "File select " _RED_("ERROR") ". Card returns 0x%04x.", sw);
+            PrintAndLogEx(ERR, "File select " _RED_("ERROR") ". Card returns 0x%04x", sw);
         DropField();
         return PM3_ESOFT;
     }
 
     if (verbose)
-        PrintAndLogEx(INFO, "Select file 0x%x " _GREEN_("OK"), fileId);
+        PrintAndLogEx(INFO, "Select file 0x%x ( " _GREEN_("ok") " )", fileId);
 
     res = CIPURSEUpdateBinary(offset, hdata, hdatalen, buf, sizeof(buf), &len, &sw);
     if (res != 0 || sw != 0x9000) {
         if (verbose == false)
-            PrintAndLogEx(ERR, "File write " _RED_("ERROR") ". Card returns 0x%04x.", sw);
+            PrintAndLogEx(ERR, "File write " _RED_("ERROR") ". Card returns 0x%04x", sw);
         DropField();
         return PM3_ESOFT;
     }
 
-    PrintAndLogEx(INFO, "File id " _YELLOW_("%x") " successfully written.", fileId);
+    PrintAndLogEx(INFO, "File id " _YELLOW_("%x") " successfully written", fileId);
 
     DropField();
     return PM3_SUCCESS;
 }
 
 static int CmdHFCipurseReadFileAttr(const char *Cmd) {
-    uint8_t buf[APDU_RES_LEN] = {0};
-    size_t len = 0;
-    uint16_t sw = 0;
-    uint8_t key[] = CIPURSE_DEFAULT_KEY;
-
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf cipurse aread",
-                  "Read file attributes by file ID with key ID and key",
-                  "hf cipurse aread -f 2ff7   -> Authenticate with keyID=1 and key = 7373...7373 and read file attributes with id 2ff7\n"
-                  "hf cipurse aread -n 2 -k 65656565656565656565656565656565 -f 2ff7 -> Authenticate with specified key and read file attributes\n");
+                  "Read file attributes by file ID with key ID and key. If no key is supplied, default key of 737373...7373 will be used",
+                  "hf cipurse aread -f 2ff7   -> Authenticate with keyID 1, read file attributes with id 2ff7\n"
+                  "hf cipurse aread -n 2 -k 65656565656565656565656565656565 --fid 2ff7 -> Authenticate keyID 2, read file attributes\n");
 
     void *argtable[] = {
         arg_param_begin,
         arg_lit0("a",  "apdu",    "show APDU requests and responses"),
         arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyid",   "<dec>", "key id"),
-        arg_str0("k",  "key",     "<hex>", "key for authenticate"),
-        arg_str0("f",  "file",    "<hex>", "file ID"),
+        arg_int0("n",  NULL,      "<dec>", "key ID"),
+        arg_str0("k",  "key",     "<hex>", "Auth key"),
+        arg_str0(NULL, "fid",     "<hex>", "file ID"),
         arg_lit0(NULL, "noauth",  "read file attributes without authentication"),
         arg_str0(NULL, "sreq",    "<plain|mac(default)|encode>", "communication reader-PICC security level"),
         arg_str0(NULL, "sresp",   "<plain|mac(default)|encode>", "communication PICC-reader security level"),
-        arg_lit0(NULL,  "sel-adf", "show info about ADF itself"),
-        arg_lit0(NULL,  "sel-mf", "show info about master file"),
+        arg_lit0(NULL, "sel-adf", "show info about ADF itself"),
+        arg_lit0(NULL, "sel-mf",  "show info about master file"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -481,27 +488,27 @@ static int CmdHFCipurseReadFileAttr(const char *Cmd) {
 
     CipurseChannelSecurityLevel sreq = CPSMACed;
     CipurseChannelSecurityLevel sresp = CPSMACed;
+    uint8_t key[] = CIPURSE_DEFAULT_KEY;
     int res = CLIParseKeyAndSecurityLevels(ctx, 4, 7, 8, key, &sreq, &sresp);
     if (res) {
         CLIParserFree(ctx);
         return PM3_EINVARG;
     }
 
-    uint16_t fileId = 0x2ff7;
-
     uint8_t hdata[250] = {0};
     int hdatalen = sizeof(hdata);
     CLIGetHexWithReturn(ctx, 5, hdata, &hdatalen);
     if (hdatalen && hdatalen != 2) {
-        PrintAndLogEx(ERR, _RED_("ERROR:") " file id length must be 2 bytes only.");
+        PrintAndLogEx(ERR, _RED_("ERROR:") " file id length must be 2 bytes only");
         CLIParserFree(ctx);
         return PM3_EINVARG;
     }
+
+    uint16_t fileId = 0x2ff7;
     if (hdatalen)
         fileId = (hdata[0] << 8) + hdata[1];
 
     bool noAuth = arg_get_lit(ctx, 6);
-
     bool seladf = arg_get_lit(ctx, 9);
     bool selmf = arg_get_lit(ctx, 10);
 
@@ -509,15 +516,24 @@ static int CmdHFCipurseReadFileAttr(const char *Cmd) {
 
     CLIParserFree(ctx);
 
+    uint8_t buf[APDU_RES_LEN] = {0};
+    size_t len = 0;
+    uint16_t sw = 0;
+
     res = CIPURSESelect(true, true, buf, sizeof(buf), &len, &sw);
     if (res != 0 || sw != 0x9000) {
-        PrintAndLogEx(ERR, "Cipurse select " _RED_("error") ". Card returns 0x%04x.", sw);
+        PrintAndLogEx(ERR, "Cipurse select " _RED_("error") ". Card returns 0x%04x", sw);
         DropField();
         return PM3_ESOFT;
     }
 
-    if (verbose)
-        PrintAndLogEx(INFO, "File id: %x key id: %d key: %s", fileId, keyId, sprint_hex(key, CIPURSE_AES_KEY_LENGTH));
+    if (verbose) {
+        PrintAndLogEx(INFO, "File id " _YELLOW_("%x") " key id " _YELLOW_("%d") " key " _YELLOW_("%s")
+            , fileId
+            , keyId
+            , sprint_hex(key, CIPURSE_AES_KEY_LENGTH)
+            );
+    }
 
     if (noAuth == false) {
         bool bres = CIPURSEChannelAuthenticate(keyId, key, verbose);
@@ -540,19 +556,19 @@ static int CmdHFCipurseReadFileAttr(const char *Cmd) {
 
         if (res != 0 || sw != 0x9000) {
             if (verbose == false)
-                PrintAndLogEx(ERR, "File select " _RED_("ERROR") ". Card returns 0x%04x.", sw);
+                PrintAndLogEx(ERR, "File select " _RED_("ERROR") ". Card returns 0x%04x", sw);
             DropField();
             return PM3_ESOFT;
         }
     }
 
     if (verbose)
-        PrintAndLogEx(INFO, "Select file 0x%x " _GREEN_("OK"), fileId);
+        PrintAndLogEx(INFO, "Select file 0x%x ( " _GREEN_("ok") " )", fileId);
 
     res = CIPURSEReadFileAttributes(buf, sizeof(buf), &len, &sw);
     if (res != 0 || sw != 0x9000) {
         if (verbose == false)
-            PrintAndLogEx(ERR, "File read " _RED_("ERROR") ". Card returns 0x%04x.", sw);
+            PrintAndLogEx(ERR, "File read " _RED_("ERROR") ". Card returns 0x%04x", sw);
         DropField();
         return PM3_ESOFT;
     }
@@ -564,7 +580,7 @@ static int CmdHFCipurseReadFileAttr(const char *Cmd) {
     }
 
     if (verbose)
-        PrintAndLogEx(INFO, "File id " _YELLOW_("%x") " attributes[%d]: %s", fileId, len, sprint_hex(buf, len));
+        PrintAndLogEx(INFO, "File id " _YELLOW_("%x") " attributes[%zu]: %s", fileId, len, sprint_hex(buf, len));
 
     CIPURSEPrintFileAttr(buf, len);
 
@@ -573,24 +589,19 @@ static int CmdHFCipurseReadFileAttr(const char *Cmd) {
 }
 
 static int CmdHFCipurseDeleteFile(const char *Cmd) {
-    uint8_t buf[APDU_RES_LEN] = {0};
-    size_t len = 0;
-    uint16_t sw = 0;
-    uint8_t key[] = CIPURSE_DEFAULT_KEY;
-
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf cipurse delete",
-                  "Read file by file ID with key ID and key",
-                  "hf cipurse delete -f 2ff7   -> Authenticate with keyID=1 and key = 7373...7373 and delete file with id 2ff7\n"
-                  "hf cipurse delete -n 2 -k 65656565656565656565656565656565 -f 2ff7 -> Authenticate with specified key and delete file\n");
+                  "Read file by file ID with key ID and key. If no key is supplied, default key of 737373...7373 will be used",
+                  "hf cipurse delete -f 2ff7   -> Authenticate with keyID 1, delete file with id 2ff7\n"
+                  "hf cipurse delete -n 2 -k 65656565656565656565656565656565 --fid 2ff7 -> Authenticate keyID 2 and delete file\n");
 
     void *argtable[] = {
         arg_param_begin,
         arg_lit0("a",  "apdu",    "show APDU requests and responses"),
         arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyid",   "<dec>", "key id"),
-        arg_str0("k",  "key",     "<hex>", "key for authenticate"),
-        arg_str0("f",  "file",    "<hex>", "file ID"),
+        arg_int0("n",  NULL,      "<dec>", "key ID"),
+        arg_str0("k",  "key",     "<hex>", "Auth key"),
+        arg_str0(NULL, "fid",     "<hex>", "file ID"),
         arg_str0(NULL, "sreq",    "<plain|mac(default)|encode>", "communication reader-PICC security level"),
         arg_str0(NULL, "sresp",   "<plain|mac(default)|encode>", "communication PICC-reader security level"),
         arg_param_end
@@ -603,22 +614,23 @@ static int CmdHFCipurseDeleteFile(const char *Cmd) {
 
     CipurseChannelSecurityLevel sreq = CPSMACed;
     CipurseChannelSecurityLevel sresp = CPSMACed;
+    uint8_t key[] = CIPURSE_DEFAULT_KEY;
     int res = CLIParseKeyAndSecurityLevels(ctx, 4, 6, 7, key, &sreq, &sresp);
     if (res) {
         CLIParserFree(ctx);
         return PM3_EINVARG;
     }
 
-    uint16_t fileId = 0x2ff7;
-
     uint8_t hdata[250] = {0};
     int hdatalen = sizeof(hdata);
     CLIGetHexWithReturn(ctx, 5, hdata, &hdatalen);
     if (hdatalen && hdatalen != 2) {
-        PrintAndLogEx(ERR, _RED_("ERROR:") " file id length must be 2 bytes only.");
+        PrintAndLogEx(ERR, _RED_("ERROR:") " file id length must be 2 bytes only");
         CLIParserFree(ctx);
         return PM3_EINVARG;
     }
+
+    uint16_t fileId = 0x2ff7;
     if (hdatalen)
         fileId = (hdata[0] << 8) + hdata[1];
 
@@ -626,15 +638,24 @@ static int CmdHFCipurseDeleteFile(const char *Cmd) {
 
     CLIParserFree(ctx);
 
+    uint8_t buf[APDU_RES_LEN] = {0};
+    size_t len = 0;
+    uint16_t sw = 0;
+
     res = CIPURSESelect(true, true, buf, sizeof(buf), &len, &sw);
     if (res != 0 || sw != 0x9000) {
-        PrintAndLogEx(ERR, "Cipurse select " _RED_("error") ". Card returns 0x%04x.", sw);
+        PrintAndLogEx(ERR, "Cipurse select " _RED_("error") ". Card returns 0x%04x", sw);
         DropField();
         return PM3_ESOFT;
     }
 
-    if (verbose)
-        PrintAndLogEx(INFO, "File id: %x key id: %d key: %s", fileId, keyId, sprint_hex(key, CIPURSE_AES_KEY_LENGTH));
+    if (verbose) {
+        PrintAndLogEx(INFO, "File id " _YELLOW_("%x") " key id " _YELLOW_("%d") " key " _YELLOW_("%s")
+            , fileId
+            , keyId
+            , sprint_hex(key, CIPURSE_AES_KEY_LENGTH)
+            );
+    }
 
     bool bres = CIPURSEChannelAuthenticate(keyId, key, verbose);
     if (bres == false) {
@@ -650,7 +671,7 @@ static int CmdHFCipurseDeleteFile(const char *Cmd) {
     res = CIPURSEDeleteFile(fileId, buf, sizeof(buf), &len, &sw);
     if (res != 0 || sw != 0x9000) {
         if (verbose == false)
-            PrintAndLogEx(ERR, "File select " _RED_("ERROR") ". Card returns 0x%04x.", sw);
+            PrintAndLogEx(ERR, "File select " _RED_("ERROR") ". Card returns 0x%04x", sw);
         DropField();
         return PM3_ESOFT;
     }
@@ -662,16 +683,6 @@ static int CmdHFCipurseDeleteFile(const char *Cmd) {
 }
 
 
-
-
-
-
-
-
-
-
-
-
 bool CheckCardCipurse(void) {
     uint8_t buf[APDU_RES_LEN] = {0};
     size_t len = 0;
@@ -683,12 +694,12 @@ bool CheckCardCipurse(void) {
 
 static command_t CommandTable[] = {
     {"help",      CmdHelp,                   AlwaysAvailable, "This help."},
-    {"info",      CmdHFCipurseInfo,          IfPm3Iso14443a,  "Info about Cipurse tag."},
-    {"auth",      CmdHFCipurseAuth,          IfPm3Iso14443a,  "Authentication."},
-    {"read",      CmdHFCipurseReadFile,      IfPm3Iso14443a,  "Read binary file."},
-    {"write",     CmdHFCipurseWriteFile,     IfPm3Iso14443a,  "Write binary file."},
-    {"aread",     CmdHFCipurseReadFileAttr,  IfPm3Iso14443a,  "Read file attributes."},
-    {"delete",    CmdHFCipurseDeleteFile,    IfPm3Iso14443a,  "Delete file."},
+    {"info",      CmdHFCipurseInfo,          IfPm3Iso14443a,  "Get info about CIPURSE tag"},
+    {"auth",      CmdHFCipurseAuth,          IfPm3Iso14443a,  "Authenticate CIPURSE tag"},
+    {"read",      CmdHFCipurseReadFile,      IfPm3Iso14443a,  "Read binary file"},
+    {"write",     CmdHFCipurseWriteFile,     IfPm3Iso14443a,  "Write binary file"},
+    {"aread",     CmdHFCipurseReadFileAttr,  IfPm3Iso14443a,  "Read file attributes"},
+    {"delete",    CmdHFCipurseDeleteFile,    IfPm3Iso14443a,  "Delete file"},
     {NULL, NULL, 0, NULL}
 };
 
