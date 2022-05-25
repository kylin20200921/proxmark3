commit d2c290c7c38276c3760bd93d61186ff36a8e7ad2
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Jan 6 23:20:55 2022 +0100

    cppcheck fixes for const

diff --git a/client/src/cmdanalyse.c b/client/src/cmdanalyse.c
index f4624dbb7..d30f7d298 100644
--- a/client/src/cmdanalyse.c
+++ b/client/src/cmdanalyse.c
@@ -32,10 +32,10 @@
 
 static int CmdHelp(const char *Cmd);
 
-static uint8_t calculateLRC(uint8_t *bytes, uint8_t len) {
+static uint8_t calculateLRC(const uint8_t *d, uint8_t n) {
     uint8_t lcr = 0;
-    for (uint8_t i = 0; i < len; i++)
-        lcr ^= bytes[i];
+    for (uint8_t i = 0; i < n; i++)
+        lcr ^= d[i];
     return lcr;
 }
 /*
diff --git a/client/src/cmdhf14b.c b/client/src/cmdhf14b.c
index 38aeb7f62..91f84aa0d 100644
--- a/client/src/cmdhf14b.c
+++ b/client/src/cmdhf14b.c
@@ -521,7 +521,7 @@ static char *get_st_chip_model(uint8_t data) {
     return retStr;
 }
 
-static char *get_st_lock_info(uint8_t model, uint8_t *lockbytes, uint8_t blk) {
+static char *get_st_lock_info(uint8_t model, const uint8_t *lockbytes, uint8_t blk) {
 
     static char str[16];
     char *s = str;
@@ -687,11 +687,11 @@ static char *get_st_lock_info(uint8_t model, uint8_t *lockbytes, uint8_t blk) {
     return s;
 }
 
-static uint8_t get_st_chipid(uint8_t *uid) {
+static uint8_t get_st_chipid(const uint8_t *uid) {
     return uid[5] >> 2;
 }
 
-static uint8_t get_st_cardsize(uint8_t *uid) {
+static uint8_t get_st_cardsize(const uint8_t *uid) {
     uint8_t chipid = get_st_chipid(uid);
     switch (chipid) {
         case 0x0:
diff --git a/client/src/cmdhfcryptorf.c b/client/src/cmdhfcryptorf.c
index 1f68b9bd4..a4cdf0272 100644
--- a/client/src/cmdhfcryptorf.c
+++ b/client/src/cmdhfcryptorf.c
@@ -314,7 +314,7 @@ static int CmdHFCryptoRFDump(const char *Cmd) {
     PacketResponseNG resp;
 
     // select
-    int status = 0;
+    int status;
     if (WaitForResponseTimeout(CMD_HF_ISO14443B_COMMAND, &resp, 2000)) {
         status = resp.oldarg[0];
         if (status < 0) {
diff --git a/client/src/cmdlfem4x05.c b/client/src/cmdlfem4x05.c
index 1c2b43efa..35f3af226 100644
--- a/client/src/cmdlfem4x05.c
+++ b/client/src/cmdlfem4x05.c
@@ -74,7 +74,7 @@ static const char *em_get_card_str(uint32_t config) {
 }
 
 // even parity COLUMN
-static bool em4x05_col_parity_test(uint8_t *bs, size_t size, uint8_t rows, uint8_t cols, uint8_t pType) {
+static bool em4x05_col_parity_test(const uint8_t *bs, size_t size, uint8_t rows, uint8_t cols, uint8_t pType) {
     if (rows * cols > size) return false;
     uint8_t colP = 0;
 
@@ -1889,7 +1889,7 @@ int CmdEM4x05Unlock(const char *Cmd) {
     return exit_code;
 }
 
-static size_t em4x05_Sniff_GetNextBitStart(size_t idx, size_t sc, int *data, size_t *pulsesamples) {
+static size_t em4x05_Sniff_GetNextBitStart(size_t idx, size_t sc, const int *data, size_t *pulsesamples) {
     while ((idx < sc) && (data[idx] <= 10)) // find a going high
         idx++;
 
@@ -1905,7 +1905,7 @@ static size_t em4x05_Sniff_GetNextBitStart(size_t idx, size_t sc, int *data, siz
     return idx;
 }
 
-uint32_t static em4x05_Sniff_GetBlock(char *bits, bool fwd) {
+static uint32_t em4x05_Sniff_GetBlock(const char *bits, bool fwd) {
     uint32_t value = 0;
     uint8_t idx;
     bool parityerror = false;
diff --git a/client/src/cmdlfnexwatch.c b/client/src/cmdlfnexwatch.c
index 3b02b798e..d69449c46 100644
--- a/client/src/cmdlfnexwatch.c
+++ b/client/src/cmdlfnexwatch.c
@@ -44,7 +44,7 @@ static uint8_t nexwatch_parity_swap(uint8_t parity) {
 }
 // parity check
 // from 32b hex id, 4b mode,
-static uint8_t nexwatch_parity(uint8_t hexid[5]) {
+static uint8_t nexwatch_parity(const uint8_t hexid[5]) {
     uint8_t p = 0;
     for (uint8_t i = 0; i < 5; i++) {
         p ^= NIBBLE_HIGH(hexid[i]);
diff --git a/client/src/cmdlft55xx.c b/client/src/cmdlft55xx.c
index 029f595fb..289105d5c 100644
--- a/client/src/cmdlft55xx.c
+++ b/client/src/cmdlft55xx.c
@@ -3905,7 +3905,7 @@ static int CmdT55xxProtect(const char *Cmd) {
 // if the difference between a and b is less then or eq to d  i.e. does a = b +/- d
 #define APPROX_EQ(a, b, d) ((abs(a - b) <= d) ? true : false)
 
-static uint8_t t55sniff_get_packet(int *pulseBuffer, char *data, uint8_t width0, uint8_t width1, uint8_t tolerance) {
+static uint8_t t55sniff_get_packet(const int *pulseBuffer, char *data, uint8_t width0, uint8_t width1, uint8_t tolerance) {
     int i = 0;
     bool ok = true;
     uint8_t len = 0;
diff --git a/client/src/cmdsmartcard.c b/client/src/cmdsmartcard.c
index 7384d5a15..8039f44d2 100644
--- a/client/src/cmdsmartcard.c
+++ b/client/src/cmdsmartcard.c
@@ -59,7 +59,7 @@ out:
     return retval;
 }
 
-static uint8_t GetATRTA1(uint8_t *atr, size_t atrlen) {
+static uint8_t GetATRTA1(const uint8_t *atr, size_t atrlen) {
     if (atrlen > 2) {
         uint8_t T0 = atr[1];
         if (T0 & 0x10)
@@ -258,7 +258,7 @@ static void PrintATR(uint8_t *atr, size_t atrlen) {
 
 static int smart_wait(uint8_t *out, int maxoutlen, bool verbose) {
     int i = 4;
-    uint32_t len = 0;
+    uint32_t len;
     do {
         clearCommandBuffer();
         PacketResponseNG resp;
diff --git a/client/src/crypto/libpcrypto.c b/client/src/crypto/libpcrypto.c
index 5bc03bd9f..6039aa55c 100644
--- a/client/src/crypto/libpcrypto.c
+++ b/client/src/crypto/libpcrypto.c
@@ -585,7 +585,7 @@ exit:
     return res;
 }
 
-void bin_xor(uint8_t *d1, uint8_t *d2, size_t len) {
+void bin_xor(uint8_t *d1, const uint8_t *d2, size_t len) {
     for (size_t i = 0; i < len; i++)
         d1[i] = d1[i] ^ d2[i];
 }
@@ -598,7 +598,7 @@ void AddISO9797M2Padding(uint8_t *ddata, size_t *ddatalen, uint8_t *sdata, size_
     ddata[sdatalen] = ISO9797_M2_PAD_BYTE;
 }
 
-size_t FindISO9797M2PaddingDataLen(uint8_t *data, size_t datalen) {
+size_t FindISO9797M2PaddingDataLen(const uint8_t *data, size_t datalen) {
     for (int i = datalen; i > 0; i--) {
         if (data[i - 1] == 0x80)
             return i - 1;
diff --git a/client/src/crypto/libpcrypto.h b/client/src/crypto/libpcrypto.h
index 89f13f486..f35cdf2d7 100644
--- a/client/src/crypto/libpcrypto.h
+++ b/client/src/crypto/libpcrypto.h
@@ -47,11 +47,11 @@ char *ecdsa_get_error(int ret);
 
 int ecdsa_nist_test(bool verbose);
 
-void bin_xor(uint8_t *d1, uint8_t *d2, size_t len);
+void bin_xor(uint8_t *d1, const uint8_t *d2, size_t len);
 
 #define ISO9797_M2_PAD_BYTE 0x80
 void AddISO9797M2Padding(uint8_t *ddata, size_t *ddatalen, uint8_t *sdata, size_t sdatalen, size_t blocklen);
-size_t FindISO9797M2PaddingDataLen(uint8_t *data, size_t datalen);
+size_t FindISO9797M2PaddingDataLen(const uint8_t *data, size_t datalen);
 
 
 #endif /* libpcrypto.h */
