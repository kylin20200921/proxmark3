commit 89a05b47a80d261597d7320d0b292a0989243cd5
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Jan 7 19:31:19 2022 +0100

    cppcheck fixes const

diff --git a/client/src/mifare/mifarehost.c b/client/src/mifare/mifarehost.c
index 7fa5ed18f..31eee3ef0 100644
--- a/client/src/mifare/mifarehost.c
+++ b/client/src/mifare/mifarehost.c
@@ -880,7 +880,7 @@ int mfEmlSetMem_xt(uint8_t *data, int blockNum, int blocksCount, int blockBtWidt
 }
 
 // "MAGIC" CARD
-int mfCSetUID(uint8_t *uid, uint8_t uidlen, uint8_t *atqa, uint8_t *sak, uint8_t *old_uid, uint8_t *verifed_uid, uint8_t wipecard) {
+int mfCSetUID(uint8_t *uid, uint8_t uidlen, const uint8_t *atqa, const uint8_t *sak, uint8_t *old_uid, uint8_t *verifed_uid, uint8_t wipecard) {
 
     uint8_t params = MAGIC_SINGLE;
     uint8_t block0[16];
@@ -945,7 +945,7 @@ int mfCSetUID(uint8_t *uid, uint8_t uidlen, uint8_t *atqa, uint8_t *sak, uint8_t
     return res;
 }
 
-int mfCWipe(uint8_t *uid, uint8_t *atqa, uint8_t *sak) {
+int mfCWipe(uint8_t *uid, const uint8_t *atqa, const uint8_t *sak) {
     uint8_t block0[16] = {0x01, 0x02, 0x03, 0x04, 0x04, 0x08, 0x04, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0xBE, 0xAF};
     uint8_t blockD[16] = {0x00};
     uint8_t blockK[16] = {0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0x08, 0x77, 0x8F, 0x00, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF};
diff --git a/client/src/mifare/mifarehost.h b/client/src/mifare/mifarehost.h
index 0d26b5da0..c12113c45 100644
--- a/client/src/mifare/mifarehost.h
+++ b/client/src/mifare/mifarehost.h
@@ -76,8 +76,8 @@ int mfEmlGetMem(uint8_t *data, int blockNum, int blocksCount);
 int mfEmlSetMem(uint8_t *data, int blockNum, int blocksCount);
 int mfEmlSetMem_xt(uint8_t *data, int blockNum, int blocksCount, int blockBtWidth);
 
-int mfCSetUID(uint8_t *uid, uint8_t uidlen, uint8_t *atqa, uint8_t *sak, uint8_t *old_uid, uint8_t *verifed_uid, uint8_t wipecard);
-int mfCWipe(uint8_t *uid, uint8_t *atqa, uint8_t *sak);
+int mfCSetUID(uint8_t *uid, uint8_t uidlen, const uint8_t *atqa, const uint8_t *sak, uint8_t *old_uid, uint8_t *verifed_uid, uint8_t wipecard);
+int mfCWipe(uint8_t *uid, const uint8_t *atqa, const uint8_t *sak);
 int mfCSetBlock(uint8_t blockNo, uint8_t *data, uint8_t *uid, uint8_t params);
 int mfCGetBlock(uint8_t blockNo, uint8_t *data, uint8_t params);
 
diff --git a/client/src/nfc/ndef.c b/client/src/nfc/ndef.c
index 7e07639ad..c0d0e0c4d 100644
--- a/client/src/nfc/ndef.c
+++ b/client/src/nfc/ndef.c
@@ -105,7 +105,7 @@ static const char *URI_s[] = {
 static int ndefRecordDecodeAndPrint(uint8_t *ndefRecord, size_t ndefRecordLen);
 static int ndefDecodePayload(NDEFHeader_t *ndef);
 
-static uint16_t ndefTLVGetLength(uint8_t *data, size_t *indx) {
+static uint16_t ndefTLVGetLength(const uint8_t *data, size_t *indx) {
     uint16_t len = 0;
     if (data[0] == 0xff) {
         len = (data[1] << 8) + data[2];
diff --git a/client/src/util.c b/client/src/util.c
index 7fa177911..867c1fc15 100644
--- a/client/src/util.c
+++ b/client/src/util.c
@@ -843,7 +843,7 @@ int hextobinstring(char *target, char *source) {
 
 // convert binary array of 0x00/0x01 values to hex
 // return number of bits converted
-int binarraytohex(char *target, const size_t targetlen, char *source, size_t srclen) {
+int binarraytohex(char *target, const size_t targetlen, const char *source, size_t srclen) {
     uint8_t i = 0, x = 0;
     uint32_t t = 0; // written target chars
     uint32_t r = 0; // consumed bits
@@ -914,7 +914,7 @@ int binstring2binarray(uint8_t *target, char *source, int length) {
 }
 
 // return parity bit required to match type
-uint8_t GetParity(uint8_t *bits, uint8_t type, int length) {
+uint8_t GetParity(const uint8_t *bits, uint8_t type, int length) {
     int x;
     for (x = 0 ; length > 0 ; --length)
         x += bits[length - 1];
@@ -939,7 +939,7 @@ void wiegand_add_parity_swapped(uint8_t *target, uint8_t *source, uint8_t length
 }
 
 // Pack a bitarray into a uint32_t.
-uint32_t PackBits(uint8_t start, uint8_t len, uint8_t *bits) {
+uint32_t PackBits(uint8_t start, uint8_t len, const uint8_t *bits) {
 
     if (len > 32) return 0;
 
@@ -1082,7 +1082,7 @@ int binstring_to_u96(uint32_t *hi2, uint32_t *hi, uint32_t *lo, const char *str)
  *
  * Returns the number of bits entered.
  */
-int binarray_to_u96(uint32_t *hi2, uint32_t *hi, uint32_t *lo, uint8_t *arr, int arrlen) {
+int binarray_to_u96(uint32_t *hi2, uint32_t *hi, uint32_t *lo, const uint8_t *arr, int arrlen) {
     int i = 0;
     for (; i < arrlen; i++) {
         uint8_t n = arr[i];
diff --git a/client/src/util.h b/client/src/util.h
index 532f47313..df9149829 100644
--- a/client/src/util.h
+++ b/client/src/util.h
@@ -86,17 +86,17 @@ int param_getstr(const char *line, int paramnum, char *str, size_t buffersize);
 
 int hextobinarray(char *target, char *source);
 int hextobinstring(char *target, char *source);
-int binarraytohex(char *target, const size_t targetlen, char *source, size_t srclen);
+int binarraytohex(char *target, const size_t targetlen, const char *source, size_t srclen);
 void binarraytobinstring(char *target,  char *source, int length);
 int binstring2binarray(uint8_t *target, char *source, int length);
 
-uint8_t GetParity(uint8_t *bits, uint8_t type, int length);
+uint8_t GetParity(const uint8_t *bits, uint8_t type, int length);
 void wiegand_add_parity(uint8_t *target, uint8_t *source, uint8_t length);
 void wiegand_add_parity_swapped(uint8_t *target, uint8_t *source, uint8_t length);
 
 //void xor(unsigned char *dst, unsigned char *src, size_t len);
 
-uint32_t PackBits(uint8_t start, uint8_t len, uint8_t *bits);
+uint32_t PackBits(uint8_t start, uint8_t len, const uint8_t *bits);
 uint64_t HornerScheme(uint64_t num, uint64_t divider, uint64_t factor);
 
 int num_CPUs(void); // number of logical CPUs
@@ -111,7 +111,7 @@ char *str_dup(const char *src);
 char *str_ndup(const char *src, size_t len);
 int hexstring_to_u96(uint32_t *hi2, uint32_t *hi, uint32_t *lo, const char *str);
 int binstring_to_u96(uint32_t *hi2, uint32_t *hi, uint32_t *lo, const char *str);
-int binarray_to_u96(uint32_t *hi2, uint32_t *hi, uint32_t *lo, uint8_t *arr, int arrlen);
+int binarray_to_u96(uint32_t *hi2, uint32_t *hi, uint32_t *lo, const uint8_t *arr, int arrlen);
 
 uint32_t bitcount32(uint32_t a);
 uint64_t bitcount64(uint64_t a);
diff --git a/common/crapto1/crapto1.c b/common/crapto1/crapto1.c
index c59c28107..b70cfa159 100644
--- a/common/crapto1/crapto1.c
+++ b/common/crapto1/crapto1.c
@@ -418,7 +418,7 @@ static uint32_t fastfwd[2][8] = {
  * encrypt the NACK which is observed when varying only the 3 last bits of Nr
  * only correct iff [NR_3] ^ NR_3 does not depend on Nr_3
  */
-uint32_t *lfsr_prefix_ks(uint8_t ks[8], int isodd) {
+uint32_t *lfsr_prefix_ks(const uint8_t ks[8], int isodd) {
     uint32_t *candidates = calloc(4 << 10, sizeof(uint8_t));
     if (!candidates) return 0;
 
diff --git a/common/crapto1/crapto1.h b/common/crapto1/crapto1.h
index 708ddb033..105b3ab06 100644
--- a/common/crapto1/crapto1.h
+++ b/common/crapto1/crapto1.h
@@ -43,7 +43,7 @@ struct Crypto1State *lfsr_recovery64(uint32_t ks2, uint32_t ks3);
 struct Crypto1State *
 lfsr_common_prefix(uint32_t pfx, uint32_t rr, uint8_t ks[8], uint8_t par[8][8], uint32_t no_par);
 #endif
-uint32_t *lfsr_prefix_ks(uint8_t ks[8], int isodd);
+uint32_t *lfsr_prefix_ks(const uint8_t ks[8], int isodd);
 
 
 uint8_t lfsr_rollback_bit(struct Crypto1State *s, uint32_t in, int fb);
diff --git a/common/crc.c b/common/crc.c
index cb077182f..f4cf3834f 100644
--- a/common/crc.c
+++ b/common/crc.c
@@ -148,7 +148,7 @@ uint32_t CRC8Hitag1(uint8_t *buff, size_t size) {
     return crc_finish(&crc);
 }
 
-uint32_t CRC8Hitag1Bits(uint8_t *buff, size_t bitsize) {
+uint32_t CRC8Hitag1Bits(const uint8_t *buff, size_t bitsize) {
     crc_t crc;
     uint8_t data = 0;
     uint8_t n = 0;
diff --git a/common/crc.h b/common/crc.h
index 33e289ee8..f06ceddf0 100644
--- a/common/crc.h
+++ b/common/crc.h
@@ -78,6 +78,6 @@ uint32_t CRC8Cardx(uint8_t *buff, size_t size);
 
 // Calculate CRC-8/Hitag1, ZX8211 checksum
 uint32_t CRC8Hitag1(uint8_t *buff, size_t size);
-uint32_t CRC8Hitag1Bits(uint8_t *buff, size_t bitsize);
+uint32_t CRC8Hitag1Bits(const uint8_t *buff, size_t bitsize);
 
 #endif /* __CRC_H */
