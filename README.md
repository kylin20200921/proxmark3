commit 2bf2bb301a4554b142538c9691073a6effa9144e
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Jan 7 19:23:42 2022 +0100

    cppcheck fixes

diff --git a/client/src/mifare/desfirecrypto.c b/client/src/mifare/desfirecrypto.c
index f022bbe65..8ee68c29e 100644
--- a/client/src/mifare/desfirecrypto.c
+++ b/client/src/mifare/desfirecrypto.c
@@ -439,7 +439,7 @@ void DesfireDESKeySetVersion(uint8_t *key, DesfireCryptoAlgorithm keytype, uint8
     }
 }
 
-uint8_t DesfireDESKeyGetVersion(uint8_t *key) {
+uint8_t DesfireDESKeyGetVersion(const uint8_t *key) {
     uint8_t version = 0;
     for (int n = 0; n < 8; n++)
         version |= ((key[n] & 1) << (7 - n));
diff --git a/client/src/mifare/desfirecrypto.h b/client/src/mifare/desfirecrypto.h
index 96f508fa6..7147eb981 100644
--- a/client/src/mifare/desfirecrypto.h
+++ b/client/src/mifare/desfirecrypto.h
@@ -114,7 +114,7 @@ void MifareKdfAn10922(DesfireContext_t *ctx, DesfireCryptoOpKeyType key_type, co
 void DesfireGenSessionKeyLRP(uint8_t *key, uint8_t *rndA, uint8_t *rndB, bool enckey, uint8_t *sessionkey);
 
 void DesfireDESKeySetVersion(uint8_t *key, DesfireCryptoAlgorithm keytype, uint8_t version);
-uint8_t DesfireDESKeyGetVersion(uint8_t *key);
+uint8_t DesfireDESKeyGetVersion(const uint8_t *key);
 
 DesfireCryptoAlgorithm DesfireKeyTypeToAlgo(uint8_t keyType);
 uint8_t DesfireKeyAlgoToType(DesfireCryptoAlgorithm keyType);
diff --git a/client/src/mifare/gallaghercore.c b/client/src/mifare/gallaghercore.c
index 71e235286..e9ae26364 100644
--- a/client/src/mifare/gallaghercore.c
+++ b/client/src/mifare/gallaghercore.c
@@ -13,7 +13,7 @@
 #include "ui.h"
 
 static void scramble(uint8_t *arr, uint8_t len) {
-    uint8_t lut[] = {
+    const uint8_t lut[] = {
         0xa3, 0xb0, 0x80, 0xc6, 0xb2, 0xf4, 0x5c, 0x6c, 0x81, 0xf1, 0xbb, 0xeb, 0x55, 0x67, 0x3c, 0x05,
         0x1a, 0x0e, 0x61, 0xf6, 0x22, 0xce, 0xaa, 0x8f, 0xbd, 0x3b, 0x1f, 0x5e, 0x44, 0x04, 0x51, 0x2e,
         0x4d, 0x9a, 0x84, 0xea, 0xf8, 0x66, 0x74, 0x29, 0x7f, 0x70, 0xd8, 0x31, 0x7a, 0x6d, 0xa4, 0x00,
@@ -38,7 +38,7 @@ static void scramble(uint8_t *arr, uint8_t len) {
 }
 
 static void descramble(uint8_t *arr, uint8_t len) {
-    uint8_t lut[] = {
+    const uint8_t lut[] = {
         0x2f, 0x6e, 0xdd, 0xdf, 0x1d, 0x0f, 0xb0, 0x76, 0xad, 0xaf, 0x7f, 0xbb, 0x77, 0x85, 0x11, 0x6d,
         0xf4, 0xd2, 0x84, 0x42, 0xeb, 0xf7, 0x34, 0x55, 0x4a, 0x3a, 0x10, 0x71, 0xe7, 0xa1, 0x62, 0x1a,
         0x3e, 0x4c, 0x14, 0xd3, 0x5e, 0xb2, 0x7d, 0x56, 0xbc, 0x27, 0x82, 0x60, 0xe3, 0xae, 0x1f, 0x9b,
diff --git a/client/src/mifare/lrpcrypto.c b/client/src/mifare/lrpcrypto.c
index bd0368b9d..8016293f8 100644
--- a/client/src/mifare/lrpcrypto.c
+++ b/client/src/mifare/lrpcrypto.c
@@ -106,7 +106,7 @@ void LRPGenerateUpdatedKeys(LRPContext_t *ctx, size_t updatedKeysCount) {
 
 // https://www.nxp.com/docs/en/application-note/AN12304.pdf
 // Algorithm 3
-void LRPEvalLRP(LRPContext_t *ctx, uint8_t *iv, size_t ivlen, bool final, uint8_t *y) {
+void LRPEvalLRP(LRPContext_t *ctx, const uint8_t *iv, size_t ivlen, bool final, uint8_t *y) {
     uint8_t ry[CRYPTO_AES128_KEY_SIZE] = {0};
     memcpy(ry, ctx->updatedKeys[ctx->useUpdatedKeyNum], CRYPTO_AES128_KEY_SIZE);
 
diff --git a/client/src/mifare/lrpcrypto.h b/client/src/mifare/lrpcrypto.h
index aa7f45704..1b6622ed9 100644
--- a/client/src/mifare/lrpcrypto.h
+++ b/client/src/mifare/lrpcrypto.h
@@ -50,7 +50,7 @@ void LRPSetKeyEx(LRPContext_t *ctx, uint8_t *key, uint8_t *counter, size_t count
 void LRPSetCounter(LRPContext_t *ctx, uint8_t *counter, size_t counterLenNibbles);
 void LRPGeneratePlaintexts(LRPContext_t *ctx, size_t plaintextsCount);
 void LRPGenerateUpdatedKeys(LRPContext_t *ctx, size_t updatedKeysCount);
-void LRPEvalLRP(LRPContext_t *ctx, uint8_t *iv, size_t ivlen, bool final, uint8_t *y);
+void LRPEvalLRP(LRPContext_t *ctx, const uint8_t *iv, size_t ivlen, bool final, uint8_t *y);
 void LRPIncCounter(uint8_t *ctr, size_t ctrlen);
 void LRPEncode(LRPContext_t *ctx, uint8_t *data, size_t datalen, uint8_t *resp, size_t *resplen);
 void LRPDecode(LRPContext_t *ctx, uint8_t *data, size_t datalen, uint8_t *resp, size_t *resplen);
diff --git a/client/src/mifare/mad.c b/client/src/mifare/mad.c
index 55868cfae..ca5be1e01 100644
--- a/client/src/mifare/mad.c
+++ b/client/src/mifare/mad.c
@@ -159,7 +159,7 @@ static int madCRCCheck(uint8_t *sector, bool verbose, int MADver) {
     return PM3_SUCCESS;
 }
 
-static uint16_t madGetAID(uint8_t *sector, bool swapmad, int MADver, int sectorNo) {
+static uint16_t madGetAID(const uint8_t *sector, bool swapmad, int MADver, int sectorNo) {
     uint16_t mad;
     if (MADver == 1)
         mad = (sector[16 + 2 + (sectorNo - 1) * 2 + 1] << 8) + (sector[16 + 2 + (sectorNo - 1) * 2]);
@@ -267,7 +267,7 @@ int MADCardHolderInfoDecode(uint8_t *data, size_t datalen, bool verbose) {
     return PM3_SUCCESS;
 }
 
-static int MADInfoByteDecode(uint8_t *sector, bool swapmad, int mad_ver, bool verbose) {
+static int MADInfoByteDecode(const uint8_t *sector, bool swapmad, int mad_ver, bool verbose) {
     uint8_t info;
     if (mad_ver == 1) {
         info = sector[16 + 1] & 0x3f;
diff --git a/client/src/mifare/mifare4.c b/client/src/mifare/mifare4.c
index 42dff092c..8b262ec40 100644
--- a/client/src/mifare/mifare4.c
+++ b/client/src/mifare/mifare4.c
@@ -66,7 +66,7 @@ AccessConditions_t MFAccessConditionsTrailer[] = {
     {0x07, "read ACCESS by AB", ""}
 };
 
-bool mfValidateAccessConditions(uint8_t *data) {
+bool mfValidateAccessConditions(const uint8_t *data) {
     uint8_t ndata1 = (data[0]) & 0x0f;
     uint8_t ndata2 = (data[0] >> 4) & 0x0f;
     uint8_t ndata3 = (data[1]) & 0x0f;
@@ -77,7 +77,7 @@ bool mfValidateAccessConditions(uint8_t *data) {
     return ((ndata1 == (data1 ^ 0xF)) && (ndata2 == (data2 ^ 0xF)) && (ndata3 == (data3 ^ 0xF)));
 }
 
-const char *mfGetAccessConditionsDesc(uint8_t blockn, uint8_t *data) {
+const char *mfGetAccessConditionsDesc(uint8_t blockn, const uint8_t *data) {
     uint8_t data1 = ((data[1] >> 4) & 0x0f) >> blockn;
     uint8_t data2 = ((data[2]) & 0x0f) >> blockn;
     uint8_t data3 = ((data[2] >> 4) & 0x0f) >> blockn;
diff --git a/client/src/mifare/mifare4.h b/client/src/mifare/mifare4.h
index 132cf6ee5..0cc716d58 100644
--- a/client/src/mifare/mifare4.h
+++ b/client/src/mifare/mifare4.h
@@ -63,8 +63,8 @@ int mfpReadSector(uint8_t sectorNo, uint8_t keyType, uint8_t *key, uint8_t *data
 int MFPGetSignature(bool activateField, bool leaveSignalON, uint8_t *dataout, int maxdataoutlen, int *dataoutlen);
 int MFPGetVersion(bool activateField, bool leaveSignalON, uint8_t *dataout, int maxdataoutlen, int *dataoutlen);
 
-bool mfValidateAccessConditions(uint8_t *data);
-const char *mfGetAccessConditionsDesc(uint8_t blockn, uint8_t *data);
+bool mfValidateAccessConditions(const uint8_t *data);
+const char *mfGetAccessConditionsDesc(uint8_t blockn, const uint8_t *data);
 
 uint8_t mfNumBlocksPerSector(uint8_t sectorNo);
 uint8_t mfFirstBlockOfSector(uint8_t sectorNo);
diff --git a/client/src/mifare/mifarehost.c b/client/src/mifare/mifarehost.c
index 4b5e4e5a1..7fa5ed18f 100644
--- a/client/src/mifare/mifarehost.c
+++ b/client/src/mifare/mifarehost.c
@@ -327,7 +327,7 @@ int mfCheckKeys_file(uint8_t *destfn, uint64_t *key) {
 
 // PM3 imp of J-Run mf_key_brute (part 2)
 // ref: https://github.com/J-Run/mf_key_brute
-int mfKeyBrute(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint64_t *resultkey) {
+int mfKeyBrute(uint8_t blockNo, uint8_t keyType, const uint8_t *key, uint64_t *resultkey) {
 
     uint64_t key64;
     uint8_t found = false;
@@ -655,7 +655,6 @@ int mfStaticNested(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_t trgBl
     PrintAndLogEx(SUCCESS, "Found " _YELLOW_("%u") " key candidates", keycnt);
 
     memset(resultKey, 0, 6);
-    uint64_t key64 = -1;
 
     // The list may still contain several key candidates. Test each of them with mfCheckKeys
     uint32_t maxkeysinblock = IfPm3Flash() ? 1000 : KEYS_IN_BLOCK;
@@ -706,7 +705,8 @@ int mfStaticNested(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_t trgBl
         }
 
         int res = 0;
-        key64 = 0;
+        uint64_t key64 = 0;
+
         uint32_t chunk = keycnt - i > max_keys_chunk ? max_keys_chunk : keycnt - i;
 
         // copy x keys to device.
diff --git a/client/src/mifare/mifarehost.h b/client/src/mifare/mifarehost.h
index 56cc5d841..0d26b5da0 100644
--- a/client/src/mifare/mifarehost.h
+++ b/client/src/mifare/mifarehost.h
@@ -67,7 +67,7 @@ int mfCheckKeys_fast(uint8_t sectorsCnt, uint8_t firstChunk, uint8_t lastChunk,
 
 int mfCheckKeys_file(uint8_t *destfn, uint64_t *key);
 
-int mfKeyBrute(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint64_t *resultkey);
+int mfKeyBrute(uint8_t blockNo, uint8_t keyType, const uint8_t *key, uint64_t *resultkey);
 
 int mfReadSector(uint8_t sectorNo, uint8_t keyType, uint8_t *key, uint8_t *data);
 int mfReadBlock(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_t *data);
