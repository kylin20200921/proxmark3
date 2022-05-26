commit 8141752d695fc44316a15fe7ff11df538dea5ab9
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Feb 24 19:19:07 2022 +0100

    texts

diff --git a/client/src/cmdhfcipurse.c b/client/src/cmdhfcipurse.c
index 3c2791cb9..9db4d1cc4 100644
--- a/client/src/cmdhfcipurse.c
+++ b/client/src/cmdhfcipurse.c
@@ -571,10 +571,7 @@ static int CmdHFCipurseAuth(const char *Cmd) {
     bool bres = CIPURSEChannelAuthenticate(keyId, key, verbose);
 
     if (verbose == false) {
-        if (bres)
-            PrintAndLogEx(INFO, "Authentication ( " _GREEN_("ok") " )");
-        else
-            PrintAndLogEx(ERR, "Authentication ( " _RED_("fail") " )");
+        PrintAndLogEx(INFO, "Authentication ( %s ) ", (bres) ?  _GREEN_("ok") :  _RED_("fail"));
     }
 
     DropField();
@@ -709,8 +706,8 @@ static int CmdHFCipurseWriteFile(const char *Cmd) {
         arg_str0(NULL, "fid",     "<hex>", "File ID"),
         arg_int0("o",  "offset",  "<dec>", "Offset for reading data from file"),
         arg_lit0(NULL, "noauth",  "Read file without authentication"),
-        arg_str0(NULL, "sreq",    "<plain|mac|encode>", "communication reader-PICC security level (def: mac)"),
-        arg_str0(NULL, "sresp",   "<plain|mac|encode>", "communication PICC-reader security level (def: mac)"),
+        arg_str0(NULL, "sreq",    "<plain|mac|encode>", "Communication reader-PICC security level (def: mac)"),
+        arg_str0(NULL, "sresp",   "<plain|mac|encode>", "Communication PICC-reader security level (def: mac)"),
         arg_str0("d",  "data",    "<hex>", "Data to write to new file"),
         arg_lit0(NULL, "commit",  "Commit after write"),
         arg_param_end
@@ -775,7 +772,7 @@ static int CmdHFCipurseWriteFile(const char *Cmd) {
                       , keyId
                       , sprint_hex(key, CIPURSE_AES_KEY_LENGTH)
                      );
-        PrintAndLogEx(INFO, "data[%d]: %s", hdatalen, sprint_hex(hdata, hdatalen));
+        PrintAndLogEx(INFO, "Data [%d]: %s", hdatalen, sprint_hex(hdata, hdatalen));
     }
 
     if (noAuth == false) {
@@ -1014,7 +1011,7 @@ static int CmdHFCipurseWriteFileAttr(const char *Cmd) {
     SetAPDULogging(APDULogging);
 
     if (verbose) {
-        PrintAndLogEx(INFO, "attribtes data[%d]: %s", hdatalen, sprint_hex(hdata, hdatalen));
+        PrintAndLogEx(INFO, "Attribtes data[%d]: %s", hdatalen, sprint_hex(hdata, hdatalen));
         CIPURSEPrintFileUpdateAttr(hdata, hdatalen);
     }
 
@@ -1185,7 +1182,7 @@ static int CmdHFCipurseCreateDGI(const char *Cmd) {
         arg_str0("k",  "key",     "<hex>", "Auth key"),
 
         arg_str0(NULL, "aid",     "<hex>", "Application ID (AID) ( 1..16 bytes )"),
-        arg_str0(NULL, "fid",     "<hex>", "file ID (FID) ( 2 bytes )"),
+        arg_str0(NULL, "fid",     "<hex>", "File ID (FID) ( 2 bytes )"),
         arg_lit0(NULL, "mfd",     "Select masterfile by empty id"),
 
         arg_str0("d",  "data",    "<hex>", "Data with DGI for create"),
@@ -1260,7 +1257,7 @@ static int CmdHFCipurseCreateDGI(const char *Cmd) {
 
     if (verbose) {
         if (!noauth)
-            PrintAndLogEx(INFO, "key id " _YELLOW_("%d") " key " _YELLOW_("%s")
+            PrintAndLogEx(INFO, "Key id " _YELLOW_("%d") " key " _YELLOW_("%s")
                           , keyId
                           , sprint_hex(key, CIPURSE_AES_KEY_LENGTH)
                          );
@@ -1367,7 +1364,7 @@ static int CmdHFCipurseDeleteFile(const char *Cmd) {
             PrintAndLogEx(INFO, "Child file id " _CYAN_("%x"), childFileId);
 
         if (!noauth)
-            PrintAndLogEx(INFO, "key id " _YELLOW_("%d") " key " _YELLOW_("%s")
+            PrintAndLogEx(INFO, "Key id " _YELLOW_("%d") " key " _YELLOW_("%s")
                           , keyId
                           , sprint_hex(key, CIPURSE_AES_KEY_LENGTH)
                          );
@@ -1463,26 +1460,26 @@ static int CmdHFCipurseUpdateKey(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  NULL,      "<dec>", "key ID for authentication"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Show technical data"),
+        arg_int0("n",  NULL,      "<dec>", "Key ID for authentication"),
         arg_str0("k",  "key",     "<hex>", "Auth key"),
 
-        arg_str0(NULL, "aid",     "<hex 1..16 bytes>", "application ID (AID)"),
-        arg_str0(NULL, "fid",     "<hex 2 bytes>", "file ID (FID)"),
-        arg_lit0(NULL, "mfd",     "select masterfile by empty id"),
+        arg_str0(NULL, "aid",     "<hex 1..16 bytes>", "Application ID (AID)"),
+        arg_str0(NULL, "fid",     "<hex 2 bytes>", "File ID (FID)"),
+        arg_lit0(NULL, "mfd",     "Select masterfile by empty id"),
 
-        arg_int0(NULL, "newkeyn", "<dec>", "target key ID"),
-        arg_str0(NULL, "newkey",  "<hex 16 byte>", "new key"),
-        arg_str0(NULL, "newkeya", "<hex 1 byte>", "new key additional info. 0x00 by default"),
+        arg_int0(NULL, "newkeyn", "<dec>", "Target key ID"),
+        arg_str0(NULL, "newkey",  "<hex 16 byte>", "New key"),
+        arg_str0(NULL, "newkeya", "<hex 1 byte>", "New key additional info (def: 0x00)"),
 
-        arg_int0(NULL, "enckeyn", "<dec>", "encrypt key ID (must be equal to the key on the card)"),
-        arg_str0(NULL, "enckey",  "<hex 16 byte>", "encrypt key (must be equal to the key on the card)"),
+        arg_int0(NULL, "enckeyn", "<dec>", "Encrypt key ID (must be equal to the key on the card)"),
+        arg_str0(NULL, "enckey",  "<hex 16 byte>", "Encrypt key (must be equal to the key on the card)"),
 
-        arg_str0(NULL, "sreq",    "<plain|mac(default)|encode>", "communication reader-PICC security level"),
-        arg_str0(NULL, "sresp",   "<plain|mac(default)|encode>", "communication PICC-reader security level"),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
-        arg_lit0(NULL, "commit",  "commit "),
+        arg_str0(NULL, "sreq",    "<plain|mac(default)|encode>", "Communication reader-PICC security level"),
+        arg_str0(NULL, "sresp",   "<plain|mac(default)|encode>", "Communication PICC-reader security level"),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
+        arg_lit0(NULL, "commit",  "Commit "),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -1605,7 +1602,7 @@ static int CmdHFCipurseUpdateKey(const char *Cmd) {
 
     if (verbose) {
         if (!noauth)
-            PrintAndLogEx(INFO, "key id " _YELLOW_("%d") " key " _YELLOW_("%s")
+            PrintAndLogEx(INFO, "Key id " _YELLOW_("%d") " key " _YELLOW_("%s")
                           , keyId
                           , sprint_hex(key, CIPURSE_AES_KEY_LENGTH)
                          );
@@ -1661,21 +1658,21 @@ static int CmdHFCipurseUpdateKeyAttr(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("a",  "apdu",    "show APDU requests and responses"),
-        arg_lit0("v",  "verbose", "show technical data"),
-        arg_int0("n",  NULL,      "<dec>", "key ID for authentication"),
+        arg_lit0("a",  "apdu",    "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose", "Show technical data"),
+        arg_int0("n",  NULL,      "<dec>", "Key ID for authentication"),
         arg_str0("k",  "key",     "<hex>", "Auth key"),
 
-        arg_str0(NULL, "aid",     "<hex 1..16 bytes>", "application ID (AID)"),
-        arg_str0(NULL, "fid",     "<hex 2 bytes>", "file ID (FID)"),
-        arg_lit0(NULL, "mfd",     "select masterfile by empty id"),
+        arg_str0(NULL, "aid",     "<hex 1..16 bytes>", "Application ID (AID)"),
+        arg_str0(NULL, "fid",     "<hex 2 bytes>", "File ID (FID)"),
+        arg_lit0(NULL, "mfd",     "Select masterfile by empty id"),
 
-        arg_int0(NULL, "trgkeyn", "<dec>", "target key ID"),
-        arg_str0(NULL, "attr",    "<hex 1 byte>", "key attributes 1 byte"),
-        arg_str0(NULL, "sreq",    "<plain|mac(default)|encode>", "communication reader-PICC security level"),
-        arg_str0(NULL, "sresp",   "<plain|mac(default)|encode>", "communication PICC-reader security level"),
-        arg_lit0(NULL, "no-auth", "execute without authentication"),
-        arg_lit0(NULL, "commit",  "commit "),
+        arg_int0(NULL, "trgkeyn", "<dec>", "Target key ID"),
+        arg_str0(NULL, "attr",    "<hex 1 byte>", "Key attributes 1 byte"),
+        arg_str0(NULL, "sreq",    "<plain|mac(default)|encode>", "Communication reader-PICC security level"),
+        arg_str0(NULL, "sresp",   "<plain|mac(default)|encode>", "Communication PICC-reader security level"),
+        arg_lit0(NULL, "no-auth", "Execute without authentication"),
+        arg_lit0(NULL, "commit",  "Commit "),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -1753,7 +1750,7 @@ static int CmdHFCipurseUpdateKeyAttr(const char *Cmd) {
 
     if (verbose) {
         if (!noauth)
-            PrintAndLogEx(INFO, "key id " _YELLOW_("%d") " key " _YELLOW_("%s")
+            PrintAndLogEx(INFO, "Key id " _YELLOW_("%d") " key " _YELLOW_("%s")
                           , keyId
                           , sprint_hex(key, CIPURSE_AES_KEY_LENGTH)
                          );
diff --git a/client/src/mifare/desfirecrypto.c b/client/src/mifare/desfirecrypto.c
index 96a7caa79..48126ae50 100644
--- a/client/src/mifare/desfirecrypto.c
+++ b/client/src/mifare/desfirecrypto.c
@@ -133,14 +133,18 @@ size_t DesfireGetMACLength(DesfireContext_t *ctx) {
 
 size_t DesfireSearchCRCPos(uint8_t *data, size_t datalen, uint8_t respcode, uint8_t crclen) {
     size_t crcpos = datalen - 1;
-    while (crcpos > 0)
-        if (data[crcpos] == 0)
+
+    while (crcpos > 0) {
+        if (data[crcpos] == 0) {
             crcpos--;
-        else
+        } else  {
             break;
+        }
+    }
+
     crcpos++; // crc may be 0x00000000 or 0x0000
     if (crcpos < crclen) {
-        PrintAndLogEx(WARNING, "No space for crc. pos: %zu", crcpos);
+        PrintAndLogEx(WARNING, "No space for crc. pos %zu", crcpos);
         return 0;
     }
 
diff --git a/client/src/mifare/desfiretest.c b/client/src/mifare/desfiretest.c
index b37fd7fd8..75a9ef781 100644
--- a/client/src/mifare/desfiretest.c
+++ b/client/src/mifare/desfiretest.c
@@ -54,9 +54,9 @@ static bool TestCRC16(void) {
     res = res && (len == 0);
 
     if (res)
-        PrintAndLogEx(INFO, "crc16............. " _GREEN_("passed"));
+        PrintAndLogEx(INFO, "CRC16............. " _GREEN_("ok"));
     else
-        PrintAndLogEx(ERR, "crc16............. " _RED_("fail"));
+        PrintAndLogEx(ERR, "CRC16............. " _RED_("fail"));
 
     return res;
 }
@@ -81,9 +81,9 @@ static bool TestCRC32(void) {
     res = res && (len == 0);
 
     if (res)
-        PrintAndLogEx(INFO, "crc32............. " _GREEN_("passed"));
+        PrintAndLogEx(INFO, "CRC32............. " _GREEN_("ok"));
     else
-        PrintAndLogEx(ERR, "crc32............. " _RED_("fail"));
+        PrintAndLogEx(ERR, "CRC32............. " _RED_("fail"));
 
     return res;
 }
@@ -132,7 +132,7 @@ static bool TestCMACSubkeys(void) {
     res = res && (memcmp(sk2, sk2_3tdea, sizeof(sk2_3tdea)) == 0);
 
     if (res)
-        PrintAndLogEx(INFO, "CMAC subkeys...... " _GREEN_("passed"));
+        PrintAndLogEx(INFO, "CMAC subkeys...... " _GREEN_("ok"));
     else
         PrintAndLogEx(ERR,  "CMAC subkeys...... " _RED_("fail"));
 
@@ -156,7 +156,7 @@ static bool TestAn10922KDFAES(void) {
     res = res && (memcmp(dctx.key, dkey, sizeof(dkey)) == 0);
 
     if (res)
-        PrintAndLogEx(INFO, "An10922 AES....... " _GREEN_("passed"));
+        PrintAndLogEx(INFO, "An10922 AES....... " _GREEN_("ok"));
     else
         PrintAndLogEx(ERR,  "An10922 AES....... " _RED_("fail"));
 
@@ -178,7 +178,7 @@ static bool TestAn10922KDF2TDEA(void) {
     res = res && (memcmp(dctx.key, dkey, sizeof(dkey)) == 0);
 
     if (res)
-        PrintAndLogEx(INFO, "An10922 2TDEA..... " _GREEN_("passed"));
+        PrintAndLogEx(INFO, "An10922 2TDEA..... " _GREEN_("ok"));
     else
         PrintAndLogEx(ERR,  "An10922 2TDEA..... " _RED_("fail"));
 
@@ -202,7 +202,7 @@ static bool TestAn10922KDF3TDEA(void) {
     res = res && (memcmp(dctx.key, dkey, sizeof(dkey)) == 0);
 
     if (res)
-        PrintAndLogEx(INFO, "An10922 3TDEA..... " _GREEN_("passed"));
+        PrintAndLogEx(INFO, "An10922 3TDEA..... " _GREEN_("ok"));
     else
         PrintAndLogEx(ERR,  "An10922 3TDEA..... " _RED_("fail"));
 
@@ -246,7 +246,7 @@ static bool TestCMAC3TDEA(void) {
     res = res && (memcmp(cmac, cmac4, sizeof(cmac1)) == 0);
 
     if (res)
-        PrintAndLogEx(INFO, "CMAC 3TDEA........ " _GREEN_("passed"));
+        PrintAndLogEx(INFO, "CMAC 3TDEA........ " _GREEN_("ok"));
     else
         PrintAndLogEx(ERR, "CMAC 3TDEA........ " _RED_("fail"));
 
@@ -290,7 +290,7 @@ static bool TestCMAC2TDEA(void) {
     res = res && (memcmp(cmac, cmac4, sizeof(cmac1)) == 0);
 
     if (res)
-        PrintAndLogEx(INFO, "CMAC 2TDEA........ " _GREEN_("passed"));
+        PrintAndLogEx(INFO, "CMAC 2TDEA........ " _GREEN_("ok"));
     else
         PrintAndLogEx(ERR, "CMAC 2TDEA........ " _RED_("fail"));
 
@@ -330,7 +330,7 @@ static bool TestCMACDES(void) {
     res = res && (memcmp(cmac, cmac4, sizeof(cmac1)) == 0);
 
     if (res)
-        PrintAndLogEx(INFO, "CMAC DES.......... " _GREEN_("passed"));
+        PrintAndLogEx(INFO, "CMAC DES.......... " _GREEN_("ok"));
     else
         PrintAndLogEx(ERR, "CMAC DES.......... " _RED_("fail"));
 
@@ -357,7 +357,7 @@ static bool TestEV2SessionKeys(void) {
     res = res && (memcmp(sessionkey, sessionkeymac, sizeof(sessionkeymac)) == 0);
 
     if (res)
-        PrintAndLogEx(INFO, "EV2 session keys.. " _GREEN_("passed"));
+        PrintAndLogEx(INFO, "EV2 session keys.. " _GREEN_("ok"));
     else
         PrintAndLogEx(ERR,  "EV2 session keys.. " _RED_("fail"));
 
@@ -393,7 +393,7 @@ static bool TestEV2IVEncode(void) {
     res = res && (memcmp(iv, ivres2, sizeof(ivres2)) == 0);
 
     if (res)
-        PrintAndLogEx(INFO, "EV2 IV calc....... " _GREEN_("passed"));
+        PrintAndLogEx(INFO, "EV2 IV calc....... " _GREEN_("ok"));
     else
         PrintAndLogEx(ERR,  "EV2 IV calc....... " _RED_("fail"));
 
@@ -453,7 +453,7 @@ static bool TestEV2MAC(void) {
     res = res && (memcmp(mac, macres4, sizeof(macres4)) == 0);
 
     if (res)
-        PrintAndLogEx(INFO, "EV2 MAC calc...... " _GREEN_("passed"));
+        PrintAndLogEx(INFO, "EV2 MAC calc...... " _GREEN_("ok"));
     else
         PrintAndLogEx(ERR,  "EV2 MAC calc...... " _RED_("fail"));
 
@@ -477,7 +477,7 @@ static bool TestTransSessionKeys(void) {
     res = res && (memcmp(sessionkey, keyenc, sizeof(keyenc)) == 0);
 
     if (res)
-        PrintAndLogEx(INFO, "Trans session key. " _GREEN_("passed"));
+        PrintAndLogEx(INFO, "Trans session key. " _GREEN_("ok"));
     else
         PrintAndLogEx(ERR,  "Trans session key. " _RED_("fail"));
 
@@ -506,7 +506,7 @@ static bool TestLRPPlaintexts(void) {
     res = res && (memcmp(ctx.plaintexts[15], pt15, sizeof(pt15)) == 0);
 
     if (res)
-        PrintAndLogEx(INFO, "LRP plaintexts.... " _GREEN_("passed"));
+        PrintAndLogEx(INFO, "LRP plaintexts.... " _GREEN_("ok"));
     else
         PrintAndLogEx(ERR,  "LRP plaintexts.... " _RED_("fail"));
 
@@ -532,7 +532,7 @@ static bool TestLRPUpdatedKeys(void) {
     res = res && (memcmp(ctx.updatedKeys[2], key2, sizeof(key2)) == 0);
 
     if (res)
-        PrintAndLogEx(INFO, "LRP updated keys.. " _GREEN_("passed"));
+        PrintAndLogEx(INFO, "LRP updated keys.. " _GREEN_("ok"));
     else
         PrintAndLogEx(ERR,  "LRP updated keys.. " _RED_("fail"));
 
@@ -588,7 +588,7 @@ static bool TestLRPEval(void) {
     res = res && (memcmp(y, y5, sizeof(y5)) == 0);
 
     if (res)
-        PrintAndLogEx(INFO, "LRP eval.......... " _GREEN_("passed"));
+        PrintAndLogEx(INFO, "LRP eval.......... " _GREEN_("ok"));
     else
         PrintAndLogEx(ERR,  "LRP eval.......... " _RED_("fail"));
 
@@ -619,7 +619,7 @@ static bool TestLRPIncCounter(void) {
     res = res && (memcmp(ctr4, ctrr4, sizeof(ctrr4)) == 0);
 
     if (res)
-        PrintAndLogEx(INFO, "LRP inc counter... " _GREEN_("passed"));
+        PrintAndLogEx(INFO, "LRP inc counter... " _GREEN_("ok"));
     else
         PrintAndLogEx(ERR,  "LRP inc counter... " _RED_("fail"));
 
@@ -687,7 +687,7 @@ static bool TestLRPEncode(void) {
     res = res && (memcmp(resp, res5, sizeof(res5)) == 0);
 
     if (res)
-        PrintAndLogEx(INFO, "LRP encode........ " _GREEN_("passed"));
+        PrintAndLogEx(INFO, "LRP encode........ " _GREEN_("ok"));
     else
         PrintAndLogEx(ERR,  "LRP encode........ " _RED_("fail"));
 
@@ -754,7 +754,7 @@ static bool TestLRPDecode(void) {
     res = res && (memcmp(resp, res5, sizeof(res5)) == 0);
 
     if (res)
-        PrintAndLogEx(INFO, "LRP decode........ " _GREEN_("passed"));
+        PrintAndLogEx(INFO, "LRP decode........ " _GREEN_("ok"));
     else
         PrintAndLogEx(ERR,  "LRP decode........ " _RED_("fail"));
 
@@ -794,7 +794,7 @@ static bool TestLRPSubkeys(void) {
     res = res && (memcmp(sk2, sk2r3, sizeof(sk2r3)) == 0);
 
     if (res)
-        PrintAndLogEx(INFO, "LRP subkeys....... " _GREEN_("passed"));
+        PrintAndLogEx(INFO, "LRP subkeys....... " _GREEN_("ok"));
     else
         PrintAndLogEx(ERR,  "LRP subkeys....... " _RED_("fail"));
 
@@ -856,7 +856,7 @@ static bool TestLRPCMAC(void) {
     res = res && (memcmp(cmac, cmacres6, sizeof(cmacres6)) == 0);
 
     if (res)
-        PrintAndLogEx(INFO, "LRP CMAC.......... " _GREEN_("passed"));
+        PrintAndLogEx(INFO, "LRP CMAC.......... " _GREEN_("ok"));
     else
         PrintAndLogEx(ERR,  "LRP CMAC.......... " _RED_("fail"));
 
@@ -878,7 +878,7 @@ static bool TestLRPSessionKeys(void) {
     res = res && (memcmp(sessionkey, sessionkeyres, sizeof(sessionkeyres)) == 0);
 
     if (res)
-        PrintAndLogEx(INFO, "LRP session keys.. " _GREEN_("passed"));
+        PrintAndLogEx(INFO, "LRP session keys.. " _GREEN_("ok"));
     else
         PrintAndLogEx(ERR,  "LRP session keys.. " _RED_("fail"));
 
@@ -888,7 +888,7 @@ static bool TestLRPSessionKeys(void) {
 bool DesfireTest(bool verbose) {
     bool res = true;
 
-    PrintAndLogEx(INFO, "------ " _CYAN_("Desfire Tests") " ------");
+    PrintAndLogEx(INFO, "------ " _CYAN_("MIFARE DESFire tests") " ------");
 
     res = res && TestCRC16();
     res = res && TestCRC32();
