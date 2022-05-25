commit a7d3c15c170a362c8f7b7412f6f895b3a6a315a4
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 22 18:36:18 2022 +0100

    hf mfdes createapp - made help text according to our standard.  Still too long parameter descriptions

diff --git a/client/src/cmdhfmfdes.c b/client/src/cmdhfmfdes.c
index e0cf6b400..95736bcd5 100644
--- a/client/src/cmdhfmfdes.c
+++ b/client/src/cmdhfmfdes.c
@@ -2346,37 +2346,37 @@ static int CmdHF14ADesCreateApp(const char *Cmd) {
                   "  0..3: Number of keys stored within the application (max. 14 keys)\n"\
                   "  4:    ks3 is present\n"\
                   "  5:    Use of 2 byte ISO FID, 0: No, 1: Yes\n"\
-                  "  6..7: Crypto Method 00: DES/2TDEA, 01: 3TDEA, 10: AES, 11: RFU\n"\
+                  "  6..7: Crypto Method 00: DES|2TDEA, 01: 3TDEA, 10: AES, 11: RFU\n"\
                   "  Example:\n"\
-                  "       2E = with FID, DES/2TDEA, 14 keys\n"\
+                  "       2E = with FID, DES|2TDEA, 14 keys\n"\
                   "       6E = with FID, 3TDEA, 14 keys\n"\
                   "       AE = with FID, AES, 14 keys\n"\
                   "\n"\
-                  "hf mfdes createapp --rawdata 5634122F2E4523616964313233343536 -> execute create by rawdata\n"\
-                  "hf mfdes createapp --aid 123456 --fid 2345 --dfname aid123456 -> app aid, iso file id, and iso df name is specified\n"
+                  "hf mfdes createapp --rawdata 5634122F2E4523616964313233343536      -> execute create by rawdata\n"\
+                  "hf mfdes createapp --aid 123456 --fid 2345 --dfname aid123456      -> app aid, iso file id, and iso df name is specified\n"
                   "hf mfdes createapp --aid 123456 --fid 2345 --dfname aid123456 --dstalgo aes -> with algorithm for key AES");
 
     void *argtable[] = {
         arg_param_begin,
         arg_lit0("a",  "apdu",    "show APDU requests and responses"),
         arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  "keyno",   "<keyno>", "Key number"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<Key>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-        arg_str0("f",  "kdf",     "<none/AN10922/gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
+        arg_int0("n",  "keyno",   "<dec>", "Key number"),
+        arg_str0("t",  "algo",    "<DES|2TDEA|3TDEA|AES>",  "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
+        arg_str0("k",  "key",     "<hex>",   "Key for authenticate (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
+        arg_str0("f",  "kdf",     "<none|AN10922|gallagher>",   "Key Derivation Function (KDF): None, AN10922, Gallagher"),
         arg_str0("i",  "kdfi",    "<kdfi>",  "KDF input (HEX 1-31 bytes)"),
-        arg_str0("m",  "cmode",   "<plain/mac/encrypt>", "Communicaton mode: plain/mac/encrypt"),
-        arg_str0("c",  "ccset",   "<native/niso/iso>", "Communicaton command set: native/niso/iso"),
-        arg_str0("s",  "schann",  "<d40/ev1/ev2/lrp>", "Secure channel: d40/ev1/ev2/lrp"),
-        arg_str0(NULL, "rawdata", "<rawdata hex>", "Rawdata that sends to command"),
-        arg_str0(NULL, "aid",     "<app id hex>", "Application ID for create. Mandatory. (3 hex bytes, big endian)"),
-        arg_str0(NULL, "fid",     "<file id hex>", "ISO file ID. Forbidden values: 0000 3F00, 3FFF, FFFF. (2 hex bytes, big endian). If specified - enable iso file id over all the files in the app."),
-        arg_str0(NULL, "dfname",  "<df name str>", "ISO DF Name 1..16 chars string"),
-        arg_str0(NULL, "ks1",     "<key settings HEX>", "Key settings 1 (HEX 1 byte). Application Master Key Settings. default 0x0f"),
-        arg_str0(NULL, "ks2",     "<key settings HEX>", "Key settings 2 (HEX 1 byte). default 0x0e"),
-        arg_str0(NULL, "dstalgo", "<DES/2TDEA/3TDEA/AES>",  "Application key crypt algo: DES, 2TDEA, 3TDEA, AES. default DES"),
-        arg_int0(NULL, "numkeys", "<number of keys>",  "Keys count. 0x00..0x0e. default 0x0e"),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
+        arg_str0("m",  "cmode",   "<plain|mac|encrypt>", "Communicaton mode: plain/mac/encrypt"),
+        arg_str0("c",  "ccset",   "<native|niso|iso>", "Communicaton command set: native/niso/iso"),
+        arg_str0("s",  "schann",  "<d40|ev1|ev2|lrp>", "Secure channel: d40/ev1/ev2/lrp"),
+        arg_str0(NULL, "rawdata", "<hex>", "Raw data that sends to command"),
+        arg_str0(NULL, "aid",     "<hex>", "Application ID for create. Mandatory. (3 hex bytes, big endian)"),
+        arg_str0(NULL, "fid",     "<hex>", "ISO file ID. Forbidden values: 0000 3F00, 3FFF, FFFF. (2 hex bytes, big endian). If specified - enable iso file id over all the files in the app."),
+        arg_str0(NULL, "dfname",  "<string>", "ISO DF Name 1..16 chars string"),
+        arg_str0(NULL, "ks1",     "<hex>", "Key settings 1 (HEX 1 byte). Application Master Key Settings. default 0x0f"),
+        arg_str0(NULL, "ks2",     "<hex>", "Key settings 2 (HEX 1 byte). default 0x0e"),
+        arg_str0(NULL, "dstalgo", "<DES|2TDEA|3TDEA|AES>",  "Application key crypt algo: DES, 2TDEA, 3TDEA, AES. default DES"),
+        arg_int0(NULL, "numkeys", "<dec>",  "Number of keys 0x00..0x0e. default 0x0e"),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
