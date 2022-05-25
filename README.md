commit b3731c60030e3266598eb02f4c56ca011538a30f
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Jan 6 18:18:48 2022 +0100

    cppcheck fixes

diff --git a/armsrc/nprintf.c b/armsrc/nprintf.c
index d60140d8c..458bfd8dd 100644
--- a/armsrc/nprintf.c
+++ b/armsrc/nprintf.c
@@ -354,11 +354,15 @@ static size_t _ftoa(out_fct_type out, char *buffer, size_t idx, size_t maxlen, d
         prec = PRINTF_DEFAULT_FLOAT_PRECISION;
     }
     // limit precision to 9, cause a prec >= 10 can lead to overflow errors
-    while ((len < PRINTF_FTOA_BUFFER_SIZE) && (prec > 9U)) {
+    while ((len < PRINTF_FTOA_BUFFER_SIZE)) {
         buf[len++] = '0';
         prec--;
     }
 
+    if (prec > 9U) {
+        prec = 9U;
+    }
+
     int whole = (int)value;
     double tmp = (value - whole) * pow10[prec];
     unsigned long frac = (unsigned long)tmp;
@@ -379,7 +383,7 @@ static size_t _ftoa(out_fct_type out, char *buffer, size_t idx, size_t maxlen, d
 
     if (prec == 0U) {
         diff = value - (double)whole;
-        if ((!(diff < 0.5) || (diff > 0.5)) && (whole & 1)) {
+        if (((diff < 0.5) == false) || (whole & 1)) {
             // exactly 0.5 and ODD, then round up
             // 1.5 -> 2, but 2.5 -> 2
             ++whole;
diff --git a/client/deps/cliparser/cliparser.c b/client/deps/cliparser/cliparser.c
index cae61a9e8..77db942a1 100644
--- a/client/deps/cliparser/cliparser.c
+++ b/client/deps/cliparser/cliparser.c
@@ -219,10 +219,7 @@ int CLIParamHexToBuf(struct arg_str *argstr, uint8_t *data, int maxdatalen, int
     // concat all strings in argstr into tmpstr[]
     //
     int res = CLIParamStrToBuf(argstr, tmpstr, sizeof(tmpstr), &tmplen);
-    if (res) {
-        return res;
-    }
-    if (tmplen == 0) {
+    if (res || (tmplen == 0)) {
         return res;
     }
 
diff --git a/client/deps/hardnested/hardnested_bf_core.c b/client/deps/hardnested/hardnested_bf_core.c
index 0f9eb3da8..3609f1938 100644
--- a/client/deps/hardnested/hardnested_bf_core.c
+++ b/client/deps/hardnested/hardnested_bf_core.c
@@ -124,7 +124,7 @@ typedef union {
 #endif
 
 // typedefs and declaration of functions:
-typedef uint64_t crack_states_bitsliced_t(uint32_t, uint8_t *, statelist_t *, uint32_t *, uint64_t *, uint32_t, uint8_t *, noncelist_t *);
+typedef uint64_t crack_states_bitsliced_t(uint32_t, uint8_t *, statelist_t *, uint32_t *, uint64_t *, uint32_t, const uint8_t *, noncelist_t *);
 crack_states_bitsliced_t crack_states_bitsliced_AVX512;
 crack_states_bitsliced_t crack_states_bitsliced_AVX2;
 crack_states_bitsliced_t crack_states_bitsliced_AVX;
@@ -133,7 +133,7 @@ crack_states_bitsliced_t crack_states_bitsliced_MMX;
 crack_states_bitsliced_t crack_states_bitsliced_NOSIMD;
 crack_states_bitsliced_t crack_states_bitsliced_dispatch;
 
-typedef void bitslice_test_nonces_t(uint32_t, uint32_t *, uint8_t *);
+typedef void bitslice_test_nonces_t(uint32_t, const uint32_t *, const uint8_t *);
 bitslice_test_nonces_t bitslice_test_nonces_AVX512;
 bitslice_test_nonces_t bitslice_test_nonces_AVX2;
 bitslice_test_nonces_t bitslice_test_nonces_AVX;
@@ -174,7 +174,7 @@ static bitslice_t bs_ones;
 static bitslice_t bs_zeroes;
 
 
-void BITSLICE_TEST_NONCES(uint32_t nonces_to_bruteforce, uint32_t *bf_test_nonce, uint8_t *bf_test_nonce_par) {
+void BITSLICE_TEST_NONCES(uint32_t nonces_to_bruteforce, const uint32_t *bf_test_nonce, const uint8_t *bf_test_nonce_par) {
 
     // initialize 1 and 0 vectors
     memset(bs_ones.bytes, 0xff, VECTOR_SIZE);
@@ -206,7 +206,9 @@ void BITSLICE_TEST_NONCES(uint32_t nonces_to_bruteforce, uint32_t *bf_test_nonce
 }
 
 
-uint64_t CRACK_STATES_BITSLICED(uint32_t cuid, uint8_t *best_first_bytes, statelist_t *p, uint32_t *keys_found, uint64_t *num_keys_tested, uint32_t nonces_to_bruteforce, uint8_t *bf_test_nonce_2nd_byte, noncelist_t *nonces) {
+uint64_t CRACK_STATES_BITSLICED(uint32_t cuid, uint8_t *best_first_bytes, statelist_t *p, uint32_t *keys_found,
+                                uint64_t *num_keys_tested, uint32_t nonces_to_bruteforce,
+                                const uint8_t *bf_test_nonce_2nd_byte, noncelist_t *nonces) {
 
     // Unlike aczid's implementation this doesn't roll back at all when performing bitsliced bruteforce.
     // We know that the best first byte is already shifted in. Testing with the remaining three bytes of
@@ -593,7 +595,10 @@ SIMDExecInstr GetSIMDInstrAuto(void) {
 }
 
 // determine the available instruction set at runtime and call the correct function
-uint64_t crack_states_bitsliced_dispatch(uint32_t cuid, uint8_t *best_first_bytes, statelist_t *p, uint32_t *keys_found, uint64_t *num_keys_tested, uint32_t nonces_to_bruteforce, uint8_t *bf_test_nonce_2nd_byte, noncelist_t *nonces) {
+uint64_t crack_states_bitsliced_dispatch(uint32_t cuid, uint8_t *best_first_bytes, statelist_t *p,
+                                         uint32_t *keys_found, uint64_t *num_keys_tested, 
+                                         uint32_t nonces_to_bruteforce, const uint8_t *bf_test_nonce_2nd_byte, 
+                                         noncelist_t *nonces) {
     switch (GetSIMDInstrAuto()) {
 #if defined(COMPILER_HAS_SIMD_AVX512)
         case SIMD_AVX512:
@@ -624,7 +629,7 @@ uint64_t crack_states_bitsliced_dispatch(uint32_t cuid, uint8_t *best_first_byte
     return (*crack_states_bitsliced_function_p)(cuid, best_first_bytes, p, keys_found, num_keys_tested, nonces_to_bruteforce, bf_test_nonce_2nd_byte, nonces);
 }
 
-void bitslice_test_nonces_dispatch(uint32_t nonces_to_bruteforce, uint32_t *bf_test_nonce, uint8_t *bf_test_nonce_par) {
+void bitslice_test_nonces_dispatch(uint32_t nonces_to_bruteforce, const uint32_t *bf_test_nonce, const uint8_t *bf_test_nonce_par) {
     switch (GetSIMDInstrAuto()) {
 #if defined(COMPILER_HAS_SIMD_AVX512)
         case SIMD_AVX512:
diff --git a/client/deps/hardnested/hardnested_bruteforce.c b/client/deps/hardnested/hardnested_bruteforce.c
index 2aab98524..71ff09144 100644
--- a/client/deps/hardnested/hardnested_bruteforce.c
+++ b/client/deps/hardnested/hardnested_bruteforce.c
@@ -117,7 +117,7 @@ inline uint8_t trailing_zeros(uint8_t byte) {
 }
 
 
-bool verify_key(uint32_t cuid, noncelist_t *nonces, uint8_t *best_first_bytes, uint32_t odd, uint32_t even) {
+bool verify_key(uint32_t cuid, noncelist_t *nonces, const uint8_t *best_first_bytes, uint32_t odd, uint32_t even) {
     struct Crypto1State pcs;
     for (uint16_t test_first_byte = 1; test_first_byte < 256; test_first_byte++) {
         noncelistentry_t *test_nonce = nonces[best_first_bytes[test_first_byte]].first;
diff --git a/client/deps/hardnested/hardnested_bruteforce.h b/client/deps/hardnested/hardnested_bruteforce.h
index a8e6786d5..8fe420aad 100644
--- a/client/deps/hardnested/hardnested_bruteforce.h
+++ b/client/deps/hardnested/hardnested_bruteforce.h
@@ -57,6 +57,6 @@ void prepare_bf_test_nonces(noncelist_t *nonces, uint8_t best_first_byte);
 bool brute_force_bs(float *bf_rate, statelist_t *candidates, uint32_t cuid, uint32_t num_acquired_nonces, uint64_t maximum_states, noncelist_t *nonces, uint8_t *best_first_bytes, uint64_t *found_key);
 float brute_force_benchmark(void);
 uint8_t trailing_zeros(uint8_t byte);
-bool verify_key(uint32_t cuid, noncelist_t *nonces, uint8_t *best_first_bytes, uint32_t odd, uint32_t even);
+bool verify_key(uint32_t cuid, noncelist_t *nonces, const uint8_t *best_first_bytes, uint32_t odd, uint32_t even);
 
 #endif
diff --git a/client/src/cipurse/cipursecrypto.c b/client/src/cipurse/cipursecrypto.c
index 48145d60a..63519d468 100644
--- a/client/src/cipurse/cipursecrypto.c
+++ b/client/src/cipurse/cipursecrypto.c
@@ -86,7 +86,7 @@ static uint64_t computeNLM48(uint64_t x, uint64_t y) {
     return res;
 }
 
-static void computeNLM(uint8_t *res, uint8_t *x, uint8_t *y) {
+static void computeNLM(uint8_t *res, const uint8_t *x, const uint8_t *y) {
     uint64_t x64 = 0;
     uint64_t y64 = 0;
 
@@ -213,7 +213,7 @@ bool CipurseCCheckCT(CipurseContext_t *ctx, uint8_t *CT) {
     return (memcmp(CT, ctx->CT, CIPURSE_AES_KEY_LENGTH) == 0);
 }
 
-static uint16_t CipurseCComputeMICCRC(uint8_t *data, size_t len) {
+static uint16_t CipurseCComputeMICCRC(const uint8_t *data, size_t len) {
     uint16_t initCRC = 0x6363;
     for (size_t i = 0; i < len; i++) {
         uint8_t ch = data[i] ^ initCRC;
diff --git a/client/src/cmdlfhitag.c b/client/src/cmdlfhitag.c
index 74534b915..4723ef39e 100644
--- a/client/src/cmdlfhitag.c
+++ b/client/src/cmdlfhitag.c
@@ -592,6 +592,9 @@ static int CmdLFHitagReader(const char *Cmd) {
 
     hitag_function htf;
     hitag_data htd;
+    memset(&htd, 0, sizeof(htd));
+
+
     uint16_t cmd = CMD_LF_HITAG_READER;
     if (s01) {
         cmd = CMD_LF_HITAGS_READ;
@@ -778,6 +781,8 @@ static int CmdLFHitagWriter(const char *Cmd) {
 
     hitag_function htf;
     hitag_data htd;
+    memset(&htd, 0, sizeof(htd));
+
     if (s03) {
         htf = WHTSF_CHALLENGE;
         memcpy(htd.auth.NrAr, nrar, sizeof(nrar));
@@ -881,10 +886,10 @@ static int CmdLFHitag2Dump(const char *Cmd) {
 
 
 // Annotate HITAG protocol
-void annotateHitag1(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize, bool is_response) {
+void annotateHitag1(char *exp, size_t size, const uint8_t *cmd, uint8_t cmdsize, bool is_response) {
 }
 
-void annotateHitag2(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize, bool is_response) {
+void annotateHitag2(char *exp, size_t size, const uint8_t *cmd, uint8_t cmdsize, bool is_response) {
 
     // iceman: live decrypt of trace?
     if (is_response) {
@@ -935,7 +940,7 @@ void annotateHitag2(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize, bool
 }
 
 
-void annotateHitagS(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize, bool is_response) {
+void annotateHitagS(char *exp, size_t size, const uint8_t *cmd, uint8_t cmdsize, bool is_response) {
 }
 
 static command_t CommandTable[] = {
diff --git a/client/src/cmdlfhitag.h b/client/src/cmdlfhitag.h
index 7f92285e8..5059557de 100644
--- a/client/src/cmdlfhitag.h
+++ b/client/src/cmdlfhitag.h
@@ -16,8 +16,8 @@
 int CmdLFHitag(const char *Cmd);
 
 int readHitagUid(void);
-void annotateHitag1(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize, bool is_response);
-void annotateHitag2(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize, bool is_response);
-void annotateHitagS(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize, bool is_response);
+void annotateHitag1(char *exp, size_t size, const uint8_t *cmd, uint8_t cmdsize, bool is_response);
+void annotateHitag2(char *exp, size_t size, const uint8_t *cmd, uint8_t cmdsize, bool is_response);
+void annotateHitagS(char *exp, size_t size, const uint8_t *cmd, uint8_t cmdsize, bool is_response);
 uint8_t hitag1_CRC_check(uint8_t *d, uint32_t nbit);
 #endif
