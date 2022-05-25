commit e6a14bf86910bc35931e0e811b0bb41ef6b38366
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jan 30 21:16:57 2022 +0100

    text

diff --git a/client/src/cmdhfmfdes.c b/client/src/cmdhfmfdes.c
index 5afa3917e..06482695d 100644
--- a/client/src/cmdhfmfdes.c
+++ b/client/src/cmdhfmfdes.c
@@ -423,13 +423,17 @@ static int CmdDesGetSessionParameters(CLIParserContext *ctx, DesfireContext_t *d
     int algores = defaultAlgoId;
     uint8_t key[DESFIRE_MAX_KEY_SIZE] = {0};
     memcpy(key, defaultKey, DESFIRE_MAX_KEY_SIZE);
+
     int kdfAlgo = defaultKdfAlgo;
+
     int kdfInputLen = defaultKdfInputLen;
     uint8_t kdfInput[50] = {0};
     memcpy(kdfInput, defaultKdfInput, defaultKdfInputLen);
+
     int commmode = defaultCommMode;
     if (defcommmode != DCMNone)
         commmode = defcommmode;
+
     int commset = defaultCommSet;
     int secchann = defaultSecureChannel;
 
@@ -2012,16 +2016,16 @@ static int CmdHF14ADesAuth(const char *Cmd) {
         arg_param_begin,
         arg_lit0("a",  "apdu",    "show APDU requests and responses"),
         arg_lit0("v",  "verbose", "show technical data"),
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
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0("f",  "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
+        arg_str0("i",  "kdfi",    "<hex>",  "KDF input (HEX 1-31 bytes)"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode: plain/mac/encrypt"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set: native/niso/iso"),
+        arg_str0("s",  "schann",  "<d40|ev1|ev2|lrp>", "Secure channel: d40/ev1/ev2/lrp"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID of application for some parameters (3 hex bytes, big endian)"),
+        arg_str0(NULL, "appisoid", "<hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)."),
         arg_lit0(NULL, "save",    "saves channels parameters to defaults if authentication succeeds"),
         arg_param_end
     };
