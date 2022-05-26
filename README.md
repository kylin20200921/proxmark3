commit 83797db14ac80eeacde07a0e49481d3ee7457140
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Feb 7 22:35:00 2022 +0100

    unify helptext

diff --git a/client/src/cmdhfcipurse.c b/client/src/cmdhfcipurse.c
index 44475bf55..622197716 100644
--- a/client/src/cmdhfcipurse.c
+++ b/client/src/cmdhfcipurse.c
@@ -193,25 +193,30 @@ static int CLIParseCommandParametersEx(CLIParserContext *ctx, size_t keyid, size
     uint8_t hdata[250] = {0};
     int hdatalen = sizeof(hdata);
     if (keyid) {
-        if (CLIParamHexToBuf(arg_get_str(ctx, keyid), hdata, hdatalen, &hdatalen))
+        if (CLIParamHexToBuf(arg_get_str(ctx, keyid), hdata, hdatalen, &hdatalen)) {
             return PM3_ESOFT;
+        }
 
         if (hdatalen && hdatalen != 16) {
             PrintAndLogEx(ERR, _RED_("ERROR:") " key length for AES128 must be 16 bytes only");
             return PM3_EINVARG;
         }
+
         if (hdatalen)
             memcpy(key, hdata, CIPURSE_AES_KEY_LENGTH);
         else
             memcpy(key, defaultKey, sizeof(defaultKey));
     }
 
-    if (useaid)
+    if (useaid) {
         *useaid = false;
+    }
+
     if (aidid && aid && aidlen) {
         hdatalen = sizeof(hdata);
-        if (CLIParamHexToBuf(arg_get_str(ctx, aidid), hdata, hdatalen, &hdatalen))
+        if (CLIParamHexToBuf(arg_get_str(ctx, aidid), hdata, hdatalen, &hdatalen)) {
             return PM3_ESOFT;
+        }
             
         if (hdatalen && (hdatalen < 1 || hdatalen > 16)) {
             PrintAndLogEx(ERR, _RED_("ERROR:") " application id length must be 1-16 bytes only");
@@ -222,16 +227,19 @@ static int CLIParseCommandParametersEx(CLIParserContext *ctx, size_t keyid, size
         if (hdatalen) {
             memcpy(aid, hdata, hdatalen);
             *aidlen = hdatalen;
-            if (useaid)
+            if (useaid) {
                 *useaid = true;
+            }
         } else {
             memcpy(aid, defaultAID, defaultAIDLength);
             *aidlen = defaultAIDLength;
         }
     }
 
-    if (usefid)
+    if (usefid) {
         *usefid = false;
+    }
+
     if (fidid && fid) {
         hdatalen = sizeof(hdata);
         if (CLIParamHexToBuf(arg_get_str(ctx, fidid), hdata, hdatalen, &hdatalen))
@@ -330,56 +338,75 @@ static int SelectCommandEx(bool selectDefaultFile, bool useAID, uint8_t *aid, si
     int res = 0;
     if (verbose && selChildFile)
         PrintAndLogEx(INFO, "Select top level application/file");
+
     if (useAID && aidLen > 0) {
+
         res = CIPURSESelectAID(true, true, aid, aidLen, buf, bufSize, len, sw);
         if (res != 0 || *sw != 0x9000) {
-            if (verbose)
+            if (verbose) {
                 PrintAndLogEx(ERR, "Cipurse select application " _GREEN_("%s ") _RED_("error") ". Card returns 0x%04x", sprint_hex_inrow(aid, aidLen), *sw);
+            }
             return PM3_ESOFT;
         }
-        if (verbose)
+        if (verbose) {
             PrintAndLogEx(INFO, "Cipurse select application " _CYAN_("%s ") _GREEN_("OK"), sprint_hex_inrow(aid, aidLen));
+        }
+
     } else if (useFID) {
+
         res = CIPURSESelectFileEx(true, true, fileId, buf, bufSize, len, sw);
         if (res != 0 || *sw != 0x9000) {
-            if (verbose)
+            if (verbose) {
                 PrintAndLogEx(ERR, "Cipurse select file 0x%04x " _RED_("error") ". Card returns 0x%04x", fileId, *sw);
+            }
             return PM3_ESOFT;
         }
-        if (verbose)
+        if (verbose) {
             PrintAndLogEx(INFO, "Cipurse select file " _CYAN_("0x%04x ") _GREEN_("OK"), fileId);
+        }
+
     } else if (selectDefaultFile) {
+
         res = CIPURSESelectMFDefaultFileEx(true, true, buf, bufSize, len, sw);
         if (res != 0 || *sw != 0x9000) {
-            if (verbose)
+            if (verbose) {
                 PrintAndLogEx(ERR, "Cipurse select default file " _RED_("error") ". Card returns 0x%04x", *sw);
+            }
             return PM3_ESOFT;
         }
-        if (verbose)
+        if (verbose) {
             PrintAndLogEx(INFO, "Cipurse select default file " _GREEN_("OK"));
+        }
+
     } else {
+
         res = CIPURSESelect(true, true, buf, bufSize, len, sw);
         if (res != 0 || *sw != 0x9000) {
-            if (verbose)
+            if (verbose) {
                 PrintAndLogEx(ERR, "Cipurse select default application " _RED_("error") ". Card returns 0x%04x", *sw);
+            }
             return PM3_ESOFT;
         }
-        if (verbose)
+        if (verbose) {
             PrintAndLogEx(INFO, "Cipurse select default application " _GREEN_("OK"));
+        }
     }
 
     if (selChildFile) {
-        if (verbose)
+        if (verbose) {
             PrintAndLogEx(INFO, "Select child file");
+        }
 
         res = CIPURSESelectFileEx(false, true, childFileId, buf, bufSize, len, sw);
         if (res != 0 || *sw != 0x9000) {
-            if (verbose)
+            if (verbose) {
                 PrintAndLogEx(ERR, "Select child file 0x%04x " _RED_("error") ". Card returns 0x%04x", childFileId, *sw);
+            }
             return PM3_ESOFT;
         }
-        if (verbose)
+        if (verbose) {
             PrintAndLogEx(INFO, "Select child file " _CYAN_("0x%04x ") _GREEN_("OK"), childFileId);
+        }
     }
 
     return PM3_SUCCESS;
@@ -401,13 +428,13 @@ static int CmdHFCipurseSelect(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
         arg_lit0("t",  "tlv",     "TLV decode returned data"),
-        arg_str0(NULL, "aid",     "<hex 1..16 bytes>", "application ID (AID)"),
-        arg_str0(NULL, "fid",     "<hex 2 bytes>", "top level file (or application) ID (FID)"),
-        arg_lit0(NULL, "mfd",     "select masterfile by empty id"),
-        arg_str0(NULL, "chfid",   "<hex 2 bytes>", "child file ID (EF under application/master file)"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (AID) 1..16 bytes"),
+        arg_str0(NULL, "fid",     "<hex>", "Top level file (or application) ID (FID) 2 bytes"),
+        arg_lit0(NULL, "mfd",     "Select masterfile by empty id"),
+        arg_str0(NULL, "chfid",   "<hex>", "Child file ID (EF under application/master file) 2 bytes"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -467,12 +494,12 @@ static int CmdHFCipurseAuth(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_str0(NULL, "aid",     "<hex 1..16 bytes>", "application ID (AID)"),
-        arg_str0(NULL, "fid",     "<hex 2 bytes>", "top file/application ID (FID)"),
-        arg_lit0(NULL, "mfd",     "select masterfile by empty id"),
-        arg_int0("n",  NULL,      "<dec>", "key ID"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (AID) ( 1..16 bytes )"),
+        arg_str0(NULL, "fid",     "<hex>", "Top file/application ID (FID) ( 2 bytes )"),
+        arg_lit0(NULL, "mfd",     "Select masterfile by empty id"),
+        arg_int0("n",  NULL,      "<dec>", "Key ID"),
         arg_str0("k",  "key",     "<hex>", "Auth key"),
         arg_param_end
     };
@@ -538,21 +565,21 @@ static int CmdHFCipurseReadFile(const char *Cmd) {
     CLIParserInit(&ctx, "hf cipurse read",
                   "Read file in the application by file ID with key ID and key. If no key is supplied, default key of 737373...7373 will be used",
                   "hf cipurse read --fid 2ff7   -> Authenticate with keyID 1, read file with id 2ff7\n"
-                  "hf cipurse read -n 2 -k 65656565656565656565656565656565 --fid 2ff7 -> Authenticate keyID 2 and read file\n"
-                  "hf cipurse read --aid 4144204631 --fid 0102 -> read file with id 0102 from application 4144204631\n");
+                  "hf cipurse read -n 2 -k 65656565656565656565656565656565 --fid 2ff7    -> Authenticate keyID 2 and read file\n"
+                  "hf cipurse read --aid 4144204631 --fid 0102    -> read file with id 0102 from application 4144204631\n");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  NULL,      "<dec>", "key ID"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  NULL,      "<dec>", "Key ID"),
         arg_str0("k",  "key",     "<hex>", "Auth key"),
-        arg_str0(NULL, "aid",     "<hex 1..16 bytes>", "application ID (AID)"),
-        arg_str0(NULL, "fid",    "<hex>", "file ID"),
-        arg_int0("o",  "offset",  "<dec>", "offset for reading data from file"),
-        arg_lit0(NULL, "noauth",  "read file without authentication"),
-        arg_str0(NULL, "sreq",    "<plain|mac(default)|encode>", "communication reader-PICC security level"),
-        arg_str0(NULL, "sresp",   "<plain|mac(default)|encode>", "communication PICC-reader security level"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (AID) ( 1..16 bytes )"),
+        arg_str0(NULL, "fid",     "<hex>", "File ID"),
+        arg_int0("o",  "offset",  "<dec>", "Offset for reading data from file"),
+        arg_lit0(NULL, "noauth",  "Read file without authentication"),
+        arg_str0(NULL, "sreq",    "<plain|mac|encode>", "Communication reader-PICC security level (def: mac)"),
+        arg_str0(NULL, "sresp",   "<plain|mac|encode>", "Communication PICC-reader security level (def: mac)"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -653,18 +680,18 @@ static int CmdHFCipurseWriteFile(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  NULL,      "<dec>", "key ID"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  NULL,      "<dec>", "Key ID"),
         arg_str0("k",  "key",     "<hex>", "Auth key"),
-        arg_str0(NULL, "aid",     "<hex 1..16 bytes>", "application ID (AID)"),
-        arg_str0(NULL, "fid",    "<hex>", "file ID"),
-        arg_int0("o",  "offset",  "<dec>", "offset for reading data from file"),
-        arg_lit0(NULL, "noauth",  "read file without authentication"),
-        arg_str0(NULL, "sreq",    "<plain|mac(default)|encode>", "communication reader-PICC security level"),
-        arg_str0(NULL, "sresp",   "<plain|mac(default)|encode>", "communication PICC-reader security level"),
-        arg_str0("d",  "data",    "<hex>", "hex data to write to new file"),
-        arg_lit0(NULL, "commit",  "need commit after write"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (AID) ( 1..16 bytes )"),
+        arg_str0(NULL, "fid",     "<hex>", "File ID"),
+        arg_int0("o",  "offset",  "<dec>", "Offset for reading data from file"),
+        arg_lit0(NULL, "noauth",  "Read file without authentication"),
+        arg_str0(NULL, "sreq",    "<plain|mac|encode>", "communication reader-PICC security level (def: mac)"),
+        arg_str0(NULL, "sresp",   "<plain|mac|encode>", "communication PICC-reader security level (def: mac)"),
+        arg_str0("d",  "data",    "<hex>", "Data to write to new file"),
+        arg_lit0(NULL, "commit",  "Commit after write"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -790,17 +817,17 @@ static int CmdHFCipurseReadFileAttr(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  NULL,      "<dec>", "key ID"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  NULL,      "<dec>", "Key ID"),
         arg_str0("k",  "key",     "<hex>", "Auth key"),
-        arg_lit0(NULL, "mfd",     "show info about master file"),
-        arg_str0(NULL, "aid",     "<hex 1..16 bytes>", "select application ID (AID)"),
-        arg_str0(NULL, "fid",     "<hex>", "file ID"),
-        arg_str0(NULL, "chfid",   "<hex 2 bytes>", "child file ID (EF under application/master file)"),
-        arg_lit0(NULL, "noauth",  "read file attributes without authentication"),
-        arg_str0(NULL, "sreq",    "<plain|mac(default)|encode>", "communication reader-PICC security level"),
-        arg_str0(NULL, "sresp",   "<plain|mac(default)|encode>", "communication PICC-reader security level"),
+        arg_lit0(NULL, "mfd",     "Show info about master file"),
+        arg_str0(NULL, "aid",     "<hex>", "Select application ID (AID) ( 1..16 bytes )"),
+        arg_str0(NULL, "fid",     "<hex>", "File ID"),
+        arg_str0(NULL, "chfid",   "<hex>", "Child file ID (EF under application/master file) ( 2 bytes )"),
+        arg_lit0(NULL, "noauth",  "Read file attributes without authentication"),
+        arg_str0(NULL, "sreq",    "<plain|mac|encode>", "Communication reader-PICC security level (def: mac)"),
+        arg_str0(NULL, "sresp",   "<plain|mac|encode>", "Communication PICC-reader security level (def: mac)"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -902,26 +929,26 @@ static int CmdHFCipurseWriteFileAttr(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf cipurse awrite",
                   "Write file attributes by file ID with key ID and key. If no key is supplied, default key of 737373...7373 will be used",
-                  "hf cipurse awrite --fid 2ff7 -d 080000C1C1C1C1C1C1C1C1C1 -> write default file attributes with id 2ff7\n"
-                  "hf cipurse awrite --mfd -d 080000FFFFFFFFFFFFFFFFFF86023232 --commit -> write file attributes for master file (MF)\n"
+                  "hf cipurse awrite --fid 2ff7 -d 080000C1C1C1C1C1C1C1C1C1    -> write default file attributes with id 2ff7\n"
+                  "hf cipurse awrite --mfd -d 080000FFFFFFFFFFFFFFFFFF86023232 --commit    -> write file attributes for master file (MF)\n"
                   "hf cipurse awrite --chfid 0102 -d 020000ffffff  -> write file 0102 attributes in the default application to full access\n"
                   "hf cipurse awrite --chfid 0102 -d 02000040ffff  -> write file 0102 attributes in the default application to full access with keys 1 and 2\n");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  NULL,      "<dec>", "key ID"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  NULL,      "<dec>", "Key ID"),
         arg_str0("k",  "key",     "<hex>", "Auth key"),
-        arg_lit0(NULL, "mfd",     "show info about master file"),
-        arg_str0(NULL, "aid",     "<hex 1..16 bytes>", "select application ID (AID)"),
-        arg_str0(NULL, "fid",     "<hex>", "file ID"),
-        arg_str0(NULL, "chfid",   "<hex 2 bytes>", "child file ID (EF under application/master file)"),
-        arg_lit0(NULL, "noauth",  "read file attributes without authentication"),
-        arg_str0(NULL, "sreq",    "<plain|mac(default)|encode>", "communication reader-PICC security level"),
-        arg_str0(NULL, "sresp",   "<plain|mac(default)|encode>", "communication PICC-reader security level"),
-        arg_str0("d",  "data",    "<hex>", "file attributes"),
-        arg_lit0(NULL, "commit",  "need commit after write"),
+        arg_lit0(NULL, "mfd",     "Show info about master file"),
+        arg_str0(NULL, "aid",     "<hex>", "Select application ID (AID) ( 1..16 bytes )"),
+        arg_str0(NULL, "fid",     "<hex>", "File ID"),
+        arg_str0(NULL, "chfid",   "<hex>", "Child file ID (EF under application/master file) ( 2 bytes )"),
+        arg_lit0(NULL, "noauth",  "Read file attributes without authentication"),
+        arg_str0(NULL, "sreq",    "<plain|mac|encode>", "Communication reader-PICC security level (def: mac)"),
+        arg_str0(NULL, "sresp",   "<plain|mac|encode>", "Communication PICC-reader security level (def: mac)"),
+        arg_str0("d",  "data",    "<hex>", "File attributes"),
+        arg_lit0(NULL, "commit",  "Commit after write"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -1046,13 +1073,13 @@ static int CmdHFCipurseFormatAll(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  NULL,      "<dec>", "key ID"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  NULL,      "<dec>", "Key ID"),
         arg_str0("k",  "key",     "<hex>", "Auth key"),
-        arg_str0(NULL, "sreq",    "<plain|mac(default)|encode>", "communication reader-PICC security level"),
-        arg_str0(NULL, "sresp",   "<plain|mac(default)|encode>", "communication PICC-reader security level"),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
+        arg_str0(NULL, "sreq",    "<plain|mac|encode>", "Communication reader-PICC security level (def: mac)"),
+        arg_str0(NULL, "sresp",   "<plain|mac|encode>", "Communication PICC-reader security level (def: mac)"),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -1131,20 +1158,20 @@ static int CmdHFCipurseCreateDGI(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  NULL,      "<dec>", "key ID"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  NULL,      "<dec>", "Key ID"),
         arg_str0("k",  "key",     "<hex>", "Auth key"),
 
-        arg_str0(NULL, "aid",     "<hex 1..16 bytes>", "application ID (AID)"),
-        arg_str0(NULL, "fid",     "<hex 2 bytes>", "file ID (FID)"),
-        arg_lit0(NULL, "mfd",     "select masterfile by empty id"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (AID) ( 1..16 bytes )"),
+        arg_str0(NULL, "fid",     "<hex>", "file ID (FID) ( 2 bytes )"),
+        arg_lit0(NULL, "mfd",     "Select masterfile by empty id"),
 
-        arg_str0("d",  "data",    "<hex>", "data with DGI for create"),
-        arg_str0(NULL, "sreq",    "<plain|mac(default)|encode>", "communication reader-PICC security level"),
-        arg_str0(NULL, "sresp",   "<plain|mac(default)|encode>", "communication PICC-reader security level"),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
-        arg_lit0(NULL, "commit",  "need commit after create"),
+        arg_str0("d",  "data",    "<hex>", "Data with DGI for create"),
+        arg_str0(NULL, "sreq",    "<plain|mac|encode>", "Communication reader-PICC security level (def: mac)"),
+        arg_str0(NULL, "sresp",   "<plain|mac|encode>", "Communication PICC-reader security level (def: mac)"),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
+        arg_lit0(NULL, "commit",  "Commit after create"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -1265,17 +1292,17 @@ static int CmdHFCipurseDeleteFile(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  NULL,      "<dec>", "key ID"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  NULL,      "<dec>", "Key ID"),
         arg_str0("k",  "key",     "<hex>", "Auth key"),
-        arg_str0(NULL, "fid",     "<hex>", "file/application ID under MF for delete"),
-        arg_str0(NULL, "aid",     "<hex 1..16 bytes>", "application ID (AID) for delete"),
-        arg_str0(NULL, "chfid",   "<hex 2 bytes>", "child file ID (EF under application/master file)"),
-        arg_str0(NULL, "sreq",    "<plain|mac(default)|encode>", "communication reader-PICC security level"),
-        arg_str0(NULL, "sresp",   "<plain|mac(default)|encode>", "communication PICC-reader security level"),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
-        arg_lit0(NULL, "commit",  "commit "),
+        arg_str0(NULL, "fid",     "<hex>", "File/application ID under MF for delete"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (AID) for delete ( 1..16 bytes )"),
+        arg_str0(NULL, "chfid",   "<hex>", "Child file ID (EF under application/master file) ( 2 bytes )"),
+        arg_str0(NULL, "sreq",    "<plain|mac|encode>", "Communication reader-PICC security level (def: mac)"),
+        arg_str0(NULL, "sresp",   "<plain|mac|encode>", "Communication PICC-reader security level (def: mac)"),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
+        arg_lit0(NULL, "commit",  "commit after delete"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -1426,11 +1453,11 @@ static int CmdHFCipurseDefault(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0(NULL, "clear",   "resets to defaults"),
+        arg_lit0(NULL, "clear",   "Resets to defaults"),
         arg_int0("n",  NULL,      "<dec>", "Key ID"),
         arg_str0("k",  "key",     "<hex>", "Authentication key"),
-        arg_str0(NULL, "aid",     "<hex 1..16 bytes>", "application ID (AID)"),
-        arg_str0(NULL, "fid",     "<hex 2 bytes>", "File ID"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (AID) ( 1..16 bytes )"),
+        arg_str0(NULL, "fid",     "<hex>", "File ID ( 2 bytes )"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
diff --git a/client/src/cmdhffido.c b/client/src/cmdhffido.c
index 1698ac8a5..f518be172 100644
--- a/client/src/cmdhffido.c
+++ b/client/src/cmdhffido.c
@@ -137,22 +137,22 @@ static int CmdHFFidoRegister(const char *cmd) {
                   "challenge parameter (32b) and application parameter (32b).\n"
                   "The default config filename is  `fido2_defparams.json`\n"
                   "\n",
-                  "hf fido reg                 -> execute command with 2 parameters, filled 0x00\n"
-                  "hf fido reg --cp s0 --ap s1 -> execute command with plain parameters\n"
+                  "hf fido reg                   -> execute command with 2 parameters, filled 0x00\n"
+                  "hf fido reg --cp s0 --ap s1   -> execute command with plain parameters\n"
                   "hf fido reg --cpx 000102030405060708090a0b0c0d0e0f000102030405060708090a0b0c0d0e0f --apx 000102030405060708090a0b0c0d0e0f000102030405060708090a0b0c0d0e0f\n"
-                  "hf fido reg -f fido2-params -> execute command with custom config file\n"
+                  "hf fido reg -f fido2-params   -> execute command with custom config file\n"
                  );
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",  "show APDU requests and responses"),
-        arg_litn("v",  "verbose",  0, 2, "show technical data. vv - show full certificates data"),
-        arg_lit0("t",  "tlv",   "Show DER certificate contents in TLV representation"),
-        arg_str0("f",  "file",  "<fn>", "JSON input file name for parameters"),
-        arg_str0(NULL,  "cp",   "<ascii>", "challenge parameter (1..16 chars)"),
-        arg_str0(NULL,  "ap",   "<ascii>",  "application parameter (1..16 chars)"),
-        arg_str0(NULL,  "cpx",   "<hex>", "challenge parameter (32 bytes hex)"),
-        arg_str0(NULL,  "apx",   "<hex>",  "application parameter (32 bytes hex)"),
+        arg_lit0("a",  "apdu", "Show APDU requests and responses"),
+        arg_litn("v",  "verbose",  0, 2, "Verbose mode. vv - show full certificates data"),
+        arg_lit0("t",  "tlv",  "Show DER certificate contents in TLV representation"),
+        arg_str0("f",  "file", "<fn>",  "JSON input file name for parameters"),
+        arg_str0(NULL, "cp",   "<str>", "Challenge parameter (1..16 chars)"),
+        arg_str0(NULL, "ap",   "<str>", "Application parameter (1..16 chars)"),
+        arg_str0(NULL, "cpx",  "<hex>", "Challenge parameter (32 bytes hex)"),
+        arg_str0(NULL, "apx",  "<hex>", "Application parameter (32 bytes hex)"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, cmd, argtable, true);
@@ -409,18 +409,18 @@ static int CmdHFFidoAuthenticate(const char *cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",      "show APDU reqests and responses"),
-        arg_lit0("v",  "verbose",   "show technical data"),
+        arg_lit0("a",  "apdu",      "Show APDU reqests and responses"),
+        arg_lit0("v",  "verbose",   "Verbose mode"),
         arg_rem("default mode:",    "dont-enforce-user-presence-and-sign"),
         arg_lit0("u",  "user",      "mode: enforce-user-presence-and-sign"),
         arg_lit0("c",  "check",     "mode: check-only"),
-        arg_str0("f",  "file",    "<fn>", "JSON input file name for parameters"),
-        arg_str0("k",  "key",    "<hex>", "public key to verify signature"),
-        arg_str0(NULL, "kh",     "<hex>",  "key handle (var 0..255b)"),
-        arg_str0(NULL, "cp",   "<ascii>", "challenge parameter (1..16 chars)"),
-        arg_str0(NULL, "ap",   "<ascii>",  "application parameter (1..16 chars)"),
-        arg_str0(NULL, "cpx",    "<hex>", "challenge parameter (32 bytes hex)"),
-        arg_str0(NULL, "apx",    "<hex>",  "application parameter (32 bytes hex)"),
+        arg_str0("f",  "file",  "<fn>",  "JSON file name for parameters"),
+        arg_str0("k",  "key",   "<hex>", "Public key to verify signature"),
+        arg_str0(NULL, "kh",    "<hex>", "Key handle (var 0..255b)"),
+        arg_str0(NULL, "cp",    "<str>", "Challenge parameter (1..16 chars)"),
+        arg_str0(NULL, "ap",    "<str>", "Application parameter (1..16 chars)"),
+        arg_str0(NULL, "cpx",   "<hex>", "Challenge parameter (32 bytes hex)"),
+        arg_str0(NULL, "apx",   "<hex>", "Application parameter (32 bytes hex)"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, cmd, argtable, true);
@@ -671,11 +671,11 @@ static int CmdHFFido2MakeCredential(const char *cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a", "apdu", "show APDU reqests and responses"),
-        arg_litn("v", "verbose", 0, 2, "show technical data. vv - show full certificates data"),
-        arg_lit0("t", "tlv", "Show DER certificate contents in TLV representation"),
-        arg_lit0("c", "cbor", "show CBOR decoded data"),
-        arg_str0("f", "file", "<fn>", "parameter JSON file name"),
+        arg_lit0("a", "apdu", "Show APDU reqests and responses"),
+        arg_litn("v", "verbose", 0, 2, "Verbose mode. vv - show full certificates data"),
+        arg_lit0("t", "tlv",  "Show DER certificate contents in TLV representation"),
+        arg_lit0("c", "cbor", "Show CBOR decoded data"),
+        arg_str0("f", "file", "<fn>", "Parameter JSON file name"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, cmd, argtable, true);
@@ -790,11 +790,11 @@ static int CmdHFFido2GetAssertion(const char *cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a", "apdu", "show APDU reqests and responses"),
-        arg_litn("v", "verbose", 0, 2, "show technical data. vv - show full certificates data"),
-        arg_lit0("c", "cbor", "show CBOR decoded data"),
-        arg_lit0("l", "list", "add CredentialId from json to allowList"),
-        arg_str0("f", "file", "<fn>", "parameter JSON file name"),
+        arg_lit0("a", "apdu", "Show APDU reqests and responses"),
+        arg_litn("v", "verbose", 0, 2, "Verbose mode. vv - show full certificates data"),
+        arg_lit0("c", "cbor", "Show CBOR decoded data"),
+        arg_lit0("l", "list", "Add CredentialId from json to allowList"),
+        arg_str0("f", "file", "<fn>", "Parameter JSON file name"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, cmd, argtable, true);
