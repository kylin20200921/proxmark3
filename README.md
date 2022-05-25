commit 073b47d4806a5be647b50a749e057c2f43f42a72
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jan 9 13:39:37 2022 +0100

    fix to handle when first_byte_sum does not find a match in the possible sums array.  ie, leads to a crash

diff --git a/client/src/cmdhfmfhard.c b/client/src/cmdhfmfhard.c
index 21de93499..bb2d4b024 100644
--- a/client/src/cmdhfmfhard.c
+++ b/client/src/cmdhfmfhard.c
@@ -56,9 +56,11 @@
 #define DEBUG_KEY_ELIMINATION
 // #define DEBUG_REDUCTION
 
-static uint16_t sums[NUM_SUMS] = {0, 32, 56, 64, 80, 96, 104, 112, 120, 128, 136, 144, 152, 160, 176, 192, 200, 224, 256}; // possible sum property values
+// possible sum property values
+static uint16_t sums[NUM_SUMS] = {0, 32, 56, 64, 80, 96, 104, 112, 120, 128, 136, 144, 152, 160, 176, 192, 200, 224, 256};
 
-#define NUM_PART_SUMS                   9 // number of possible partial sum property values
+// number of possible partial sum property values
+#define NUM_PART_SUMS                  9 
 
 typedef enum {
     EVEN_STATE = 0,
@@ -79,7 +81,6 @@ static uint64_t known_target_key;
 static uint32_t test_state[2] = {0, 0};
 static float brute_force_per_second;
 
-
 static void get_SIMD_instruction_set(char *instruction_set) {
     switch (GetSIMDInstrAuto()) {
 #if defined(COMPILER_HAS_SIMD_AVX512)
@@ -108,7 +109,6 @@ static void get_SIMD_instruction_set(char *instruction_set) {
     }
 }
 
-
 static void print_progress_header(void) {
     char progress_text[80];
     char instr_set[12] = "";
@@ -143,7 +143,6 @@ void hardnested_print_progress(uint32_t nonces, const char *activity, float brut
     }
 }
 
-
 //////////////////////////////////////////////////////////////////////////////////////////////////////////////////
 // bitarray functions
 
@@ -243,7 +242,6 @@ static void init_bunzip2(bz_stream *compressed_stream, char *input_buffer, uint3
 
 }
 
-
 static void init_bitflip_bitarrays(void) {
 #if defined (DEBUG_REDUCTION)
     uint8_t line = 0;
@@ -372,7 +370,6 @@ static void init_bitflip_bitarrays(void) {
     hardnested_print_progress(0, progress_text, (float)(1LL << 47), 0);
 }
 
-
 static void free_bitflip_bitarrays(void) {
     for (int16_t bitflip = 0x3ff; bitflip > 0x000; bitflip--) {
         free_bitarray(bitflip_bitarrays[ODD_STATE][bitflip]);
@@ -382,7 +379,6 @@ static void free_bitflip_bitarrays(void) {
     }
 }
 
-
 //////////////////////////////////////////////////////////////////////////////////////////////////////////////////
 // sum property bitarrays
 
@@ -413,7 +409,6 @@ static uint16_t PartialSumProperty(uint32_t state, odd_even_t odd_even) {
     return sum;
 }
 
-
 static void init_part_sum_bitarrays(void) {
     for (odd_even_t odd_even = EVEN_STATE; odd_even <= ODD_STATE; odd_even++) {
         for (uint16_t part_sum_a0 = 0; part_sum_a0 < NUM_PART_SUMS; part_sum_a0++) {
@@ -456,7 +451,6 @@ static void init_part_sum_bitarrays(void) {
     }
 }
 
-
 static void free_part_sum_bitarrays(void) {
     for (int16_t part_sum_a8 = (NUM_PART_SUMS - 1); part_sum_a8 >= 0; part_sum_a8--) {
         free_bitarray(part_sum_a8_bitarrays[ODD_STATE][part_sum_a8]);
@@ -472,7 +466,6 @@ static void free_part_sum_bitarrays(void) {
     }
 }
 
-
 static void init_sum_bitarrays(void) {
     for (uint16_t sum_a0 = 0; sum_a0 < NUM_SUMS; sum_a0++) {
         for (odd_even_t odd_even = EVEN_STATE; odd_even <= ODD_STATE; odd_even++) {
@@ -496,7 +489,6 @@ static void init_sum_bitarrays(void) {
 
 }
 
-
 static void free_sum_bitarrays(void) {
     for (int8_t sum_a0 = NUM_SUMS - 1; sum_a0 >= 0; sum_a0--) {
         free_bitarray(sum_a0_bitarrays[ODD_STATE][sum_a0]);
@@ -504,7 +496,6 @@ static void free_sum_bitarrays(void) {
     }
 }
 
-
 #ifdef DEBUG_KEY_ELIMINATION
 static char failstr[250] = "";
 #endif
@@ -512,9 +503,7 @@ static char failstr[250] = "";
 static const float p_K0[NUM_SUMS] = { // the probability that a random nonce has a Sum Property K
     0.0290, 0.0083, 0.0006, 0.0339, 0.0048, 0.0934, 0.0119, 0.0489, 0.0602, 0.4180, 0.0602, 0.0489, 0.0119, 0.0934, 0.0048, 0.0339, 0.0006, 0.0083, 0.0290
 };
-
 static float my_p_K[NUM_SUMS];
-
 static const float *p_K;
 
 static uint32_t cuid;
@@ -534,7 +523,6 @@ static uint64_t sample_period = 0;
 static uint64_t num_keys_tested = 0;
 static statelist_t *candidates = NULL;
 
-
 static int add_nonce(uint32_t nonce_enc, uint8_t par_enc) {
     uint8_t first_byte = nonce_enc >> 24;
     noncelistentry_t *p1 = nonces[first_byte].first;
@@ -552,15 +540,15 @@ static int add_nonce(uint32_t nonce_enc, uint8_t par_enc) {
 
     if (p1 == NULL) {                                                          // need to add at the end of the list
         if (p2 == NULL) {           // list is empty yet. Add first entry.
-            p2 = nonces[first_byte].first = malloc(sizeof(noncelistentry_t));
+            p2 = nonces[first_byte].first = calloc(1, sizeof(noncelistentry_t));
         } else {                    // add new entry at end of existing list.
-            p2 = p2->next = malloc(sizeof(noncelistentry_t));
+            p2 = p2->next = calloc(1, sizeof(noncelistentry_t));
         }
     } else if ((p1->nonce_enc & 0x00ff0000) != (nonce_enc & 0x00ff0000)) {     // found distinct 2nd byte. Need to insert.
         if (p2 == NULL) {           // need to insert at start of list
-            p2 = nonces[first_byte].first = malloc(sizeof(noncelistentry_t));
+            p2 = nonces[first_byte].first = calloc(1, sizeof(noncelistentry_t));
         } else {
-            p2 = p2->next = malloc(sizeof(noncelistentry_t));
+            p2 = p2->next = calloc(1, sizeof(noncelistentry_t));
         }
     } else {                                                                   // we have seen this 2nd byte before. Nothing to add or insert.
         return (0);
@@ -577,7 +565,6 @@ static int add_nonce(uint32_t nonce_enc, uint8_t par_enc) {
     return (1); // new nonce added
 }
 
-
 static void init_nonce_memory(void) {
     for (uint16_t i = 0; i < 256; i++) {
         nonces[i].num = 0;
@@ -612,7 +599,6 @@ static void init_nonce_memory(void) {
     first_byte_Sum = 0;
 }
 
-
 static void free_nonce_list(noncelistentry_t *p) {
     if (p == NULL) {
         return;
@@ -642,13 +628,14 @@ static double p_hypergeometric(uint16_t i_K, uint16_t n, uint16_t k) {
     // P(X=0) = -----------------------------
     //               N*(N-1)*...*(N-n+1)
 
-
     uint16_t const N = 256;
     uint16_t K = sums[i_K];
 
+    // avoids log(x<=0) in calculation below
+    if (n - k > N - K || k > K) {
+        return 0.0;
+    }
 
-
-    if (n - k > N - K || k > K) return 0.0; // avoids log(x<=0) in calculation below
     if (k == 0) {
         // use logarithms to avoid overflow with huge factorials (double type can only hold 170!)
         double log_result = 0.0;
@@ -680,7 +667,9 @@ static double p_hypergeometric(uint16_t i_K, uint16_t n, uint16_t k) {
 }
 
 static float sum_probability(uint16_t i_K, uint16_t n, uint16_t k) {
-    if (k > sums[i_K]) return 0.0;
+    if (k > sums[i_K]) {
+        return 0.0;
+    }
 
     double p_T_is_k_when_S_is_K = p_hypergeometric(i_K, n, k);
     double p_S_is_K = p_K[i_K];
@@ -706,7 +695,6 @@ static void init_allbitflips_array(void) {
     }
 }
 
-
 static void update_allbitflips_array(void) {
     if (hardnested_stage & CHECK_2ND_BYTES) {
         for (uint16_t i = 0; i < 256; i++) {
@@ -724,12 +712,10 @@ static void update_allbitflips_array(void) {
     }
 }
 
-
 static uint32_t estimated_num_states_part_sum_coarse(uint16_t part_sum_a0_idx, uint16_t part_sum_a8_idx, odd_even_t odd_even) {
     return part_sum_count[odd_even][part_sum_a0_idx][part_sum_a8_idx];
 }
 
-
 static uint32_t estimated_num_states_part_sum(uint8_t first_byte, uint16_t part_sum_a0_idx, uint16_t part_sum_a8_idx, odd_even_t odd_even) {
     if (odd_even == ODD_STATE) {
         return count_bitarray_AND3(part_sum_a0_bitarrays[odd_even][part_sum_a0_idx],
@@ -751,7 +737,6 @@ static uint32_t estimated_num_states_part_sum(uint8_t first_byte, uint16_t part_
     // }
 }
 
-
 static uint64_t estimated_num_states(uint8_t first_byte, uint16_t sum_a0, uint16_t sum_a8) {
     uint64_t num_states = 0;
     for (uint8_t p = 0; p < NUM_PART_SUMS; p++) {
@@ -771,7 +756,6 @@ static uint64_t estimated_num_states(uint8_t first_byte, uint16_t sum_a0, uint16
     return num_states;
 }
 
-
 static uint64_t estimated_num_states_coarse(uint16_t sum_a0, uint16_t sum_a8) {
     uint64_t num_states = 0;
     for (uint8_t p = 0; p < NUM_PART_SUMS; p++) {
@@ -791,7 +775,6 @@ static uint64_t estimated_num_states_coarse(uint16_t sum_a0, uint16_t sum_a8) {
     return num_states;
 }
 
-
 static void update_p_K(void) {
     if (hardnested_stage & CHECK_2ND_BYTES) {
         uint64_t total_count = 0;
@@ -813,7 +796,6 @@ static void update_p_K(void) {
     }
 }
 
-
 static void update_sum_bitarrays(odd_even_t odd_even) {
     if (all_bitflips_bitarray_dirty[odd_even]) {
         for (uint8_t part_sum = 0; part_sum < NUM_PART_SUMS; part_sum++) {
@@ -833,7 +815,6 @@ static void update_sum_bitarrays(odd_even_t odd_even) {
     }
 }
 
-
 static int compare_expected_num_brute_force(const void *b1, const void *b2) {
     uint8_t index1 = *(uint8_t *)b1;
     uint8_t index2 = *(uint8_t *)b2;
@@ -842,7 +823,6 @@ static int compare_expected_num_brute_force(const void *b1, const void *b2) {
     return (score1 > score2) - (score1 < score2);
 }
 
-
 static int compare_sum_a8_guess(const void *b1, const void *b2) {
     float prob1 = ((guess_sum_a8_t *)b1)->prob;
     float prob2 = ((guess_sum_a8_t *)b2)->prob;
@@ -850,7 +830,6 @@ static int compare_sum_a8_guess(const void *b1, const void *b2) {
 
 }
 
-
 static float check_smallest_bitflip_bitarrays(void) {
     uint64_t smallest = 1LL << 48;
     // initialize best_first_bytes, do a rough estimation on remaining states
@@ -871,7 +850,6 @@ static float check_smallest_bitflip_bitarrays(void) {
     return (float)smallest / 2.0;
 }
 
-
 static void update_expected_brute_force(uint8_t best_byte) {
 
     float total_prob = 0.0;
@@ -892,7 +870,6 @@ static void update_expected_brute_force(uint8_t best_byte) {
     return;
 }
 
-
 static float sort_best_first_bytes(void) {
 
     // initialize best_first_bytes, do a rough estimation on remaining states for each Sum_a8 property
@@ -981,7 +958,6 @@ static float sort_best_first_bytes(void) {
     return nonces[best_first_bytes[0]].expected_num_brute_force;
 }
 
-
 static float update_reduction_rate(float last, bool init) {
 #define QUEUE_LEN 4
     static float queue[QUEUE_LEN];
@@ -1024,7 +1000,6 @@ static float update_reduction_rate(float last, bool init) {
     return reduction_rate;
 }
 
-
 static bool shrink_key_space(float *brute_forces) {
 #if defined(DEBUG_REDUCTION)
     PrintAndLogEx(INFO, "shrink_key_space() with stage = 0x%02x\n", hardnested_stage);
@@ -1044,7 +1019,6 @@ static bool shrink_key_space(float *brute_forces) {
 
 }
 
-
 static void estimate_sum_a8(void) {
     if (first_byte_num == 256) {
         for (uint16_t i = 0; i < 256; i++) {
@@ -1060,7 +1034,6 @@ static void estimate_sum_a8(void) {
     }
 }
 
-
 static int read_nonce_file(char *filename) {
 
     if (filename == NULL) {
@@ -1107,17 +1080,21 @@ static int read_nonce_file(char *filename) {
     sprintf(progress_string, "Target Block=%d, Keytype=%c", trgBlockNo, trgKeyType == 0 ? 'A' : 'B');
     hardnested_print_progress(num_acquired_nonces, progress_string, (float)(1LL << 47), 0);
 
+    bool got_match = false;
     for (uint8_t i = 0; i < NUM_SUMS; i++) {
         if (first_byte_Sum == sums[i]) {
             first_byte_Sum = i;
+            got_match = true;
             break;
         }
     }
-
-    return 0;
+    if (got_match == false) {
+        PrintAndLogEx(FAILED, "No match for the First_Byte_Sum (%u), is the card a genuine MFC Ev1? ", first_byte_Sum);
+        return 1;
+    }
+    return PM3_SUCCESS;
 }
 
-
 static noncelistentry_t *SearchFor2ndByte(uint8_t b1, uint8_t b2) {
     noncelistentry_t *p = nonces[b1].first;
     while (p != NULL) {
@@ -1129,7 +1106,6 @@ static noncelistentry_t *SearchFor2ndByte(uint8_t b1, uint8_t b2) {
     return NULL;
 }
 
-
 static bool timeout(void) {
     return (msclock() > last_sample_clock + sample_period);
 }
@@ -1233,7 +1209,6 @@ __attribute__((force_align_arg_pointer))
     return NULL;
 }
 
-
 static void check_for_BitFlipProperties(bool time_budget) {
     // create and run worker threads
     pthread_t thread_id[NUM_CHECK_BITFLIPS_THREADS];
@@ -1272,7 +1247,6 @@ static void check_for_BitFlipProperties(bool time_budget) {
 #endif
 }
 
-
 static void update_nonce_data(bool time_budget) {
     check_for_BitFlipProperties(time_budget);
     update_allbitflips_array();
@@ -1282,7 +1256,6 @@ static void update_nonce_data(bool time_budget) {
     estimate_sum_a8();
 }
 
-
 static void apply_sum_a0(void) {
     uint32_t old_count = num_all_bitflips_bitarray[EVEN_STATE];
     num_all_bitflips_bitarray[EVEN_STATE] = count_bitarray_AND(all_bitflips_bitarray[EVEN_STATE], sum_a0_bitarrays[EVEN_STATE][first_byte_Sum]);
@@ -1296,7 +1269,6 @@ static void apply_sum_a0(void) {
     }
 }
 
-
 static void simulate_MFplus_RNG(uint32_t test_cuid, uint64_t test_key, uint32_t *nt_enc, uint8_t *par_enc) {
     struct Crypto1State sim_cs = {0, 0};
 
@@ -1316,10 +1288,9 @@ static void simulate_MFplus_RNG(uint32_t test_cuid, uint64_t test_key, uint32_t
         uint8_t nt_byte_par_enc = ks_par ^ oddparity8(nt_byte_dec);  // determine the nt byte's parity and encode it
         *par_enc = (*par_enc << 1) | nt_byte_par_enc;
     }
-
 }
 
-static void simulate_acquire_nonces(void) {
+static int simulate_acquire_nonces(void) {
     time_t time1 = time(NULL);
     last_sample_clock = 0;
     sample_period = 1000; // for simulation
@@ -1355,12 +1326,21 @@ static void simulate_acquire_nonces(void) {
 
         if (first_byte_num == 256) {
             if (hardnested_stage == CHECK_1ST_BYTES) {
+
+                bool got_match = false;
                 for (uint8_t i = 0; i < NUM_SUMS; i++) {
                     if (first_byte_Sum == sums[i]) {
                         first_byte_Sum = i;
+                        got_match = true;
                         break;
                     }
                 }
+
+                if (got_match == false) {
+                    PrintAndLogEx(FAILED, "No match for the First_Byte_Sum (%u), is the card a genuine MFC Ev1? ", first_byte_Sum);
+                    return PM3_ESOFT;
+                }
+
                 hardnested_stage |= CHECK_2ND_BYTES;
                 apply_sum_a0();
             }
@@ -1389,10 +1369,9 @@ static void simulate_acquire_nonces(void) {
     // );
 
     fprintf(fstats, "%" PRIu32 ";%" PRIu32 ";%1.0f;", total_num_nonces, num_acquired_nonces, difftime(end_time, time1));
-
+    return PM3_SUCCESS;
 }
 
-
 static int acquire_nonces(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_t trgBlockNo, uint8_t trgKeyType, bool nonce_file_write, bool slow, char *filename) {
 
     last_sample_clock = msclock();
@@ -1486,12 +1465,20 @@ static int acquire_nonces(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_
 
             if (first_byte_num == 256) {
                 if (hardnested_stage == CHECK_1ST_BYTES) {
+                    bool got_match = false;
                     for (uint8_t i = 0; i < NUM_SUMS; i++) {
                         if (first_byte_Sum == sums[i]) {
                             first_byte_Sum = i;
+                            got_match = true;
                             break;
                         }
                     }
+
+                    if (got_match == false) {
+                        PrintAndLogEx(FAILED, "No match for the First_Byte_Sum (%u), is the card a genuine MFC Ev1? ", first_byte_Sum);
+                        return 4;
+                    }
+
                     hardnested_stage |= CHECK_2ND_BYTES;
                     apply_sum_a0();
                 }
@@ -1550,10 +1537,9 @@ static int acquire_nonces(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_
         fclose(fnonces);
     }
 
-    return 0;
+    return PM3_SUCCESS;
 }
 
-
 static inline bool invariant_holds(uint_fast8_t byte_diff, uint_fast32_t state1, uint_fast32_t state2, uint_fast8_t bit, uint_fast8_t state_bit) {
     uint_fast8_t j_1_bit_mask = 0x01 << (bit - 1);
     uint_fast8_t bit_diff = byte_diff & j_1_bit_mask;                                               // difference of (j-1)th bit
@@ -1564,7 +1550,6 @@ static inline bool invariant_holds(uint_fast8_t byte_diff, uint_fast32_t state1,
     return !all_diff;
 }
 
-
 static inline bool invalid_state(uint_fast8_t byte_diff, uint_fast32_t state1, uint_fast32_t state2, uint_fast8_t bit, uint_fast8_t state_bit) {
     uint_fast8_t j_bit_mask = (0x01 << bit);
     uint_fast8_t bit_diff = byte_diff & j_bit_mask;                                                 // difference of jth bit
@@ -1574,7 +1559,6 @@ static inline bool invalid_state(uint_fast8_t byte_diff, uint_fast32_t state1, u
     return all_diff;
 }
 
-
 static inline bool remaining_bits_match(uint_fast8_t num_common_bits, uint_fast8_t byte_diff, uint_fast32_t state1, uint_fast32_t state2, odd_even_t odd_even) {
     if (odd_even) {
         // odd bits
@@ -1619,11 +1603,9 @@ static inline bool remaining_bits_match(uint_fast8_t num_common_bits, uint_fast8
     return true; // valid state
 }
 
-
 static pthread_mutex_t statelist_cache_mutex = PTHREAD_MUTEX_INITIALIZER;
 static pthread_mutex_t book_of_work_mutex = PTHREAD_MUTEX_INITIALIZER;
 
-
 typedef enum {
     TO_BE_DONE,
     WORK_IN_PROGRESS,
@@ -1636,7 +1618,6 @@ static struct sl_cache_entry {
     work_status_t cache_status;
 } sl_cache[NUM_PART_SUMS][NUM_PART_SUMS][2];
 
-
 static void init_statelist_cache(void) {
     pthread_mutex_lock(&statelist_cache_mutex);
     for (uint16_t i = 0; i < NUM_PART_SUMS; i++) {
@@ -1651,7 +1632,6 @@ static void init_statelist_cache(void) {
     pthread_mutex_unlock(&statelist_cache_mutex);
 }
 
-
 static void free_statelist_cache(void) {
     pthread_mutex_lock(&statelist_cache_mutex);
     for (uint16_t i = 0; i < NUM_PART_SUMS; i++) {
@@ -1750,7 +1730,6 @@ static void bitarray_to_list(uint8_t byte, uint32_t *bitarray, uint32_t *state_l
     *len = p - state_list;
 }
 
-
 static void add_cached_states(statelist_t *cands, uint16_t part_sum_a0, uint16_t part_sum_a8, odd_even_t odd_even) {
     cands->states[odd_even] = sl_cache[part_sum_a0 / 2][part_sum_a8 / 2][odd_even].sl;
     cands->len[odd_even] = sl_cache[part_sum_a0 / 2][part_sum_a8 / 2][odd_even].len;
@@ -2227,7 +2206,6 @@ int mfnestedhard(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_t trgBloc
     char instr_set[12] = {0};
 
     get_SIMD_instruction_set(instr_set);
-    PrintAndLogEx(SUCCESS, "Using " _GREEN_("%s") " SIMD core.", instr_set);
 
     // initialize static arrays
     memset(part_sum_count, 0, sizeof(part_sum_count));
@@ -2243,7 +2221,7 @@ int mfnestedhard(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_t trgBloc
         setlocale(LC_NUMERIC, "");
         if ((fstats = fopen("hardnested_stats.txt", "a")) == NULL) {
             PrintAndLogEx(WARNING, "Could not create/open file " _YELLOW_("hardnested_stats.txt"));
-            return 3;
+            return PM3_EFILE;
         }
 
         for (uint32_t i = 0; i < tests; i++) {
@@ -2267,7 +2245,9 @@ int mfnestedhard(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_t trgBloc
             init_nonce_memory();
             update_reduction_rate(0.0, true);
 
-            simulate_acquire_nonces();
+            if (simulate_acquire_nonces() != PM3_SUCCESS) {
+                return 3;
+            }
 
             set_test_state(best_first_bytes[0]);
 
