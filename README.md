commit 6dbde61c4e625b3cd1cb9164ef8b3649f97e2b3d
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Jan 6 21:08:22 2022 +0100

    cppcheck fixes

diff --git a/client/src/cmdhfemrtd.c b/client/src/cmdhfemrtd.c
index 2afefff1d..493720853 100644
--- a/client/src/cmdhfemrtd.c
+++ b/client/src/cmdhfemrtd.c
@@ -1118,7 +1118,7 @@ static void emrtd_print_legal_sex(char *legal_sex) {
     PrintAndLogEx(SUCCESS, "Legal Sex Marker......: " _YELLOW_("%s"), sex);
 }
 
-static int emrtd_mrz_determine_length(char *mrz, int offset, int max_length) {
+static int emrtd_mrz_determine_length(const char *mrz, int offset, int max_length) {
     int i;
     for (i = max_length; i >= 1; i--) {
         if (mrz[offset + i - 1] != '<') {
@@ -1129,7 +1129,7 @@ static int emrtd_mrz_determine_length(char *mrz, int offset, int max_length) {
     return 0;
 }
 
-static int emrtd_mrz_determine_separator(char *mrz, int offset, int max_length) {
+static int emrtd_mrz_determine_separator(const char *mrz, int offset, int max_length) {
     // Note: this function does not account for len=0
     int i;
     for (i = max_length - 1; i > 0; i--) {
diff --git a/client/src/cmdhffelica.c b/client/src/cmdhffelica.c
index d6a03f0ef..7a9c5acac 100644
--- a/client/src/cmdhffelica.c
+++ b/client/src/cmdhffelica.c
@@ -560,7 +560,7 @@ static int send_wr_plain(uint8_t flags, uint16_t datalen, uint8_t *data, bool ve
  * @param length in bytes of the master secret.
  * @param reverse_master_key output in which the reversed secret is stored.
  */
-static void reverse_3des_key(uint8_t *master_key, int length, uint8_t *reverse_master_key) {
+static void reverse_3des_key(const uint8_t *master_key, int length, uint8_t *reverse_master_key) {
     for (int i = 0; i < length; i++) {
         reverse_master_key[i] = master_key[(length - 1) - i];
     }
diff --git a/client/src/cmdhfgallagher.c b/client/src/cmdhfgallagher.c
index 801699480..e95e25bcc 100644
--- a/client/src/cmdhfgallagher.c
+++ b/client/src/cmdhfgallagher.c
@@ -82,7 +82,7 @@ static void reverse_aid(uint8_t *aid) {
  * @brief Converts a Card Application Directory format application ID to an integer.
  * Note: the CAD stores AIDs in reverse order, so this is different to DesfireAIDByteToUint().
  */
-static uint32_t cad_aid_byte_to_uint(uint8_t *data) {
+static uint32_t cad_aid_byte_to_uint(const uint8_t *data) {
     return data[2] + (data[1] << 8) + (data[0] << 16);
 }
 
@@ -100,7 +100,7 @@ static void cad_aid_uint_to_byte(uint32_t aid, uint8_t *data) {
  * @brief Returns true if the Card Application Directory entry
  * is for the specified region & facility, false otherwise.
  */
-static bool cad_facility_match(uint8_t *entry, uint8_t region_code, uint16_t facility_code) {
+static bool cad_facility_match(const uint8_t *entry, uint8_t region_code, uint16_t facility_code) {
     return entry[0] == region_code && (entry[1] << 8) + entry[2] == facility_code;
 }
 
diff --git a/client/src/cmdhfgallagher.h b/client/src/cmdhfgallagher.h
index 22aee9c2f..c4ed31784 100644
--- a/client/src/cmdhfgallagher.h
+++ b/client/src/cmdhfgallagher.h
@@ -15,7 +15,7 @@
 #include "common.h"
 #include <stdint.h>
 
-int CmdHFGallagher(const char *Cmd);
+int CmdHFGallagher(const char *cmd);
 
 /**
  * @brief Create Gallagher Application Master Key by diversifying
@@ -29,7 +29,8 @@ int CmdHFGallagher(const char *Cmd);
  * @param keyOut Buffer to copy the diversified key into (must be 16 bytes).
  * @return PM3_SUCCESS if successful, PM3_EINVARG if an argument is invalid.
  */
-int hfgal_diversify_key(uint8_t *sitekey, uint8_t *uid, uint8_t uidLen, uint8_t keyNum, uint32_t aid, uint8_t *keyOut);
+int hfgal_diversify_key(uint8_t *site_key, uint8_t *uid, uint8_t uid_len,
+                        uint8_t key_num, uint32_t aid, uint8_t *key_output);
 
 // Return error
 #define HFGAL_RET_ERR(err, ...)  { PrintAndLogEx(ERR, __VA_ARGS__); return err; }
diff --git a/client/src/cmdhficlass.c b/client/src/cmdhficlass.c
index 7810f38b0..3880361dc 100644
--- a/client/src/cmdhficlass.c
+++ b/client/src/cmdhficlass.c
@@ -3603,7 +3603,7 @@ static void permute_rev(uint8_t *data, uint8_t len, uint8_t *output) {
     permute(output, len, data);
     permute(data, len, output);
 }
-static void simple_crc(uint8_t *data, uint8_t len, uint8_t *output) {
+static void simple_crc(const uint8_t *data, uint8_t len, uint8_t *output) {
     uint8_t crc = 0;
     for (uint8_t i = 0; i < len; ++i) {
         // seventh byte contains the crc.
diff --git a/client/src/cmdhflist.c b/client/src/cmdhflist.c
index 362a665bc..ea22f4f31 100644
--- a/client/src/cmdhflist.c
+++ b/client/src/cmdhflist.c
@@ -1403,7 +1403,8 @@ void annotateLTO(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize) {
     }
 }
 
-void annotateMifare(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize, uint8_t *parity, uint8_t paritysize, bool isResponse) {
+void annotateMifare(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize,
+                    const uint8_t *parity, uint8_t paritysize, bool isResponse) {
     if (!isResponse && cmdsize == 1) {
         switch (cmd[0]) {
             case ISO14443A_CMD_WUPA:
@@ -1716,7 +1717,7 @@ bool NestedCheckKey(uint64_t key, AuthData_t *ad, uint8_t *cmd, uint8_t cmdsize,
     return true;
 }
 
-bool CheckCrypto1Parity(uint8_t *cmd_enc, uint8_t cmdsize, uint8_t *cmd, uint8_t *parity_enc) {
+bool CheckCrypto1Parity(const uint8_t *cmd_enc, uint8_t cmdsize, uint8_t *cmd, const uint8_t *parity_enc) {
     for (int i = 0; i < cmdsize - 1; i++) {
         if (oddparity8(cmd[i]) ^ (cmd[i + 1] & 0x01) ^ ((parity_enc[i / 8] >> (7 - i % 8)) & 0x01) ^ (cmd_enc[i + 1] & 0x01))
             return false;
diff --git a/client/src/cmdhflist.h b/client/src/cmdhflist.h
index 081b3b20f..06a6d3abe 100644
--- a/client/src/cmdhflist.h
+++ b/client/src/cmdhflist.h
@@ -48,14 +48,15 @@ void annotateIso7816(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize);
 void annotateIso14443b(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize);
 void annotateIso14443a(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize, bool is_response);
 void annotateMfDesfire(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize);
-void annotateMifare(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize, uint8_t *parity, uint8_t paritysize, bool isResponse);
+void annotateMifare(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize,
+                    const uint8_t *parity, uint8_t paritysize, bool isResponse);
 void annotateLTO(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize);
 void annotateCryptoRF(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize);
 
 bool DecodeMifareData(uint8_t *cmd, uint8_t cmdsize, uint8_t *parity, bool isResponse, uint8_t *mfData, size_t *mfDataLen, const uint64_t *dicKeys, uint32_t dicKeysCount);
 bool NTParityChk(AuthData_t *ad, uint32_t ntx);
 bool NestedCheckKey(uint64_t key, AuthData_t *ad, uint8_t *cmd, uint8_t cmdsize, uint8_t *parity);
-bool CheckCrypto1Parity(uint8_t *cmd_enc, uint8_t cmdsize, uint8_t *cmd, uint8_t *parity_enc);
+bool CheckCrypto1Parity(const uint8_t *cmd_enc, uint8_t cmdsize, uint8_t *cmd, const uint8_t *parity_enc);
 uint64_t GetCrypto1ProbableKey(AuthData_t *ad);
 
 #endif // CMDHFLIST
