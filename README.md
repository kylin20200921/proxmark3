commit 8bf5a99bca04a349bc5f37bf6b6b0b3c04b7b453
Author: iceman1001 <iceman@iuse.se>
Date:   Wed May 19 18:07:19 2021 +0200

    codestyle prefer calloc

diff --git a/client/src/cmdhfmfdes.c b/client/src/cmdhfmfdes.c
index 276891e45..1fbe37a7a 100644
--- a/client/src/cmdhfmfdes.c
+++ b/client/src/cmdhfmfdes.c
@@ -1293,7 +1293,6 @@ static int mifare_desfire_change_key(uint8_t key_no, uint8_t *new_key, uint8_t n
     memcpy(&data[1], p, cmdcnt);
     apdu.data = data;
 
-
     uint32_t recv_len = 0;
     uint16_t sw = 0;
 
@@ -1303,7 +1302,7 @@ static int mifare_desfire_change_key(uint8_t key_no, uint8_t *new_key, uint8_t n
     int res = send_desfire_cmd(&apdu, false, NULL, &recv_len, &sw, 0, true);
 
     if (res != PM3_SUCCESS) {
-        PrintAndLogEx(WARNING, _RED_("   Can't change key -> %s"), GetErrorString(res, &sw));
+        PrintAndLogEx(WARNING, _RED_("can't change key -> %s"), GetErrorString(res, &sw));
         DropFieldDesfire();
         return res;
     }
@@ -2044,7 +2043,7 @@ static int handler_desfire_deletefile(uint8_t file_no) {
     return res;
 }
 
-static int handler_desfire_clearrecordfile(uint8_t file_no) {
+static int handler_desfire_clear_record_file(uint8_t file_no) {
     if (file_no > 0x1F)
         return PM3_EINVARG;
 
@@ -2664,7 +2663,7 @@ static int CmdHF14ADesClearRecordFile(const char *Cmd) {
         return PM3_ESOFT;
     }
 
-    int res = handler_desfire_clearrecordfile(fno);
+    int res = handler_desfire_clear_record_file(fno);
     if (res == PM3_SUCCESS) {
         PrintAndLogEx(SUCCESS, "Successfully cleared record file.");
     } else {
@@ -4088,18 +4087,9 @@ static int CmdHF14ADesBruteApps(const char *Cmd) {
 }
 
 static int CmdHF14ADesChangeKey(const char *Cmd) {
-    //DropFieldDesfire();
-    // NR  DESC     KEYLENGHT
-    // ------------------------
-    // 1 = DES      8
-    // 2 = 3DES     16
-    // 3 = 3K 3DES  24
-    // 4 = AES      16
-    uint8_t keylength = 8;
-    uint8_t newkeylength = 8;
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfdes changekey",
-                  "Changes MIFARE DESFire Key\n"
+                  "Change MIFARE DESFire Key.\n"
                   "Make sure to select aid or authenticate aid before running this command.",
                   "hf mfdes changekey -n 0 -t 1 -k 0000000000000000 -u 1 -j 0102030405060708  -> DES, keynumber 0"
                  );
@@ -4130,6 +4120,14 @@ static int CmdHF14ADesChangeKey(const char *Cmd) {
     uint8_t aesversion = arg_get_int_def(ctx, 6, 0);
     CLIParserFree(ctx);
 
+    //DropFieldDesfire();
+    // NR  DESC     KEYLENGHT
+    // ------------------------
+    // 1 = DES      8
+    // 2 = 3DES     16
+    // 3 = 3K 3DES  24
+    // 4 = AES      16
+    uint8_t keylength = 8;
     if (cmdAuthAlgo == MFDES_ALGO_AES) {
         keylength = 16;
     } else if (cmdAuthAlgo == MFDES_ALGO_3DES) {
@@ -4140,6 +4138,7 @@ static int CmdHF14ADesChangeKey(const char *Cmd) {
         keylength = 24;
     }
 
+    uint8_t newkeylength = 8;
     if (newcmdAuthAlgo == MFDES_ALGO_AES) {
         newkeylength = 16;
     } else if (newcmdAuthAlgo == MFDES_ALGO_3DES) {
@@ -4151,37 +4150,36 @@ static int CmdHF14ADesChangeKey(const char *Cmd) {
     }
 
     if (res_klen || (keylen < 8) || (keylen > 24)) {
-        PrintAndLogEx(ERR, "Specified key must have %d bytes length.", keylen);
+        PrintAndLogEx(ERR, "Specified key must have %d bytes length", keylen);
         return PM3_EINVARG;
     }
 
     if (res_newklen || (newkeylen < 8) || (newkeylen > 24)) {
-        PrintAndLogEx(ERR, "Specified key must have %d bytes length.", newkeylen);
+        PrintAndLogEx(ERR, "Specified new key must have %d bytes length", newkeylen);
         return PM3_EINVARG;
     }
 
     if (keylen != keylength) {
-        PrintAndLogEx(WARNING, "Key must include %d HEX symbols", keylength);
+        PrintAndLogEx(WARNING, "Key must include %d hex symbols, got %d", keylength, keylen);
         return PM3_EINVARG;
     }
 
     if (newkeylen != newkeylength) {
-        PrintAndLogEx(WARNING, "New key must include %d HEX symbols", keylength);
+        PrintAndLogEx(WARNING, "New key must include %d hex symbols, got %d", keylength, newkeylen);
         return PM3_EINVARG;
     }
 
-    PrintAndLogEx(INFO, "changing key number 0x%02x", cmdKeyNo);
-    PrintAndLogEx(INFO, "old key: %s (%s)", sprint_hex_inrow(key, keylen), getEncryptionAlgoStr(cmdAuthAlgo));
-    PrintAndLogEx(INFO, "new key: %s (%s)", sprint_hex_inrow(newkey, newkeylen), getEncryptionAlgoStr(newcmdAuthAlgo));
+    PrintAndLogEx(INFO, "changing key number  " _YELLOW_("0x%02x"), cmdKeyNo);
+    PrintAndLogEx(INFO, "old key: %s ( %s )", sprint_hex_inrow(key, keylen), getEncryptionAlgoStr(cmdAuthAlgo));
+    PrintAndLogEx(INFO, "new key: %s ( %s )", sprint_hex_inrow(newkey, newkeylen), getEncryptionAlgoStr(newcmdAuthAlgo));
 
-    int error = mifare_desfire_change_key(cmdKeyNo, newkey, newcmdAuthAlgo, key, cmdAuthAlgo, aesversion);
-    if (error == PM3_SUCCESS) {
-        PrintAndLogEx(SUCCESS, "  Successfully changed key.");
+    int res = mifare_desfire_change_key(cmdKeyNo, newkey, newcmdAuthAlgo, key, cmdAuthAlgo, aesversion);
+    if (res == PM3_SUCCESS) {
+        PrintAndLogEx(SUCCESS, "Change key ( " _GREEN_("ok") " )");
     } else {
-        PrintAndLogEx(SUCCESS, "  Error on changing key.");
-        return PM3_ESOFT;
+        PrintAndLogEx(FAILED, "Change key ( " _RED_("fail") " )");
     }
-    return PM3_SUCCESS;
+    return res;
 }
 
 
diff --git a/client/src/mifare/desfire_crypto.c b/client/src/mifare/desfire_crypto.c
index d13563344..1cf043a7b 100644
--- a/client/src/mifare/desfire_crypto.c
+++ b/client/src/mifare/desfire_crypto.c
@@ -285,7 +285,7 @@ void cmac(const desfirekey_t key, uint8_t *ivect, const uint8_t *data, size_t le
         return;
     }
 
-    uint8_t *buffer = malloc(padded_data_length(len, kbs));
+    uint8_t *buffer = calloc(padded_data_length(len, kbs), sizeof(uint8_t));
 
     memcpy(buffer, data, len);
 
@@ -315,8 +315,8 @@ void mifare_kdf_an10922(const desfirekey_t key, const uint8_t *data, size_t len)
 
     cmac_generate_subkeys(key, MCD_SEND);
 
-    uint8_t *buffer = malloc(kbs2);
-    uint8_t *ivect = malloc(kbs);
+    uint8_t *buffer = calloc(kbs2, sizeof(uint8_t));
+    uint8_t *ivect = calloc(kbs, sizeof(uint8_t));
 
     memset(ivect, 0, kbs);
 
@@ -556,7 +556,7 @@ void *mifare_cryto_postprocess_data(desfiretag_t tag, void *data, size_t *nbytes
     void *res = data;
     void *edata = NULL;
     tag->crypto_buffer_size = *nbytes * 2;
-    tag->crypto_buffer = (uint8_t *)malloc(tag->crypto_buffer_size);
+    tag->crypto_buffer = (uint8_t *)calloc(tag->crypto_buffer_size, sizeof(uint8_t));
 
     uint8_t first_cmac_byte = 0x00;
 
@@ -592,7 +592,7 @@ void *mifare_cryto_postprocess_data(desfiretag_t tag, void *data, size_t *nbytes
                         }
 
                         size_t edl = enciphered_data_length(tag, *nbytes, communication_settings);
-                        edata = malloc(edl);
+                        edata = calloc(edl, sizeof(uint8_t));
 
                         memcpy(edata, data, *nbytes);
                         memset((uint8_t *)edata + *nbytes, 0, edl - *nbytes);
