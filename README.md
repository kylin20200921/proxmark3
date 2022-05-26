commit d41c025f5cc7d539c4afc943784b8ec911d272d2
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Feb 6 23:13:20 2022 +0100

    helptext unify

diff --git a/client/src/cmdhfmfdes.c b/client/src/cmdhfmfdes.c
index b75b2161d..5b7438c8c 100644
--- a/client/src/cmdhfmfdes.c
+++ b/client/src/cmdhfmfdes.c
@@ -442,33 +442,41 @@ static int CmdDesGetSessionParameters(CLIParserContext *ctx, DesfireContext_t *d
     }
 
     if (algoid) {
-        if (CLIGetOptionList(arg_get_str(ctx, algoid), DesfireAlgoOpts, &algores))
+        if (CLIGetOptionList(arg_get_str(ctx, algoid), DesfireAlgoOpts, &algores)) {
             return PM3_ESOFT;
+        }
     }
 
     if (keyid) {
         int keylen = 0;
         uint8_t keydata[200] = {0};
-        if (CLIParamHexToBuf(arg_get_str(ctx, keyid), keydata, sizeof(keydata), &keylen))
+        if (CLIParamHexToBuf(arg_get_str(ctx, keyid), keydata, sizeof(keydata), &keylen)) {
             return PM3_ESOFT;
+        }
+
         if (keylen && keylen != desfire_get_key_length(algores)) {
             PrintAndLogEx(ERR, "%s key must have %d bytes length instead of %d.", CLIGetOptionListStr(DesfireAlgoOpts, algores), desfire_get_key_length(algores), keylen);
             return PM3_EINVARG;
         }
-        if (keylen)
+
+        if (keylen) {
             memcpy(key, keydata, keylen);
+        }
     }
 
     if (kdfid) {
-        if (CLIGetOptionList(arg_get_str(ctx, kdfid), DesfireKDFAlgoOpts, &kdfAlgo))
+        if (CLIGetOptionList(arg_get_str(ctx, kdfid), DesfireKDFAlgoOpts, &kdfAlgo)) {
             return PM3_ESOFT;
+        }
     }
 
     if (kdfiid) {
         int datalen = kdfInputLen;
         uint8_t data[200] = {0};
-        if (CLIParamHexToBuf(arg_get_str(ctx, kdfiid), data, sizeof(data), &datalen))
+        if (CLIParamHexToBuf(arg_get_str(ctx, kdfiid), data, sizeof(data), &datalen)) {
             return PM3_ESOFT;
+        }
+
         if (datalen) {
             kdfInputLen = datalen;
             memcpy(kdfInput, data, datalen);
@@ -525,19 +533,19 @@ static int CmdDesGetSessionParameters(CLIParserContext *ctx, DesfireContext_t *d
 static int CmdHF14ADesDefault(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfdes default",
-                  "Set default parameters for access to desfire card.",
-                  "hf mfdes default -n 0 -t des -k 0000000000000000 -f none -> save to the default parameters");
+                  "Set default parameters for access to MIFARE DESfire card.",
+                  "hf mfdes default -n 0 -t des -k 0000000000000000 --kdf none -> save to the default parameters");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
+        arg_int0("n",  "keyno", "<dec>", "Key number"),
+        arg_str0("t",  "algo", "<DES|2TDEA|3TDEA|AES>", "Crypt algo"),
+        arg_str0("k",  "key", "<hex>", "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf", "<none|AN10922|gallagher>", "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi", "<hex>", "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode", "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset", "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL,  "schann", "<d40|ev1|ev2|lrp>", "Secure channel"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -1059,25 +1067,25 @@ static int CmdHF14aDesChk(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfdes chk",
                   "Checks keys with MIFARE DESFire card.",
-                  "hf mfdes chk -a 123456 -k 000102030405060708090a0b0c0d0e0f -> check key on aid 0x123456\n"
-                  "hf mfdes chk -d mfdes_default_keys -> check keys from dictionary against all existing aid on card\n"
-                  "hf mfdes chk -d mfdes_default_keys -a 123456 -> check keys from dictionary against aid 0x123456\n"
-                  "hf mfdes chk -a 123456 --pattern1b -j keys -> check all 1-byte keys pattern on aid 0x123456 and save found keys to json\n"
-                  "hf mfdes chk -a 123456 --pattern2b --startp2b FA00 -> check all 2-byte keys pattern on aid 0x123456. Start from key FA00FA00...FA00");
+                  "hf mfdes chk -a 123456 -k 000102030405060708090a0b0c0d0e0f  -> check key on aid 0x123456\n"
+                  "hf mfdes chk -d mfdes_default_keys                          -> check keys from dictionary against all existing aid on card\n"
+                  "hf mfdes chk -d mfdes_default_keys -a 123456        -> check keys from dictionary against aid 0x123456\n"
+                  "hf mfdes chk -a 123456 --pattern1b -j keys          -> check all 1-byte keys pattern on aid 0x123456 and save found keys to json\n"
+                  "hf mfdes chk -a 123456 --pattern2b --startp2b FA00  -> check all 2-byte keys pattern on aid 0x123456. Start from key FA00FA00...FA00");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str0(NULL,  "aid",      "<aid>", "Use specific AID (3 hex bytes, big endian)"),
-        arg_str0("k",  "key",       "<Key>", "Key for checking (HEX 16 bytes)"),
-        arg_str0("d",  "dict",      "<file>", "File with keys dictionary"),
-        arg_lit0(NULL,  "pattern1b", "Check all 1-byte combinations of key (0000...0000, 0101...0101, 0202...0202, ...)"),
-        arg_lit0(NULL,  "pattern2b", "Check all 2-byte combinations of key (0000...0000, 0001...0001, 0002...0002, ...)"),
-        arg_str0(NULL,  "startp2b",  "<Pattern>", "Start key (2-byte HEX) for 2-byte search (use with `--pattern2b`)"),
-        arg_str0("j",  "json",      "<file>",  "Json file to save keys"),
-        arg_lit0("v",  "verbose",   "Verbose mode."),
-        arg_int0("f",  "kdf",     "<kdf>", "Key Derivation Function (KDF) (0=None, 1=AN10922, 2=Gallagher)"),
-        arg_str0("i",  "kdfi",    "<kdfi>", "KDF input (HEX 1-31 bytes)"),
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
+        arg_str0(NULL, "aid",        "<hex>", "Use specific AID (3 hex bytes, big endian)"),
+        arg_str0("k",  "key",        "<hex>", "Key for checking (HEX 16 bytes)"),
+        arg_str0("d",  "dict",       "<fn>", "Dictionary file with keys"),
+        arg_lit0(NULL, "pattern1b",  "Check all 1-byte combinations of key (0000...0000, 0101...0101, 0202...0202, ...)"),
+        arg_lit0(NULL, "pattern2b",  "Check all 2-byte combinations of key (0000...0000, 0001...0001, 0002...0002, ...)"),
+        arg_str0(NULL, "startp2b",   "<pattern>", "Start key (2-byte HEX) for 2-byte search (use with `--pattern2b`)"),
+        arg_str0("j",  "json",       "<fn>",  "Json file name to save keys"),
+        arg_lit0("v",  "verbose",    "Verbose mode"),
+        arg_int0(NULL, "kdf",        "<0|1|2>", "Key Derivation Function (KDF) (0=None, 1=AN10922, 2=Gallagher)"),
+        arg_str0("i",  "kdfi",       "<hex>", "KDF input (1-31 hex bytes)"),
+        arg_lit0("a",  "apdu",       "Show APDU requests and responses"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -1160,9 +1168,10 @@ static int CmdHF14aDesChk(const char *Cmd) {
     bool verbose = arg_get_lit(ctx, 8);
 
     // Get KDF input
+    uint8_t cmdKDFAlgo  = arg_get_int_def(ctx, 9, 0);
+
     uint8_t kdfInput[31] = {0};
     int kdfInputLen = 0;
-    uint8_t cmdKDFAlgo  = arg_get_int_def(ctx, 9, 0);
     CLIGetHexWithReturn(ctx, 10, kdfInput, &kdfInputLen);
 
     CLIParserFree(ctx);
@@ -1370,28 +1379,28 @@ static int CmdHF14aDesDetect(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfdes detect",
                   "Detect key type and tries to find one from the list.",
-                  "hf mfdes detect -> detect key 0 from PICC level\n"
-                  "hf mfdes detect -s d40 -> detect key 0 from PICC level via secure channel D40\n"
-                  "hf mfdes detect --dict mfdes_default_keys -> detect key 0 from PICC level with help of the standard dictionary\n"
-                  "hf mfdes detect --aid 123456 -n 2 --save -> detect key 2 from app 123456 and if succeed - save params to defaults (`default` command)\n"
-                  "hf mfdes detect --appisoid df01 --save -> detect key 0 and save to defaults with card in the LRP mode");
+                  "hf mfdes detect                            -> detect key 0 from PICC level\n"
+                  "hf mfdes detect --schann d40               -> detect key 0 from PICC level via secure channel D40\n"
+                  "hf mfdes detect --dict mfdes_default_keys  -> detect key 0 from PICC level with help of the standard dictionary\n"
+                  "hf mfdes detect --aid 123456 -n 2 --save   -> detect key 2 from app 123456 and if succeed - save params to defaults (`default` command)\n"
+                  "hf mfdes detect --isoid df01 --save        -> detect key 0 and save to defaults with card in the LRP mode");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID (3 hex bytes, big endian)"),
-        arg_str0(NULL, "appisoid", "<isoid hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
-        arg_str0(NULL, "dict",    "<file>", "File with keys dictionary"),
-        arg_lit0(NULL, "save",    "save found key and parameters to defaults"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>", "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>", "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>", "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>", "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (3 hex bytes, big endian)"),
+        arg_str0(NULL, "isoid",   "<hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
+        arg_str0(NULL, "dict",    "<fn>", "Dictionary file name with keys"),
+        arg_lit0(NULL, "save",    "Save found key and parameters to defaults"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -1645,17 +1654,17 @@ static int CmdHF14aDesMAD(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID of issuer info file, (non-standard feature!) (3 hex bytes, big endian)"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>", "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID of issuer info file, (3 hex bytes, big endian),  (non-standard feature!)"),
         arg_lit0(NULL, "auth",    "Authenticate to get info from GetApplicationIDs command (non-standard feature!)"),
         arg_param_end
     };
@@ -1779,30 +1788,30 @@ static int CmdHF14ADesSelectApp(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfdes selectapp",
                   "Select application on the card. It selects app if it is a valid one or returns an error.",
-                  "hf mfdes selectapp --aid 123456 -> select application 123456\n"
-                  "hf mfdes selectapp --mf -> select master file (PICC level)\n"
+                  "hf mfdes selectapp --aid 123456       -> select application 123456\n"
+                  "hf mfdes selectapp --mf               -> select master file (PICC level)\n"
                   "hf mfdes selectapp --dfname aid123456 -> select application aid123456 by DF name\n"
-                  "hf mfdes selectapp --isoid 1111 -> select application 1111 by ISO ID\n"
-                  "hf mfdes selectapp --isoid 1111 --fileisoid 2222 -> select application 1111 file 2222 by ISO ID\n"
-                  "hf mfdes selectapp --isoid 01df --fileisoid 00ef -> select file 00 on the Desfire Light");
+                  "hf mfdes selectapp --isoid 1111       -> select application 1111 by ISO ID\n"
+                  "hf mfdes selectapp --isoid 1111 --fileisoid 2222   -> select application 1111 file 2222 by ISO ID\n"
+                  "hf mfdes selectapp --isoid 01df --fileisoid 00ef   -> select file 00 on the Desfire Light");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID of application for some parameters (3 hex bytes, big endian)"),
-        arg_str0(NULL, "dfname",  "<df name str>", "Application DF Name (string, max 16 chars). Selects application via ISO SELECT command"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID of application for some parameters (3 hex bytes, big endian)"),
+        arg_str0(NULL, "dfname",  "<str>", "Application DF Name (string, max 16 chars). Selects application via ISO SELECT command"),
         arg_lit0(NULL, "mf",      "Select MF (master file) via ISO channel"),
-        arg_str0(NULL, "isoid",   "<isoid hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)"),
-        arg_str0(NULL, "fileisoid", "<isoid hex>", "Select file inside application by ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
+        arg_str0(NULL, "isoid",   "<hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)"),
+        arg_str0(NULL, "fileisoid", "<hex>", "Select file inside application by ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -1921,14 +1930,14 @@ static int CmdHF14ADesBruteApps(const char *Cmd) {
                   "Recover AIDs by bruteforce.\n"
                   "WARNING: This command takes a loooong time",
                   "hf mfdes bruteaid                    -> Search all apps\n"
-                  "hf mfdes bruteaid -s F0000F -i 16    -> Search MAD range manually");
+                  "hf mfdes bruteaid --start F0000F -i 16    -> Search MAD range manually");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str0("s",  "start", "<hex>", "Starting App ID as hex bytes (3 bytes, big endian)"),
-        arg_str0("e",  "end",   "<hex>", "Last App ID as hex bytes (3 bytes, big endian)"),
-        arg_int0("i",   "step",  "<dec>", "Increment step when bruteforcing"),
-        arg_lit0("m",   "mad",   "Only bruteforce the MAD range"),
+        arg_str0(NULL, "start", "<hex>", "Starting App ID as hex bytes (3 bytes, big endian)"),
+        arg_str0(NULL, "end",   "<hex>", "Last App ID as hex bytes (3 bytes, big endian)"),
+        arg_int0("i",  "step",  "<dec>", "Increment step when bruteforcing"),
+        arg_lit0("m",  "mad",   "Only bruteforce the MAD range"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -2007,25 +2016,25 @@ static int CmdHF14ADesAuth(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfdes auth",
                   "Select application on the card. It selects app if it is a valid one or returns an error.",
-                  "hf mfdes auth  -n 0 -t des -k 0000000000000000 -f none -> select PICC level and authenticate with key num=0, key type=des, key=00..00 and key derivation = none\n"
-                  "hf mfdes auth  -n 0 -t aes -k 00000000000000000000000000000000 -> select PICC level and authenticate with key num=0, key type=aes, key=00..00 and key derivation = none\n"
-                  "hf mfdes auth  -n 0 -t des -k 0000000000000000 --save -> select PICC level and authenticate and in case of successful authentication - save channel parameters to defaults\n"
-                  "hf mfdes auth --aid 123456 -> select application 123456 and authenticate via parameters from `default` command");
+                  "hf mfdes auth  -n 0 -t des -k 0000000000000000 --kdf none       -> select PICC level and authenticate with key num=0, key type=des, key=00..00 and key derivation = none\n"
+                  "hf mfdes auth  -n 0 -t aes -k 00000000000000000000000000000000  -> select PICC level and authenticate with key num=0, key type=aes, key=00..00 and key derivation = none\n"
+                  "hf mfdes auth  -n 0 -t des -k 0000000000000000 --save           -> select PICC level and authenticate and in case of successful authentication - save channel parameters to defaults\n"
+                  "hf mfdes auth --aid 123456    -> select application 123456 and authenticate via parameters from `default` command");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
         arg_int0("n",  "keyno",   "<dec>", "Key number"),
-        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40|ev1|ev2|lrp>", "Secure channel: d40/ev1/ev2/lrp"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>", "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>", "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>", "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
         arg_str0(NULL, "aid",     "<hex>", "Application ID of application for some parameters (3 hex bytes, big endian)"),
-        arg_str0(NULL, "appisoid", "<hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
+        arg_str0(NULL, "isoid",   "<hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)"),
         arg_lit0(NULL, "save",    "saves channels parameters to defaults if authentication succeeds"),
         arg_param_end
     };
@@ -2085,41 +2094,44 @@ static int CmdHF14ADesAuth(const char *Cmd) {
 static int CmdHF14ADesSetConfiguration(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfdes setconfig",
-                  "Set card configuration. WARNING! Danger zone! Needs to provide card's master key and works if not blocked by config.",
-                  "More about options MF2DLHX0.pdf. Options list:\n"
-                  "00h PICC configuration.\n"
-                  "02h ATS update.\n"
-                  "03h SAK update\n"
-                  "04h Secure Messaging Configuration.\n"
-                  "05h Capability data. (here change for LRP in the Desfire Light [enable 00000000010000000000])\n"
-                  "06h DF Name renaming (one-time)\n"
-                  "08h File renaming (one-time)\n"
-                  "09h Value file configuration (one-time)\n"
-                  "0Ah Failed authentication counter setting [disable 00ffffffff]\n"
-                  "0Bh HW configuration\n"
+                  "Set card configuration. \n"
+                  "WARNING! Danger zone!\n"
+                  "Needs to provide card's master key and works if not blocked by config.",
+                  "More about options MF2DLHX0.pdf.\n"
+                  "Options list:\n"
+                  "  00h PICC configuration.\n"
+                  "  02h ATS update.\n"
+                  "  03h SAK update\n"
+                  "  04h Secure Messaging Configuration.\n"
+                  "  05h Capability data. (here change for LRP in the Desfire Light [enable 00000000010000000000])\n"
+                  "  06h DF Name renaming (one-time)\n"
+                  "  08h File renaming (one-time)\n"
+                  "  09h Value file configuration (one-time)\n"
+                  "  0Ah Failed authentication counter setting [disable 00ffffffff]\n"
+                  "  0Bh HW configuration\n"
                   "\n"
-                  "hf mfdes setconfig --param 03 --data 0428 -> set SAK\n"
-                  "hf mfdes setconfig --param 02 --data 0875778102637264 -> set ATS (first byte - length)\n"
-                  "hf mfdes setconfig --appisoid df01 -t aes -s ev2 --param 05 --data 00000000020000000000 -> set LRP mode enable for Desfire Light\n"
-                  "hf mfdes setconfig --appisoid df01 -t aes -s ev2 --param 0a --data 00ffffffff -> Disable failed auth counters for Desfire Light\n"
-                  "hf mfdes setconfig --appisoid df01 -t aes -s lrp --param 0a --data 00ffffffff -> Disable failed auth counters for Desfire Light via lrp channel");
+                  "hf mfdes setconfig --param 03 --data 0428               -> set SAK\n"
+                  "hf mfdes setconfig --param 02 --data 0875778102637264   -> set ATS (first byte - length)\n"
+                  "hf mfdes setconfig --isoid df01 -t aes -s ev2 --param 05 --data 00000000020000000000 -> set LRP mode enable for Desfire Light\n"
+                  "hf mfdes setconfig --isoid df01 -t aes -s ev2 --param 0a --data 00ffffffff           -> Disable failed auth counters for Desfire Light\n"
+                  "hf mfdes setconfig --isoid df01 -t aes -s lrp --param 0a --data 00ffffffff           -> Disable failed auth counters for Desfire Light via lrp");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID of application for some parameters (3 hex bytes, big endian)"),
-        arg_str0(NULL, "appisoid", "<isoid hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
-        arg_str0("p",  "param",   "<HEX 1 byte>", "Parameter id (HEX 1 byte)"),
-        arg_str0("d",  "data",    "<data HEX>", "Data for parameter (HEX 1..30 bytes)"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>", "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>", "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID of application for some parameters (3 hex bytes, big endian)"),
+        arg_str0(NULL, "isoid",   "<hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
+        arg_str0("p",  "param",   "<hex>", "Parameter id (1 hex byte)"),
+        arg_str0("d",  "data",    "<hex>", "Data for parameter (1..30 hex bytes)"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -2189,35 +2201,36 @@ static int CmdHF14ADesChangeKey(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfdes changekey",
                   "Change PICC/Application key. Needs to provide keynum/key for a valid authentication (may get from default parameters).",
-                  "Change crypto algorithm for PICC key is possible, but for APP keys crypto algorithm is set by createapp command and can't be changed wo application delete\n"
+                  "Change crypto algorithm for PICC key is possible, \n"
+                  "but for APP keys crypto algorithm is set by createapp command and can't be changed wo application delete\n"
                   "\n"
-                  "hf mfdes changekey --aid 123456 -> execute with default factory setup. change des key 0 in the app 123456 from 00..00 to 00..00\n"
-                  "hf mfdes changekey --appisoid df01 -t aes -s lrp --newkeyno 01 -> change key 01 via lrp channel"
-                  "hf mfdes changekey -t des --newalgo aes --newkey 11223344556677889900112233445566 --newver a5 -> change card master key to AES one\n"
+                  "hf mfdes changekey --aid 123456    -> execute with default factory setup. change des key 0 in the app 123456 from 00..00 to 00..00\n"
+                  "hf mfdes changekey --isoid df01 -t aes -s lrp --newkeyno 01    -> change key 01 via lrp channel"
+                  "hf mfdes changekey -t des --newalgo aes --newkey 11223344556677889900112233445566 --newver a5      -> change card master key to AES one\n"
                   "hf mfdes changekey --aid 123456 -t aes --key 00000000000000000000000000000000 --newkey 11223344556677889900112233445566 -> change app master key\n"
-                  "hf mfdes changekey --aid 123456 -t des -n 0 --newkeyno 1 --oldkey 5555555555555555 --newkey 1122334455667788 -> change key 1 with auth from key 0\n"
-                  "hf mfdes changekey --aid 123456 -t 3tdea --newkey 112233445566778899001122334455667788990011223344-> change 3tdea key 0 from default 00..00 to provided");
+                  "hf mfdes changekey --aid 123456 -t des -n 0 --newkeyno 1 --oldkey 5555555555555555 --newkey 1122334455667788  -> change key 1 with auth from key 0\n"
+                  "hf mfdes changekey --aid 123456 -t 3tdea --newkey 112233445566778899001122334455667788990011223344            -> change 3tdea key 0 from default 00..00 to provided");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID of application (3 hex bytes, big endian)"),
-        arg_str0(NULL, "appisoid", "<isoid hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
-        arg_str0(NULL, "oldalgo", "<DES/2TDEA/3TDEA/AES>", "Old key crypto algorithm: DES, 2TDEA, 3TDEA, AES"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>", "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>", "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID of application (3 hex bytes, big endian)"),
+        arg_str0(NULL, "isoid",   "<hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
+        arg_str0(NULL, "oldalgo", "<DES|2TDEA|3TDEA|AES>", "Old key crypto algorithm"),
         arg_str0(NULL, "oldkey",  "<old key>", "Old key (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_int0(NULL, "newkeyno", "<keyno>", "Key number for change"),
-        arg_str0(NULL, "newalgo", "<DES/2TDEA/3TDEA/AES>", "New key crypto algorithm: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0(NULL, "newkey",  "<new key>", "New key (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0(NULL, "newver",  "<version hex>", "New key's version (1 hex byte)"),
+        arg_int0(NULL, "newkeyno", "<dec>", "Key number for change"),
+        arg_str0(NULL, "newalgo", "<DES|2TDEA|3TDEA|AES>", "New key crypto algorithm"),
+        arg_str0(NULL, "newkey",  "<hex>", "New key (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "newver",  "<hex>", "Version of new key (1 hex byte)"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -2362,24 +2375,24 @@ static int CmdHF14ADesCreateApp(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
         arg_int0("n",  "keyno",   "<dec>", "Key number"),
-        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
         arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40|ev1|ev2|lrp>", "Secure channel: d40/ev1/ev2/lrp"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
         arg_str0(NULL, "rawdata", "<hex>", "Raw data that sends to command"),
         arg_str0(NULL, "aid",     "<hex>", "Application ID for create. Mandatory. (3 hex bytes, big endian)"),
-        arg_str0(NULL, "fid",     "<hex>", "ISO file ID. Forbidden values: 0000 3F00, 3FFF, FFFF. (2 hex bytes, big endian). If specified - enable iso file id over all the files in the app."),
-        arg_str0(NULL, "dfname",  "<string>", "ISO DF Name 1..16 chars string"),
-        arg_str0(NULL, "ks1",     "<hex>", "Key settings 1 (HEX 1 byte). Application Master Key Settings. default 0x0f"),
-        arg_str0(NULL, "ks2",     "<hex>", "Key settings 2 (HEX 1 byte). default 0x0e"),
-        arg_str0(NULL, "dstalgo", "<DES|2TDEA|3TDEA|AES>",  "Application key crypt algo: DES, 2TDEA, 3TDEA, AES. default DES"),
-        arg_int0(NULL, "numkeys", "<dec>",  "Number of keys 0x00..0x0e. default 0x0e"),
+        arg_str0(NULL, "fid",     "<hex>", "ISO file ID. Forbidden values: 0000 3F00, 3FFF, FFFF. (2 hex bytes, big endian)"),
+        arg_str0(NULL, "dfname",  "<string>", "ISO DF Name (1..16 chars)"),
+        arg_str0(NULL, "ks1",     "<hex>", "Key settings 1 (1 hex byte). Application Master Key Settings (def: 0x0F)"),
+        arg_str0(NULL, "ks2",     "<hex>", "Key settings 2 (1 hex byte). (def: 0x0E)"),
+        arg_str0(NULL, "dstalgo", "<DES|2TDEA|3TDEA|AES>",  "Application key crypt algo (def: DES)"),
+        arg_int0(NULL, "numkeys", "<dec>",  "Number of keys 0x00..0x0e (def: 0x0E)"),
         arg_lit0(NULL, "no-auth", "Execute without authentication"),
         arg_param_end
     };
@@ -2524,21 +2537,21 @@ static int CmdHF14ADesDeleteApp(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfdes deleteapp",
                   "Delete application by its 3-byte AID. Master key needs to be provided. ",
-                  "hf mfdes deleteapp --aid 123456 -> execute with default factory setup");
+                  "hf mfdes deleteapp --aid 123456    -> execute with default factory setup");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID of delegated application (3 hex bytes, big endian)"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID of delegated application (3 hex bytes, big endian)"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -2586,23 +2599,23 @@ static int CmdHF14ADesGetUID(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfdes getuid",
                   "Get UID from card. Get the real UID if the random UID bit is on and get the same UID as in anticollision if not. Any card's key needs to be provided. ",
-                  "hf mfdes getuid -> execute with default factory setup\n"
-                  "hf mfdes getuid --appisoid df01 -t aes -s lrp -> for desfire lights default settings");
+                  "hf mfdes getuid    -> execute with default factory setup\n"
+                  "hf mfdes getuid --isoid df01 -t aes -s lrp   -> for desfire lights default settings");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID (3 hex bytes, big endian)"),
-        arg_str0(NULL, "appisoid", "<isoid hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (3 hex bytes, big endian)"),
+        arg_str0(NULL, "isoid",   "<hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -2667,21 +2680,21 @@ static int CmdHF14ADesFormatPICC(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfdes formatpicc",
                   "Format card. Can be done only if enabled in the configuration. Master key needs to be provided. ",
-                  "hf mfdes formatpicc -> execute with default factory setup");
+                  "hf mfdes formatpicc    -> execute with default factory setup");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID of delegated application (3 hex bytes, big endian)"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID of delegated application (3 hex bytes, big endian)"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -2724,21 +2737,21 @@ static int CmdHF14ADesGetFreeMem(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfdes getfreemem",
                   "Get card's free memory. Can be done with or without authentication. Master key may be provided.",
-                  "hf mfdes getfreemem -> execute with default factory setup");
+                  "hf mfdes getfreemem    -> execute with default factory setup");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -2783,24 +2796,25 @@ static int CmdHF14ADesGetFreeMem(const char *Cmd) {
 static int CmdHF14ADesChKeySettings(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfdes chkeysettings",
-                  "Change key settings for card level or application level. WARNING: card level changes may block the card!",
-                  "hf mfdes chkeysettings -d 0f -> set picc key settings with default key/channel setup\n"\
-                  "hf mfdes chkeysettings --aid 123456 -d 0f -> set app 123456 key settings with default key/channel setup");
+                  "Change key settings for card level or application level.\n"
+                  "WARNING: card level changes may block the card!",
+                  "hf mfdes chkeysettings -d 0f         -> set picc key settings with default key/channel setup\n"\
+                  "hf mfdes chkeysettings --aid 123456 -d 0f     -> set app 123456 key settings with default key/channel setup");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID (3 hex bytes, big endian)"),
-        arg_str0("d",  "data",    "<key settings HEX>", "Key settings (HEX 1 byte)"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (3 hex bytes, big endian)"),
+        arg_str0("d",  "data",    "<HEX>", "Key settings (1 hex byte)"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -2856,27 +2870,27 @@ static int CmdHF14ADesGetKeyVersions(const char *Cmd) {
     CLIParserInit(&ctx, "hf mfdes getkeyversions",
                   "Get key versions for card level or application level.",
                   "--keynum parameter: App level: key number. PICC level: 00..0d - keys count, 21..23 vc keys, default 0x00.\n"\
-                  "hf mfdes getkeyversions --keynum 00 -> get picc master key version with default key/channel setup\n"\
-                  "hf mfdes getkeyversions --aid 123456 --keynum 0d -> get app 123456 all key versions with default key/channel setup\n"
-                  "hf mfdes getkeyversions --aid 123456 --keynum 0d --no-auth -> get key version without authentication");
+                  "hf mfdes getkeyversions --keynum 00    -> get picc master key version with default key/channel setup\n"\
+                  "hf mfdes getkeyversions --aid 123456 --keynum 0d    -> get app 123456 all key versions with default key/channel setup\n"
+                  "hf mfdes getkeyversions --aid 123456 --keynum 0d --no-auth    -> get key version without authentication");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number for authentication"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID (3 hex bytes, big endian)"),
-        arg_str0(NULL, "appisoid", "<isoid hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
-        arg_str0(NULL, "keynum",  "<key number HEX>", "Key number/count (HEX 1 byte). Default 0x00."),
-        arg_str0(NULL, "keyset",  "<keyset num HEX>", "Keyset number (HEX 1 byte)"),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number for authentication"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (3 hex bytes, big endian)"),
+        arg_str0(NULL, "isoid",   "<hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
+        arg_str0(NULL, "keynum",  "<hex>", "Key number/count (1 hex byte). (def: 0x00)"),
+        arg_str0(NULL, "keyset",  "<hex>", "Keyset number (1 hex byte)"),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -2964,17 +2978,17 @@ static int CmdHF14ADesGetKeySettings(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID (3 hex bytes, big endian)"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (3 hex bytes, big endian)"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -3038,21 +3052,21 @@ static int CmdHF14ADesGetAIDs(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfdes getaids",
                   "Get Application IDs list from card. Master key needs to be provided or flag --no-auth set.",
-                  "hf mfdes getaids -n 0 -t des -k 0000000000000000 -f none -> execute with default factory setup");
+                  "hf mfdes getaids -n 0 -t des -k 0000000000000000 --kdf none -> execute with default factory setup");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -3109,21 +3123,21 @@ static int CmdHF14ADesGetAppNames(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfdes getappnames",
                   "Get Application IDs, ISO IDs and DF names from card. Master key needs to be provided or flag --no-auth set.",
-                  "hf mfdes getappnames -n 0 -t des -k 0000000000000000 -f none -> execute with default factory setup");
+                  "hf mfdes getappnames -n 0 -t des -k 0000000000000000 --kdf none   -> execute with default factory setup");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -3181,23 +3195,23 @@ static int CmdHF14ADesGetFileIDs(const char *Cmd) {
     CLIParserInit(&ctx, "hf mfdes getfileids",
                   "Get File IDs list from card. Master key needs to be provided or flag --no-auth set.",
                   "hf mfdes getfileids --aid 123456 -> execute with defaults from `default` command\n"
-                  "hf mfdes getfileids -n 0 -t des -k 0000000000000000 -f none --aid 123456 -> execute with default factory setup");
+                  "hf mfdes getfileids -n 0 -t des -k 0000000000000000 --kdf none --aid 123456   -> execute with default factory setup");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID (3 hex bytes, big endian)"),
-        arg_str0(NULL, "appisoid", "<isoid hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (3 hex bytes, big endian)"),
+        arg_str0(NULL, "isoid",   "<hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -3252,26 +3266,26 @@ static int CmdHF14ADesGetFileISOIDs(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfdes getfileisoids",
                   "Get File IDs list from card. Master key needs to be provided or flag --no-auth set.",
-                  "hf mfdes getfileisoids --aid 123456 -> execute with defaults from `default` command\n"
-                  "hf mfdes getfileisoids -n 0 -t des -k 0000000000000000 -f none --aid 123456 -> execute with default factory setup\n"
-                  "hf mfdes getfileisoids --appisoid df01 -> get iso file ids from Desfire Light with factory card settings\n"
-                  "hf mfdes getfileisoids --appisoid df01 -s lrp -t aes -> get iso file ids from Desfire Light via lrp channel with default key authentication");
+                  "hf mfdes getfileisoids --aid 123456    -> execute with defaults from `default` command\n"
+                  "hf mfdes getfileisoids -n 0 -t des -k 0000000000000000 --kdf none --aid 123456    -> execute with default factory setup\n"
+                  "hf mfdes getfileisoids --isoid df01     -> get iso file ids from Desfire Light with factory card settings\n"
+                  "hf mfdes getfileisoids --isoid df01 -s lrp -t aes     -> get iso file ids from Desfire Light via lrp channel with default key authentication");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID (3 hex bytes, big endian)"),
-        arg_str0(NULL, "appisoid", "<isoid hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (3 hex bytes, big endian)"),
+        arg_str0(NULL, "isoid",   "<hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -3326,26 +3340,26 @@ static int CmdHF14ADesGetFileSettings(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfdes getfilesettings",
                   "Get File Settings from file from application. Master key needs to be provided or flag --no-auth set (depend on cards settings).",
-                  "hf mfdes getfilesettings --aid 123456 --fid 01 -> execute with defaults from `default` command\n"
-                  "hf mfdes getfilesettings --appisoid df01 --fid 00 --no-auth -> get file settings with select by iso id\n"
-                  "hf mfdes getfilesettings -n 0 -t des -k 0000000000000000 -f none --aid 123456 --fid 01 -> execute with default factory setup");
+                  "hf mfdes getfilesettings --aid 123456 --fid 01     -> execute with defaults from `default` command\n"
+                  "hf mfdes getfilesettings --isoid df01 --fid 00 --no-auth     -> get file settings with select by iso id\n"
+                  "hf mfdes getfilesettings -n 0 -t des -k 0000000000000000 --kdf none --aid 123456 --fid 01    -> execute with default factory setup");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID (3 hex bytes, big endian)"),
-        arg_str0(NULL, "appisoid", "<isoid hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
-        arg_str0(NULL, "fid",     "<file id hex>", "File ID (1 hex byte). default: 1"),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (3 hex bytes, big endian)"),
+        arg_str0(NULL, "isoid",   "<hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)"),
+        arg_str0(NULL, "fid",     "<hex>", "File ID (1 hex byte). (def: 1)"),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -3495,33 +3509,33 @@ static int CmdHF14ADesChFileSettings(const char *Cmd) {
     CLIParserInit(&ctx, "hf mfdes chfilesettings",
                   "Get File Settings from file from application. Master key needs to be provided or flag --no-auth set (depend on cards settings).",
                   "hf mfdes chfilesettings --aid 123456 --fid 01 --amode plain --rrights free --wrights free --rwrights free --chrights key0 -> change file settings app=123456, file=01 with defaults from `default` command\n"
-                  "hf mfdes chfilesettings -n 0 -t des -k 0000000000000000 -f none --aid 123456 --fid 01 --rawdata 00EEEE -> execute with default factory setup\n"
+                  "hf mfdes chfilesettings -n 0 -t des -k 0000000000000000 --kdf none --aid 123456 --fid 01 --rawdata 00EEEE -> execute with default factory setup\n"
                   "hf mfdes chfilesettings --aid 123456 --fid 01 --rawdata 810000021f112f22 -> change file settings with additional rights for keys 1 and 2\n"
-                  "hf mfdes chfilesettings --appisoid df01 --fid 00 --amode plain --rawrights eee0 -s lrp -t aes -> change file settings via lrp channel");
+                  "hf mfdes chfilesettings --isoid df01 --fid 00 --amode plain --rawrights eee0 -s lrp -t aes -> change file settings via lrp channel");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID (3 hex bytes, big endian)"),
-        arg_str0(NULL, "appisoid", "<isoid hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
-        arg_str0(NULL, "fid",     "<file id hex>", "File ID (1 hex byte)"),
-        arg_str0(NULL, "rawdata", "<file settings HEX>", "File settings (HEX > 5 bytes). Have priority over the other settings."),
-        arg_str0(NULL, "amode",   "<plain/mac/encrypt>", "File access mode: plain/mac/encrypt"),
-        arg_str0(NULL, "rawrights", "<access rights HEX>", "Access rights for file (HEX 2 byte) R/W/RW/Chg, 0x0 - 0xD Key, 0xE Free, 0xF Denied"),
-        arg_str0(NULL, "rrights", "<key0/../key13/free/deny>", "Read file access mode: the specified key, free, deny"),
-        arg_str0(NULL, "wrights", "<key0/../key13/free/deny>", "Write file access mode: the specified key, free, deny"),
-        arg_str0(NULL, "rwrights", "<key0/../key13/free/deny>", "Read/Write file access mode: the specified key, free, deny"),
-        arg_str0(NULL, "chrights", "<key0/../key13/free/deny>", "Change file settings access mode: the specified key, free, deny"),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (3 hex bytes, big endian)"),
+        arg_str0(NULL, "isoid",   "<hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)"),
+        arg_str0(NULL, "fid",     "<hex>", "File ID (1 hex byte)"),
+        arg_str0(NULL, "rawdata", "<hex>", "File settings (HEX > 5 bytes). Have priority over the other settings"),
+        arg_str0(NULL, "amode",   "<plain|mac|encrypt>", "File access mode"),
+        arg_str0(NULL, "rawrights", "<hex>", "Access rights for file (2 hex bytes) R/W/RW/Chg, 0x0 - 0xD Key, 0xE Free, 0xF Denied"),
+        arg_str0(NULL, "rrights",  "<key0..13|free|deny>", "Read file access mode: the specified key, free, deny"),
+        arg_str0(NULL, "wrights",  "<key0..13|free|deny>", "Write file access mode: the specified key, free, deny"),
+        arg_str0(NULL, "rwrights", "<key0..13|free|deny>", "Read/Write file access mode: the specified key, free, deny"),
+        arg_str0(NULL, "chrights", "<key0..13|free|deny>", "Change file settings access mode: the specified key, free, deny"),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -3640,32 +3654,32 @@ static int CmdHF14ADesCreateFile(const char *Cmd) {
                   "hf mfdes createfile --aid 123456 --fid 01 --isofid 0001 --size 000010 -> create file with iso id. Authentication with defaults from `default` command\n"
                   "hf mfdes createfile --aid 123456 --fid 01 --rawtype 01 --rawdata 000100EEEE000100 -> create file via sending rawdata to the card. Can be used to create any type of file. Authentication with defaults from `default` command\n"
                   "hf mfdes createfile --aid 123456 --fid 01 --amode plain --rrights free --wrights free --rwrights free --chrights key0 -> create file app=123456, file=01 and mentioned rights with defaults from `default` command\n"
-                  "hf mfdes createfile -n 0 -t des -k 0000000000000000 -f none --aid 123456 --fid 01 --rawtype 00 --rawdata 00EEEE000100 -> execute with default factory setup");
+                  "hf mfdes createfile -n 0 -t des -k 0000000000000000 --kdf none --aid 123456 --fid 01 --rawtype 00 --rawdata 00EEEE000100 -> execute with default factory setup");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID (3 hex bytes, big endian)"),
-        arg_str0(NULL, "fid",     "<file id hex>", "File ID (1 hex byte)"),
-        arg_str0(NULL, "isofid",  "<iso file id hex>", "ISO File ID (2 hex bytes)"),
-        arg_str0(NULL, "rawtype", "<file type HEX 1b>", "Raw file type (HEX 1 byte)"),
-        arg_str0(NULL, "rawdata", "<file settings HEX>", "Raw file settings (HEX > 5 bytes)"),
-        arg_str0(NULL, "amode",   "<plain/mac/encrypt>", "File access mode: plain/mac/encrypt"),
-        arg_str0(NULL, "rawrights", "<access rights HEX>", "Access rights for file (HEX 2 byte) R/W/RW/Chg, 0x0 - 0xD Key, 0xE Free, 0xF Denied"),
-        arg_str0(NULL, "rrights", "<key0/../key13/free/deny>", "Read file access mode: the specified key, free, deny"),
-        arg_str0(NULL, "wrights", "<key0/../key13/free/deny>", "Write file access mode: the specified key, free, deny"),
-        arg_str0(NULL, "rwrights", "<key0/../key13/free/deny>", "Read/Write file access mode: the specified key, free, deny"),
-        arg_str0(NULL, "chrights", "<key0/../key13/free/deny>", "Change file settings access mode: the specified key, free, deny"),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (3 hex bytes, big endian)"),
+        arg_str0(NULL, "fid",     "<hex>", "File ID (1 hex byte)"),
+        arg_str0(NULL, "isofid",  "<hex>", "ISO File ID (2 hex bytes)"),
+        arg_str0(NULL, "rawtype", "<hex>", "Raw file type (1 hex byte)"),
+        arg_str0(NULL, "rawdata", "<hex>", "Raw file settings (hex > 5 bytes)"),
+        arg_str0(NULL, "amode",   "<plain|mac|encrypt>", "File access mode"),
+        arg_str0(NULL, "rawrights", "<hex>", "Access rights for file (2 hex bytes) R/W/RW/Chg, 0x0 - 0xD Key, 0xE Free, 0xF Denied"),
+        arg_str0(NULL, "rrights",  "<key0..key13|free|deny>", "Read file access mode: the specified key, free, deny"),
+        arg_str0(NULL, "wrights",  "<key0..key13|free|deny>", "Write file access mode: the specified key, free, deny"),
+        arg_str0(NULL, "rwrights", "<key0..key13|free|deny>", "Read/Write file access mode: the specified key, free, deny"),
+        arg_str0(NULL, "chrights", "<key0..key13|free|deny>", "Change file settings access mode: the specified key, free, deny"),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
         arg_str0(NULL, "size", "<hex>", "File size (3 hex bytes, big endian)"),
         arg_lit0(NULL, "backup", "Create backupfile instead of standard file"),
         arg_param_end
@@ -3778,29 +3792,29 @@ static int CmdHF14ADesCreateValueFile(const char *Cmd) {
                   "Key/mode/etc of the authentication depends on application settings\n"
                   "hf mfdes createvaluefile --aid 123456 --fid 01 --lower 00000010 --upper 00010000 --value 00000100 -> create file with parameters. Rights from default. Authentication with defaults from `default` command\n"
                   "hf mfdes createvaluefile --aid 123456 --fid 01 --amode plain --rrights free --wrights free --rwrights free --chrights key0 -> create file app=123456, file=01 and mentioned rights with defaults from `default` command\n"
-                  "hf mfdes createvaluefile -n 0 -t des -k 0000000000000000 -f none --aid 123456 --fid 01 -> execute with default factory setup");
+                  "hf mfdes createvaluefile -n 0 -t des -k 0000000000000000 --kdf none --aid 123456 --fid 01 -> execute with default factory setup");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID (3 hex bytes, big endian)"),
-        arg_str0(NULL, "fid",     "<file id hex>", "File ID (1 hex byte)"),
-        arg_str0(NULL, "amode",   "<plain/mac/encrypt>", "File access mode: plain/mac/encrypt"),
-        arg_str0(NULL, "rawrights", "<access rights HEX>", "Access rights for file (HEX 2 byte) R/W/RW/Chg, 0x0 - 0xD Key, 0xE Free, 0xF Denied"),
-        arg_str0(NULL, "rrights", "<key0/../key13/free/deny>", "Read file access mode: the specified key, free, deny"),
-        arg_str0(NULL, "wrights", "<key0/../key13/free/deny>", "Write file access mode: the specified key, free, deny"),
-        arg_str0(NULL, "rwrights", "<key0/../key13/free/deny>", "Read/Write file access mode: the specified key, free, deny"),
-        arg_str0(NULL, "chrights", "<key0/../key13/free/deny>", "Change file settings access mode: the specified key, free, deny"),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (3 hex bytes, big endian)"),
+        arg_str0(NULL, "fid",     "<hex>", "File ID (1 hex byte)"),
+        arg_str0(NULL, "amode",   "<plain|mac|encrypt>", "File access mode"),
+        arg_str0(NULL, "rawrights", "<hex>", "Access rights for file (2 hex bytes) R/W/RW/Chg, 0x0 - 0xD Key, 0xE Free, 0xF Denied"),
+        arg_str0(NULL, "rrights",  "<key0..key13|free|deny>", "Read file access mode: the specified key, free, deny"),
+        arg_str0(NULL, "wrights",  "<key0..key13|free|deny>", "Write file access mode: the specified key, free, deny"),
+        arg_str0(NULL, "rwrights", "<key0..key13|free|deny>", "Read/Write file access mode: the specified key, free, deny"),
+        arg_str0(NULL, "chrights", "<key0..key13|free|deny>", "Change file settings access mode: the specified key, free, deny"),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
         arg_str0(NULL, "lower",   "<hex>", "Lower limit (4 hex bytes, big endian)"),
         arg_str0(NULL, "upper",   "<hex>", "Upper limit (4 hex bytes, big endian)"),
         arg_str0(NULL, "value",   "<hex>", "Value (4 hex bytes, big endian)"),
@@ -3904,31 +3918,31 @@ static int CmdHF14ADesCreateRecordFile(const char *Cmd) {
                   "Key/mode/etc of the authentication depends on application settings\n"
                   "hf mfdes createrecordfile --aid 123456 --fid 01 --size 000010 --maxrecord 000010 --cyclic -> create cyclic record file with parameters. Rights from default. Authentication with defaults from `default` command\n"
                   "hf mfdes createrecordfile --aid 123456 --fid 01 --amode plain --rrights free --wrights free --rwrights free --chrights key0 --size 000010 --maxrecord 000010 -> create linear record file app=123456, file=01 and mentioned rights with defaults from `default` command\n"
-                  "hf mfdes createrecordfile -n 0 -t des -k 0000000000000000 -f none --aid 123456 --fid 01 --size 000010 --maxrecord 000010 -> execute with default factory setup");
+                  "hf mfdes createrecordfile -n 0 -t des -k 0000000000000000 --kdf none --aid 123456 --fid 01 --size 000010 --maxrecord 000010 -> execute with default factory setup");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID (3 hex bytes, big endian)"),
-        arg_str0(NULL, "fid",     "<file id hex>", "File ID (1 hex byte)"),
-        arg_str0(NULL, "isofid",  "<iso file id hex>", "ISO File ID (2 hex bytes)"),
-        arg_str0(NULL, "amode",   "<plain/mac/encrypt>", "File access mode: plain/mac/encrypt"),
-        arg_str0(NULL, "rawrights", "<access rights HEX>", "Access rights for file (HEX 2 byte) R/W/RW/Chg, 0x0 - 0xD Key, 0xE Free, 0xF Denied"),
-        arg_str0(NULL, "rrights", "<key0/../key13/free/deny>", "Read file access mode: the specified key, free, deny"),
-        arg_str0(NULL, "wrights", "<key0/../key13/free/deny>", "Write file access mode: the specified key, free, deny"),
-        arg_str0(NULL, "rwrights", "<key0/../key13/free/deny>", "Read/Write file access mode: the specified key, free, deny"),
-        arg_str0(NULL, "chrights", "<key0/../key13/free/deny>", "Change file settings access mode: the specified key, free, deny"),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
-        arg_str0(NULL, "size",    "<hex>", "Record size (3 hex bytes, big endian, 000001 to FFFFFF)"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (3 hex bytes, big endian)"),
+        arg_str0(NULL, "fid",     "<hex>", "File ID (1 hex byte)"),
+        arg_str0(NULL, "isofid",  "<hex>", "ISO File ID (2 hex bytes)"),
+        arg_str0(NULL, "amode",   "<plain|mac|encrypt>", "File access mode"),
+        arg_str0(NULL, "rawrights", "<hex>", "Access rights for file (2 hex bytes) R/W/RW/Chg, 0x0 - 0xD Key, 0xE Free, 0xF Denied"),
+        arg_str0(NULL, "rrights",  "<key0..key13|free|deny>", "Read file access mode: the specified key, free, deny"),
+        arg_str0(NULL, "wrights",  "<key0..key13|free|deny>", "Write file access mode: the specified key, free, deny"),
+        arg_str0(NULL, "rwrights", "<key0..key13|free|deny>", "Read/Write file access mode: the specified key, free, deny"),
+        arg_str0(NULL, "chrights", "<key0..key13|free|deny>", "Change file settings access mode: the specified key, free, deny"),
+        arg_lit0(NULL, "no-auth",   "Execute without authentication"),
+        arg_str0(NULL, "size",      "<hex>", "Record size (3 hex bytes, big endian, 000001 to FFFFFF)"),
         arg_str0(NULL, "maxrecord", "<hex>", "Max. Number of Records (3 hex bytes, big endian)"),
         arg_lit0(NULL, "cyclic", "Create cyclic record file instead of linear record file"),
         arg_param_end
@@ -4021,34 +4035,34 @@ static int CmdHF14ADesCreateTrMACFile(const char *Cmd) {
                   "\n"
                   "hf mfdes createmacfile --aid 123456 --fid 01 --rawrights 0FF0 --mackey 00112233445566778899aabbccddeeff --mackeyver 01 -> create transaction mac file with parameters. Rights from default. Authentication with defaults from `default` command\n"
                   "hf mfdes createmacfile --aid 123456 --fid 01 --amode plain --rrights free --wrights deny --rwrights free --chrights key0 --mackey 00112233445566778899aabbccddeeff -> create file app=123456, file=01, with key, and mentioned rights with defaults from `default` command\n"
-                  "hf mfdes createmacfile -n 0 -t des -k 0000000000000000 -f none --aid 123456 --fid 01 -> execute with default factory setup. key and keyver == 0x00..00\n"
-                  "hf mfdes createmacfile --appisoid df01 --fid 0f -s lrp -t aes --rawrights 0FF0 --mackey 00112233445566778899aabbccddeeff --mackeyver 01 -> create transaction mac file via lrp channel\n"
-                  "hf mfdes createmacfile --appisoid df01 --fid 0f -s lrp -t aes --rawrights 0F10 --mackey 00112233445566778899aabbccddeeff --mackeyver 01 -> create transaction mac file via lrp channel with CommitReaderID command enable");
+                  "hf mfdes createmacfile -n 0 -t des -k 0000000000000000 --kdf none --aid 123456 --fid 01 -> execute with default factory setup. key and keyver == 0x00..00\n"
+                  "hf mfdes createmacfile --isoid df01 --fid 0f -s lrp -t aes --rawrights 0FF0 --mackey 00112233445566778899aabbccddeeff --mackeyver 01 -> create transaction mac file via lrp channel\n"
+                  "hf mfdes createmacfile --isoid df01 --fid 0f -s lrp -t aes --rawrights 0F10 --mackey 00112233445566778899aabbccddeeff --mackeyver 01 -> create transaction mac file via lrp channel with CommitReaderID command enable");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",      "show APDU requests and responses"),
-        arg_lit0("v",  "verbose",   "show technical data"),
-        arg_int0("n",  "keyno",     "<keyno>", "Key number"),
-        arg_str0("t",  "algo",      "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",       "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",       "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",      "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",     "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",     "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",    "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",       "<app id hex>", "Application ID (3 hex bytes, big endian)"),
-        arg_str0(NULL, "appisoid", "<isoid hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
-        arg_str0(NULL, "fid",       "<file id hex>", "File ID (1 hex byte)"),
-        arg_str0(NULL, "amode",     "<plain/mac/encrypt>", "File access mode: plain/mac/encrypt"),
-        arg_str0(NULL, "rawrights", "<access rights HEX>", "Access rights for file (HEX 2 byte) R/W/RW/Chg, 0x0 - 0xD Key, 0xE Free, 0xF Denied"),
-        arg_str0(NULL, "rrights",   "<key0/../key13/free/deny>", "Read file access mode: the specified key, free, deny"),
-        arg_str0(NULL, "wrights",   "<key0/../key13/free/deny>", "Write file access mode: the specified key, free, deny"),
-        arg_str0(NULL, "rwrights",  "<key0/../key13/free/deny>", "Read/Write file access mode: the specified key, free, deny"),
-        arg_str0(NULL, "chrights",  "<key0/../key13/free/deny>", "Change file settings access mode: the specified key, free, deny"),
-        arg_lit0(NULL, "no-auth",   "execute without authentication"),
-        arg_str0(NULL, "mackey",    "<hex>", "AES-128 key for MAC (16 hex bytes, big endian). Default 0x00..00"),
-        arg_str0(NULL, "mackeyver", "<ver hex 1b>", "AES key version for MAC (1 hex byte). Default 0x00"),
+        arg_lit0("a",  "apdu",      "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose",   "Verbose mode"),
+        arg_int0("n",  "keyno",     "<dec>", "Key number"),
+        arg_str0("t",  "algo",      "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",       "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",       "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",      "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",     "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",     "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",    "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",       "<hex>", "Application ID (3 hex bytes, big endian)"),
+        arg_str0(NULL, "isoid",     "<hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)"),
+        arg_str0(NULL, "fid",       "<hex>", "File ID (1 hex byte)"),
+        arg_str0(NULL, "amode",     "<plain|mac|encrypt>", "File access mode"),
+        arg_str0(NULL, "rawrights", "<hex>", "Access rights for file (2 hex bytes) R/W/RW/Chg, 0x0 - 0xD Key, 0xE Free, 0xF Denied"),
+        arg_str0(NULL, "rrights",   "<key0..key13|free|deny>", "Read file access mode: the specified key, free, deny"),
+        arg_str0(NULL, "wrights",   "<key0..key13|free|deny>", "Write file access mode: the specified key, free, deny"),
+        arg_str0(NULL, "rwrights",  "<key0..key13|free|deny>", "Read/Write file access mode: the specified key, free, deny"),
+        arg_str0(NULL, "chrights",  "<key0..key13|free|deny>", "Change file settings access mode: the specified key, free, deny"),
+        arg_lit0(NULL, "no-auth",   "Execute without authentication"),
+        arg_str0(NULL, "mackey",    "<hex>", "AES-128 key for MAC (16 hex bytes, big endian). (def: all zeros)"),
+        arg_str0(NULL, "mackeyver", "<hex>", "AES key version for MAC (1 hex byte). (def: 0x0)"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -4140,24 +4154,24 @@ static int CmdHF14ADesDeleteFile(const char *Cmd) {
     CLIParserInit(&ctx, "hf mfdes deletefile",
                   "Delete file from application. Master key needs to be provided or flag --no-auth set (depend on cards settings).",
                   "hf mfdes deletefile --aid 123456 --fid 01 -> delete file for: app=123456, file=01 with defaults from `default` command\n"
-                  "hf mfdes deletefile --appisoid df01 --fid 0f -s lrp -t aes -> delete file for lrp channel");
+                  "hf mfdes deletefile --isoid df01 --fid 0f -s lrp -t aes -> delete file for lrp channel");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID (3 hex bytes, big endian)"),
-        arg_str0(NULL, "appisoid", "<isoid hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
-        arg_str0(NULL, "fid",     "<file id hex>", "File ID (1 hex byte)"),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (3 hex bytes, big endian)"),
+        arg_str0(NULL, "isoid",   "<hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)"),
+        arg_str0(NULL, "fid",     "<hex>", "File ID (1 hex byte)"),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -4216,28 +4230,28 @@ static int CmdHF14ADesValueOperations(const char *Cmd) {
                   "Get File Settings from file from application. Master key needs to be provided or flag --no-auth set (depend on cards settings).",
                   "hf mfdes value --aid 123456 --fid 01  -> get value app=123456, file=01 with defaults from `default` command\n"
                   "hf mfdes value --aid 123456 --fid 01 --op credit -d 00000001 -> credit value app=123456, file=01 with defaults from `default` command\n"
-                  "hf mfdes value -n 0 -t des -k 0000000000000000 -f none --aid 123456 --fid 01 -> get value with default factory setup\n"
-                  "hf mfdes val --appisoid df01 --fid 03 -s lrp -t aes -n 1 --op credit --d 00000001 -m encrypt -> credit value in the lrp encrypted mode\n"
-                  "hf mfdes val --appisoid df01 --fid 03 -s lrp -t aes -n 1 --op get -m plain -> get value in plain (nevertheless of mode) works for desfire light (look SetConfiguration option 0x09)");
+                  "hf mfdes value -n 0 -t des -k 0000000000000000 --kdf none --aid 123456 --fid 01 -> get value with default factory setup\n"
+                  "hf mfdes val --isoid df01 --fid 03 -s lrp -t aes -n 1 --op credit --d 00000001 -m encrypt -> credit value in the lrp encrypted mode\n"
+                  "hf mfdes val --isoid df01 --fid 03 -s lrp -t aes -n 1 --op get -m plain -> get value in plain (nevertheless of mode) works for desfire light (look SetConfiguration option 0x09)");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID (3 hex bytes, big endian)"),
-        arg_str0(NULL, "appisoid", "<isoid hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
-        arg_str0(NULL, "fid",     "<file id hex>", "File ID (1 hex byte)"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (3 hex bytes, big endian)"),
+        arg_str0(NULL, "isoid",   "<hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)"),
+        arg_str0(NULL, "fid",     "<hex>", "File ID (1 hex byte)"),
         arg_str0("o",  "op",      "<get/credit/limcredit/debit/clear>", "Operation: get(default)/credit/limcredit(limited credit)/debit/clear. Operation clear: get-getopt-debit to min value"),
-        arg_str0("d",  "data",    "<value HEX>", "Value for operation (HEX 4 bytes)"),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
+        arg_str0("d",  "data",    "<hex>", "Value for operation (HEX 4 bytes)"),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -4390,24 +4404,24 @@ static int CmdHF14ADesClearRecordFile(const char *Cmd) {
     CLIParserInit(&ctx, "hf mfdes clearrecfile",
                   "Clear record file. Master key needs to be provided or flag --no-auth set (depend on cards settings).",
                   "hf mfdes clearrecfile --aid 123456 --fid 01 -> clear record file for: app=123456, file=01 with defaults from `default` command\n"
-                  "hf mfdes clearrecfile --appisoid df01 --fid 01 -s lrp -t aes -n 3 -> clear record file for lrp channel with key number 3");
+                  "hf mfdes clearrecfile --isoid df01 --fid 01 -s lrp -t aes -n 3 -> clear record file for lrp channel with key number 3");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID (3 hex bytes, big endian)"),
-        arg_str0(NULL, "appisoid", "<isoid hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
-        arg_str0(NULL, "fid",     "<file id hex>", "File ID for clearing (1 hex byte)"),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (3 hex bytes, big endian)"),
+        arg_str0(NULL, "isoid",   "<hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)"),
+        arg_str0(NULL, "fid",     "<hex>", "File ID for clearing (1 hex byte)"),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -4786,32 +4800,32 @@ static int CmdHF14ADesReadData(const char *Cmd) {
                   "hf mfdes read --aid 123456 --fid 01 --type record --offset 000000 --length 000001 -> read one last record from record file. use default channel settings from `default` command\n"
                   "hf mfdes read --aid 123456 --fid 10 --type data -c iso -> read file via ISO channel: app=123456, short iso id=10, offset=0.\n"
                   "hf mfdes read --aid 123456 --fileisoid 1000 --type data -c iso -> read file via ISO channel: app=123456, iso id=1000, offset=0. Select via native ISO wrapper\n"
-                  "hf mfdes read --appisoid 0102 --fileisoid 1000 --type data -c iso -> read file via ISO channel: app iso id=0102, iso id=1000, offset=0. Select via ISO commands\n"
-                  "hf mfdes read --appisoid 0102 --fileisoid 1100 --type record -c iso --offset 000005 --length 000001 -> get one record (number 5) from file 1100 via iso commands\n"
-                  "hf mfdes read --appisoid 0102 --fileisoid 1100 --type record -c iso --offset 000005 --length 000000 -> get all record (from 5 to 1) from file 1100 via iso commands\n"
-                  "hf mfdes read --appisoid df01 --fid 00 -s lrp -t aes --length 000010 -> read via lrp channel\n"
-                  "hf mfdes read --appisoid df01 --fid 00 -s ev2 -t aes --length 000010 --isochain -> read Desfire Light via ev2 channel");
+                  "hf mfdes read --isoid 0102 --fileisoid 1000 --type data -c iso -> read file via ISO channel: app iso id=0102, iso id=1000, offset=0. Select via ISO commands\n"
+                  "hf mfdes read --isoid 0102 --fileisoid 1100 --type record -c iso --offset 000005 --length 000001 -> get one record (number 5) from file 1100 via iso commands\n"
+                  "hf mfdes read --isoid 0102 --fileisoid 1100 --type record -c iso --offset 000005 --length 000000 -> get all record (from 5 to 1) from file 1100 via iso commands\n"
+                  "hf mfdes read --isoid df01 --fid 00 -s lrp -t aes --length 000010 -> read via lrp channel\n"
+                  "hf mfdes read --isoid df01 --fid 00 -s ev2 -t aes --length 000010 --isochain -> read Desfire Light via ev2 channel");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID (3 hex bytes, big endian)"),
-        arg_str0(NULL, "fid",     "<file id hex>", "File ID (1 hex byte)"),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
-        arg_str0(NULL, "type",    "<auto/data/value/record/mac>", "File Type auto/data(Standard/Backup)/value/record(linear/cyclic)/mac). Auto - check file settings and then read. Default: auto"),
-        arg_str0("o", "offset",   "<hex>", "File Offset (3 hex bytes, big endian). For records - record number (0 - lastest record). Default 0"),
-        arg_str0("l", "length",   "<hex>", "Length to read (3 hex bytes, big endian -> 000000 = Read all data). For records - records count (0 - all). Default 0."),
-        arg_str0(NULL, "appisoid", "<isoid hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
-        arg_str0(NULL, "fileisoid", "<isoid hex>", "File ISO ID (ISO DF ID) (2 hex bytes, big endian). Works only for ISO read commands."),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL,  "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (3 hex bytes, big endian)"),
+        arg_str0(NULL, "fid",     "<hex>", "File ID (1 hex byte)"),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
+        arg_str0(NULL, "type",    "<auto|data|value|record|mac>", "File Type, Auto - check file settings and then read. (def: auto)"),
+        arg_str0("o", "offset",   "<hex>", "File Offset (3 hex bytes, big endian). For records - record number (0 - lastest record). (def: 0)"),
+        arg_str0("l", "length",   "<hex>", "Length to read (3 hex bytes, big endian -> 000000 = Read all data). For records - records count (0 - all). (def: 0)"),
+        arg_str0(NULL, "isoid",     "<hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)"),
+        arg_str0(NULL, "fileisoid", "<hex>", "File ISO ID (ISO DF ID) (2 hex bytes, big endian). Works only for ISO read commands"),
         arg_lit0(NULL, "isochain", "use iso chaining commands. Switched on by default if secure channel = lrp"),
         arg_param_end
     };
@@ -4958,36 +4972,36 @@ static int CmdHF14ADesWriteData(const char *Cmd) {
                   "hf mfdes write --aid 123456 --fid 01 --type record -d 01020304 -> write data to record file\n"
                   "hf mfdes write --aid 123456 --fid 01 --type record -d 01020304 --updaterec 0 -> update record in the record file. record 0 - lastest record.\n"
                   "hf mfdes write --aid 123456 --fid 01 --type record --offset 000000 -d 11223344 -> write record to record file. use default channel settings from `default` command\n"
-                  "hf mfdes write --appisoid 1234 --fileisoid 1000 --type data -c iso -d 01020304 -> write data to std/backup file via iso commandset\n"
-                  "hf mfdes write --appisoid 1234 --fileisoid 2000 --type record -c iso -d 01020304 -> send record to record file via iso commandset\n"
+                  "hf mfdes write --isoid 1234 --fileisoid 1000 --type data -c iso -d 01020304 -> write data to std/backup file via iso commandset\n"
+                  "hf mfdes write --isoid 1234 --fileisoid 2000 --type record -c iso -d 01020304 -> send record to record file via iso commandset\n"
                   "hf mfdes write --aid 123456 --fid 01 -d 01020304 --readerid 010203 -> write data to file with CommitReaderID command before write and CommitTransaction after write\n"
-                  "hf mfdes write --appisoid df01 --fid 04 -d 01020304 --trkey 00112233445566778899aabbccddeeff --readerid 5532 -t aes -s lrp -> advanced CommitReaderID via lrp channel sample");
+                  "hf mfdes write --isoid df01 --fid 04 -d 01020304 --trkey 00112233445566778899aabbccddeeff --readerid 5532 -t aes -s lrp -> advanced CommitReaderID via lrp channel sample");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID (3 hex bytes, big endian)"),
-        arg_str0(NULL, "fid",     "<file id hex>", "File ID (1 hex byte)"),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
-        arg_str0(NULL, "type",    "<auto/data/value/record/mac>", "File Type auto/data(Standard/Backup)/value/record(linear/cyclic)/mac). Auto - check file settings and then write. Default: auto"),
-        arg_str0("o",  "offset",  "<hex>", "File Offset (3 hex bytes, big endian). For records - record number (0 - lastest record). Default 0"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (3 hex bytes, big endian)"),
+        arg_str0(NULL, "fid",     "<hex>", "File ID (1 hex byte)"),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
+        arg_str0(NULL, "type",    "<auto|data|value|record|mac>", "File Type, Auto - check file settings and then write. (def: auto)"),
+        arg_str0("o",  "offset",  "<hex>", "File Offset (3 hex bytes, big endian). For records - record number (0 - lastest record). (def: 0)"),
         arg_str0("d",  "data",    "<hex>", "data for write (data/record file), credit/debit(value file)"),
         arg_lit0(NULL, "debit",   "use for value file debit operation instead of credit"),
-        arg_lit0(NULL, "commit",  "commit needs for backup file only. For the other file types and in the `auto` mode - command set it automatically."),
-        arg_int0(NULL, "updaterec", "<record number dec>", "Record number for update record command. Updates record instead of write. Lastest record - 0"),
-        arg_str0(NULL, "appisoid", "<isoid hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
-        arg_str0(NULL, "fileisoid", "<isoid hex>", "File ISO ID (ISO DF ID) (2 hex bytes, big endian). Works only for ISO write commands."),
-        arg_str0(NULL, "readerid", "<hex>", "reader id for CommitReaderID command. If present - the command issued before write command."),
-        arg_str0(NULL, "trkey",   "<hex>", "key for decode previous reader id."),
+        arg_lit0(NULL, "commit",  "commit needs for backup file only. For the other file types and in the `auto` mode - command set it automatically"),
+        arg_int0(NULL, "updaterec", "<dec>", "Record number for update record command. Updates record instead of write. Lastest record - 0"),
+        arg_str0(NULL, "isoid"   ,  "<hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)"),
+        arg_str0(NULL, "fileisoid", "<hex>", "File ISO ID (ISO DF ID) (2 hex bytes, big endian). Works only for ISO write commands"),
+        arg_str0(NULL, "readerid",  "<hex>", "reader id for CommitReaderID command. If present - the command issued before write command"),
+        arg_str0(NULL, "trkey",     "<hex>", "key for decode previous reader id"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -5298,24 +5312,24 @@ static int CmdHF14ADesLsFiles(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfdes lsfiles",
                   "Show file list. Master key needs to be provided or flag --no-auth set (depend on cards settings).",
-                  "hf mfdes lsfiles --aid 123456 -> show file list for: app=123456 with defaults from `default` command"
-                  "hf mfdes lsfiles --appisoid df01 --no-auth -> show files from desfire light");
+                  "hf mfdes lsfiles --aid 123456     -> show file list for: app=123456 with defaults from `default` command"
+                  "hf mfdes lsfiles --isoid df01 --no-auth    -> show files from desfire light");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID (3 hex bytes, big endian)"),
-        arg_str0(NULL, "appisoid", "<isoid hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (3 hex bytes, big endian)"),
+        arg_str0(NULL, "isoid",   "<hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)"),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -5371,24 +5385,24 @@ static int CmdHF14ADesLsApp(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfdes lsapp",
                   "Show application list. Master key needs to be provided or flag --no-auth set (depend on cards settings).",
-                  "hf mfdes lsapp -> show application list with defaults from `default` command\n"
-                  "hf mfdes lsapp --files -> show application list and show each file type/settings/etc for each application");
+                  "hf mfdes lsapp           -> show application list with defaults from `default` command\n"
+                  "hf mfdes lsapp --files   -> show application list and show each file type/settings/etc");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
         arg_lit0(NULL, "no-deep", "not to check authentication commands that avail for any application"),
-        arg_lit0(NULL, "files",   "scan files and print file settings for each application"),
+        arg_lit0(NULL, "files",   "scan files and print file settings"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -5437,25 +5451,25 @@ static int CmdHF14ADesDump(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfdes dump",
                   "For each application show fil list and then file content. Key needs to be provided for authentication or flag --no-auth set (depend on cards settings).",
-                  "hf mfdes dump --aid 123456 -> show file dump for: app=123456 with channel defaults from `default` command/n"
-                  "hf mfdes dump --appisoid df01 -s lrp -t aes --length 000090 -> lrp default settings with length limit");
+                  "hf mfdes dump --aid 123456     -> show file dump for: app=123456 with channel defaults from `default` command/n"
+                  "hf mfdes dump --isoid df01 -s lrp -t aes --length 000090    -> lrp default settings with length limit");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
-        arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID (3 hex bytes, big endian)"),
-        arg_str0(NULL, "appisoid", "<isoid hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
-        arg_str0("l", "length",   "<hex>", "Maximum length for read data files (3 hex bytes, big endian)."),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Verbose mode"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0(NULL, "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF)"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (1-31 hex bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set"),
+        arg_str0(NULL, "schann",  "<d40|ev1|ev2|lrp>", "Secure channel"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID (3 hex bytes, big endian)"),
+        arg_str0(NULL, "isoid",   "<hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)"),
+        arg_str0("l", "length",   "<hex>", "Maximum length for read data files (3 hex bytes, big endian)"),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
diff --git a/client/src/mifare/desfirecore.c b/client/src/mifare/desfirecore.c
index b4047c264..386f528e2 100644
--- a/client/src/mifare/desfirecore.c
+++ b/client/src/mifare/desfirecore.c
@@ -2252,10 +2252,11 @@ static const DesfireCreateFileCommands_t DesfireFileCommands[] = {
 };
 
 const DesfireCreateFileCommands_t *GetDesfireFileCmdRec(uint8_t type) {
-    for (int i = 0; i < ARRAYLEN(DesfireFileCommands); i++)
-        if (DesfireFileCommands[i].id == type)
+    for (int i = 0; i < ARRAYLEN(DesfireFileCommands); i++) {
+        if (DesfireFileCommands[i].id == type) {
             return &DesfireFileCommands[i];
-
+        }
+    }
     return NULL;
 }
 
@@ -2303,6 +2304,7 @@ const char *GetDesfireAccessRightStr(uint8_t right) {
         sprintf(int_access_str, "key 0x%02x", right);
         return int_access_str;
     }
+
     if (right == 0x0e)
         return DesfireFreeStr;
 
@@ -2332,8 +2334,9 @@ const char *AccessRightShortStr[] = {
 };
 
 const char *GetDesfireAccessRightShortStr(uint8_t right) {
-    if (right > 0x0f)
+    if (right > 0x0F) {
         return DesfireNAStr;
+    }
 
     return AccessRightShortStr[right];
 }
@@ -2346,23 +2349,20 @@ void DesfireEncodeFileAcessMode(uint8_t *mode, uint8_t r, uint8_t w, uint8_t rw,
 void DesfireDecodeFileAcessMode(const uint8_t *mode, uint8_t *r, uint8_t *w, uint8_t *rw, uint8_t *ch) {
     // read
     if (r)
-        *r = (mode[1] >> 4) & 0x0f; // hi 2b
+        *r = (mode[1] >> 4) & 0x0F; // hi 2b
     // write
     if (w)
-        *w = mode[1] & 0x0f;
+        *w = mode[1] & 0x0F;
     // read/write
     if (rw)
-        *rw = (mode[0] >> 4) & 0x0f; // low 2b
+        *rw = (mode[0] >> 4) & 0x0F; // low 2b
     // change
     if (ch)
-        *ch = mode[0] & 0x0f;
+        *ch = mode[0] & 0x0F;
 }
 
 void DesfirePrintAccessRight(uint8_t *data) {
-    uint8_t r = 0;
-    uint8_t w = 0;
-    uint8_t rw = 0;
-    uint8_t ch = 0;
+    uint8_t r = 0, w = 0, rw = 0, ch = 0;
     DesfireDecodeFileAcessMode(data, &r, &w, &rw, &ch);
     PrintAndLogEx(SUCCESS, "read     : %s", GetDesfireAccessRightStr(r));
     PrintAndLogEx(SUCCESS, "write    : %s", GetDesfireAccessRightStr(w));
