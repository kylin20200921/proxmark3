commit 287a99d1861f4007b5cdf8d91ff91587437a526e
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Nov 28 21:20:13 2021 +0100

    hardnested - text output adaptation

diff --git a/client/deps/hardnested/hardnested_bruteforce.c b/client/deps/hardnested/hardnested_bruteforce.c
index 84f34e808..2aab98524 100644
--- a/client/deps/hardnested/hardnested_bruteforce.c
+++ b/client/deps/hardnested/hardnested_bruteforce.c
@@ -75,7 +75,7 @@ THE SOFTWARE.
 //#define WRITE_BENCH_FILE
 
 // debugging options
-#define DEBUG_KEY_ELIMINATION
+#define DEBUG_KEY_ELIMINATION           1
 // #define DEBUG_BRUTE_FORCE
 
 typedef enum {
@@ -173,7 +173,7 @@ crack_states_thread(void *x) {
 
                 char progress_text[80];
                 char keystr[19];
-                sprintf(keystr, "%012" PRIx64 "  ", key);
+                sprintf(keystr, "%012" PRIX64 "  ", key);
                 sprintf(progress_text, "Brute force phase completed.  Key found: " _GREEN_("%s"), keystr);
                 hardnested_print_progress(thread_arg->num_acquired_nonces, progress_text, 0.0, 0);
                 break;
diff --git a/client/src/cmdhfmfhard.c b/client/src/cmdhfmfhard.c
index 22b659110..310492c68 100644
--- a/client/src/cmdhfmfhard.c
+++ b/client/src/cmdhfmfhard.c
@@ -106,14 +106,15 @@ static void print_progress_header(void) {
     char progress_text[80];
     char instr_set[12] = "";
     get_SIMD_instruction_set(instr_set);
-    sprintf(progress_text, "Start using %d threads and %s SIMD core", num_CPUs(), instr_set);
-    PrintAndLogEx(NORMAL, "\n\n");
-    PrintAndLogEx(NORMAL, " time    | #nonces | Activity                                                | expected to brute force");
-    PrintAndLogEx(NORMAL, "         |         |                                                         | #states         | time ");
-    PrintAndLogEx(NORMAL, "------------------------------------------------------------------------------------------------------");
-    PrintAndLogEx(NORMAL, "       0 |       0 | %-55s |                 |", progress_text);
-}
+    sprintf(progress_text, "Start using " _YELLOW_("%d") " threads and " _YELLOW_("%s") " SIMD core", num_CPUs(), instr_set);
 
+    PrintAndLogEx(INFO, "Hardnested attack starting...");
+    PrintAndLogEx(INFO, "---------+---------+---------------------------------------------------------+-----------------+-------");
+    PrintAndLogEx(INFO, "         |         |                                                         | Expected to brute force");
+    PrintAndLogEx(INFO, " Time    | #nonces | Activity                                                | #states         | time ");
+    PrintAndLogEx(INFO, "---------+---------+---------------------------------------------------------+-----------------+-------");
+    PrintAndLogEx(INFO, "       0 |       0 | %-73s |                 |", progress_text);
+}
 
 void hardnested_print_progress(uint32_t nonces, const char *activity, float brute_force, uint64_t min_diff_print_time) {
     static uint64_t last_print_time = 0;
@@ -131,7 +132,7 @@ void hardnested_print_progress(uint32_t nonces, const char *activity, float brut
         } else {
             sprintf(brute_force_time_string, "%2.0fd", brute_force_time / (60 * 60 * 24));
         }
-        PrintAndLogEx(NORMAL, " %7.0f | %7u | %-55s | %15.0f | %5s", (float)total_time / 1000.0, nonces, activity, brute_force, brute_force_time_string);
+        PrintAndLogEx(INFO, " %7.0f | %7u | %-55s | %15.0f | %5s", (float)total_time / 1000.0, nonces, activity, brute_force, brute_force_time_string);
     }
 }
 
@@ -311,10 +312,10 @@ static void init_bitflip_bitarrays(void) {
                     bitflip_bitarrays[odd_even][bitflip] = bitset;
                     count_bitflip_bitarrays[odd_even][bitflip] = count;
 #if defined (DEBUG_REDUCTION)
-                    PrintAndLogEx(NORMAL, "(%03" PRIx16 " %s:%5.1f%%) ", bitflip, odd_even ? "odd " : "even", (float)count / (1 << 24) * 100.0);
+                    PrintAndLogEx(INFO, "(%03" PRIx16 " %s:%5.1f%%) ", bitflip, odd_even ? "odd " : "even", (float)count / (1 << 24) * 100.0);
                     line++;
                     if (line == 8) {
-                        PrintAndLogEx(NORMAL, "\n");
+                        PrintAndLogEx(NORMAL, "");
                         line = 0;
                     }
 #endif
@@ -347,16 +348,16 @@ static void init_bitflip_bitarrays(void) {
     }
     qsort(all_effective_bitflip, num_1st_byte_effective_bitflips, sizeof(uint16_t), compare_count_bitflip_bitarrays);
 #if defined (DEBUG_REDUCTION)
-    PrintAndLogEx(NORMAL, "\n1st byte effective bitflips (%d): \n", num_1st_byte_effective_bitflips);
+    PrintAndLogEx(INFO, "1st byte effective bitflips (%d): ", num_1st_byte_effective_bitflips);
     for (uint16_t i = 0; i < num_1st_byte_effective_bitflips; i++) {
-        PrintAndLogEx(NORMAL, "%03x ",  all_effective_bitflip[i]);
+        PrintAndLogEx(INFO, "%03x ",  all_effective_bitflip[i]);
     }
 #endif
     qsort(all_effective_bitflip + num_1st_byte_effective_bitflips, num_all_effective_bitflips - num_1st_byte_effective_bitflips, sizeof(uint16_t), compare_count_bitflip_bitarrays);
 #if defined (DEBUG_REDUCTION)
-    PrintAndLogEx(NORMAL, "\n2nd byte effective bitflips (%d): \n", num_all_effective_bitflips - num_1st_byte_effective_bitflips);
+    PrintAndLogEx(INFO, "2nd byte effective bitflips (%d): ", num_all_effective_bitflips - num_1st_byte_effective_bitflips);
     for (uint16_t i = num_1st_byte_effective_bitflips; i < num_all_effective_bitflips; i++) {
-        PrintAndLogEx(NORMAL, "%03x ",  all_effective_bitflip[i]);
+        PrintAndLogEx(INFO, "%03x ",  all_effective_bitflip[i]);
     }
 #endif
     char progress_text[80];
@@ -418,7 +419,7 @@ static void init_part_sum_bitarrays(void) {
         }
     }
     for (odd_even_t odd_even = EVEN_STATE; odd_even <= ODD_STATE; odd_even++) {
-        //PrintAndLogEx(NORMAL, "(%d, %" PRIu16 ")...", odd_even, part_sum_a0);
+        //PrintAndLogEx(INFO, "(%d, %" PRIu16 ")...", odd_even, part_sum_a0);
         for (uint32_t state = 0; state < (1 << 20); state++) {
             uint16_t part_sum_a0 = PartialSumProperty(state, odd_even) / 2;
             for (uint16_t low_bits = 0; low_bits < 1 << 4; low_bits++) {
@@ -438,7 +439,7 @@ static void init_part_sum_bitarrays(void) {
         }
     }
     for (odd_even_t odd_even = EVEN_STATE; odd_even <= ODD_STATE; odd_even++) {
-        //PrintAndLogEx(NORMAL, "(%d, %" PRIu16 ")...", odd_even, part_sum_a8);
+        //PrintAndLogEx(INFO, "(%d, %" PRIu16 ")...", odd_even, part_sum_a8);
         for (uint32_t state = 0; state < (1 << 20); state++) {
             uint16_t part_sum_a8 = PartialSumProperty(state, odd_even) / 2;
             for (uint16_t high_bits = 0; high_bits < 1 << 4; high_bits++) {
@@ -614,7 +615,6 @@ static void free_nonce_list(noncelistentry_t *p) {
     }
 }
 
-
 static void free_nonces_memory(void) {
     for (uint16_t i = 0; i < 256; i++) {
         free_nonce_list(nonces[i].first);
@@ -625,10 +625,6 @@ static void free_nonces_memory(void) {
     }
 }
 
-
-
-
-
 static double p_hypergeometric(uint16_t i_K, uint16_t n, uint16_t k) {
     // for efficient computation we are using the recursive definition
     //                      (K-k+1) * (n-k+1)
@@ -676,7 +672,6 @@ static double p_hypergeometric(uint16_t i_K, uint16_t n, uint16_t k) {
     }
 }
 
-
 static float sum_probability(uint16_t i_K, uint16_t n, uint16_t k) {
     if (k > sums[i_K]) return 0.0;
 
@@ -689,7 +684,6 @@ static float sum_probability(uint16_t i_K, uint16_t n, uint16_t k) {
     return (p_T_is_k_when_S_is_K * p_S_is_K / p_T_is_k);
 }
 
-
 static uint32_t part_sum_count[2][NUM_PART_SUMS][NUM_PART_SUMS];
 
 static void init_allbitflips_array(void) {
@@ -804,9 +798,9 @@ static void update_p_K(void) {
             float f = estimated_num_states_coarse(sum_a0, sum_a8);
             my_p_K[sum_a8_idx] = f / total_count;
         }
-        // PrintAndLogEx(NORMAL, "my_p_K = [");
+        // PrintAndLogEx(INFO,  "my_p_K = [");
         // for (uint8_t sum_a8_idx = 0; sum_a8_idx < NUM_SUMS; sum_a8_idx++) {
-        // PrintAndLogEx(NORMAL, "%7.4f ", my_p_K[sum_a8_idx]);
+        // PrintAndLogEx(INFO, "%7.4f ", my_p_K[sum_a8_idx]);
         // }
         p_K = my_p_K;
     }
@@ -865,7 +859,7 @@ static float check_smallest_bitflip_bitarrays(void) {
 #if defined (DEBUG_REDUCTION)
     uint32_t num_odd = nonces[best_first_byte_smallest_bitarray].num_states_bitarray[ODD_STATE];
     uint32_t num_even = nonces[best_first_byte_smallest_bitarray].num_states_bitarray[EVEN_STATE]; // * (float)nonces[best_first_byte_smallest_bitarray^0x80].num_states_bitarray[EVEN_STATE] / num_all_bitflips_bitarray[EVEN_STATE];
-    PrintAndLogEx(NORMAL, "0x%02x: %8d * %8d = %12" PRIu64 " (2^%1.1f)\n", best_first_byte_smallest_bitarray, num_odd, num_even, (uint64_t)num_odd * num_even, log((uint64_t)num_odd * num_even) / log(2.0));
+    PrintAndLogEx(INFO, "0x%02x: %8d * %8d = %12" PRIu64 " (2^%1.1f)\n", best_first_byte_smallest_bitarray, num_odd, num_even, (uint64_t)num_odd * num_even, log((uint64_t)num_odd * num_even) / log(2.0));
 #endif
     return (float)smallest / 2.0;
 }
@@ -911,11 +905,11 @@ static float sort_best_first_bytes(void) {
     // sort based on expected number of states to brute force
     qsort(best_first_bytes, 256, 1, compare_expected_num_brute_force);
 
-    // PrintAndLogEx(NORMAL, "refine estimations: ");
+    // PrintAndLogEx(INFO, "refine estimations: ");
 #define NUM_REFINES 1
     // refine scores for the best:
     for (uint16_t i = 0; i < NUM_REFINES; i++) {
-        // PrintAndLogEx(NORMAL, "%d...", i);
+        // PrintAndLogEx(INFO, "%d...", i);
         uint16_t first_byte = best_first_bytes[i];
         for (uint8_t j = 0; j < NUM_SUMS && nonces[first_byte].sum_a8_guess[j].prob > 0.05; j++) {
             nonces[first_byte].sum_a8_guess[j].num_states = estimated_num_states(first_byte, sums[first_byte_Sum], sums[nonces[first_byte].sum_a8_guess[j].sum_a8_idx]);
@@ -925,17 +919,17 @@ static float sort_best_first_bytes(void) {
         // || nonces[first_byte].sum_a8_guess[2].num_states == 0) {
         // if (nonces[first_byte].sum_a8_guess[0].num_states == 0) {
         // nonces[first_byte].sum_a8_guess[0].prob = 0.0;
-        // PrintAndLogEx(NORMAL, "(0x%02x,%d)", first_byte, 0);
+        // PrintAndLogEx(INFO, "(0x%02x,%d)", first_byte, 0);
         // }
         // if (nonces[first_byte].sum_a8_guess[1].num_states == 0) {
         // nonces[first_byte].sum_a8_guess[1].prob = 0.0;
-        // PrintAndLogEx(NORMAL, "(0x%02x,%d)", first_byte, 1);
+        // PrintAndLogEx(INFO, "(0x%02x,%d)", first_byte, 1);
         // }
         // if (nonces[first_byte].sum_a8_guess[2].num_states == 0) {
         // nonces[first_byte].sum_a8_guess[2].prob = 0.0;
-        // PrintAndLogEx(NORMAL, "(0x%02x,%d)", first_byte, 2);
+        // PrintAndLogEx(INFO, "(0x%02x,%d)", first_byte, 2);
         // }
-        // PrintAndLogEx(NORMAL, "|");
+        // PrintAndLogEx(INFO, "|");
         // qsort(nonces[first_byte].sum_a8_guess, NUM_SUMS, sizeof(guess_sum_a8_t), compare_sum_a8_guess);
         // for (uint8_t j = 0; j < NUM_SUMS && nonces[first_byte].sum_a8_guess[j].prob > 0.05; j++) {
         // nonces[first_byte].sum_a8_guess[j].num_states = estimated_num_states(first_byte, sums[first_byte_Sum], sums[nonces[first_byte].sum_a8_guess[j].sum_a8_idx]);
@@ -971,7 +965,7 @@ static float sort_best_first_bytes(void) {
         }
     }
     if (best_byte != 0) {
-        // PrintAndLogEx(NORMAL, "0x%02x <-> 0x%02x", best_first_bytes[0], best_first_bytes[best_byte]);
+        // PrintAndLogEx(INFO, "0x%02x <-> 0x%02x", best_first_bytes[0], best_first_bytes[best_byte]);
         uint8_t tmp = best_first_bytes[0];
         best_first_bytes[0] = best_first_bytes[best_byte];
         best_first_bytes[best_byte] = tmp;
@@ -1018,7 +1012,7 @@ static float update_reduction_rate(float last, bool init) {
     float reduction_rate = -1.0 * dev_xy / dev_x2;  // the negative slope of the linear regression
 
 #if defined (DEBUG_REDUCTION)
-    PrintAndLogEx(NORMAL, "update_reduction_rate(%1.0f) = %1.0f per sample, brute_force_per_sample = %1.0f\n", last, reduction_rate, brute_force_per_second * (float)sample_period / 1000.0);
+    PrintAndLogEx(INFO, "update_reduction_rate(%1.0f) = %1.0f per sample, brute_force_per_sample = %1.0f\n", last, reduction_rate, brute_force_per_second * (float)sample_period / 1000.0);
 #endif
     return reduction_rate;
 }
@@ -1026,7 +1020,7 @@ static float update_reduction_rate(float last, bool init) {
 
 static bool shrink_key_space(float *brute_forces) {
 #if defined(DEBUG_REDUCTION)
-    PrintAndLogEx(NORMAL, "shrink_key_space() with stage = 0x%02x\n", hardnested_stage);
+    PrintAndLogEx(INFO, "shrink_key_space() with stage = 0x%02x\n", hardnested_stage);
 #endif
     float brute_forces1 = check_smallest_bitflip_bitarrays();
     float brute_forces2 = (float)(1LL << 47);
@@ -1072,11 +1066,11 @@ static int read_nonce_file(char *filename) {
 
     num_acquired_nonces = 0;
     if ((fnonces = fopen(filename, "rb")) == NULL) {
-        PrintAndLogEx(WARNING, "Could not open file %s", filename);
+        PrintAndLogEx(WARNING, "Could not open file " _YELLOW_("%s"), filename);
         return 1;
     }
 
-    snprintf(progress_text, 80, "Reading nonces from file %s...", filename);
+    snprintf(progress_text, 80, "Reading nonces from file " _YELLOW_("%s"), filename);
     hardnested_print_progress(0, progress_text, (float)(1LL << 47), 0);
     size_t bytes_read = fread(read_buf, 1, 6, fnonces);
     if (bytes_read != 6) {
@@ -1151,7 +1145,7 @@ __attribute__((force_align_arg_pointer))
             uint16_t bitflip = all_effective_bitflip[bitflip_idx];
             if (time_budget && timeout()) {
 #if defined (DEBUG_REDUCTION)
-                PrintAndLogEx(NORMAL, "break at bitflip_idx %d...", bitflip_idx);
+                PrintAndLogEx(INFO, "break at bitflip_idx " _YELLOW_("%d") " ...", bitflip_idx);
 #endif
                 return NULL;
             }
@@ -1176,7 +1170,7 @@ __attribute__((force_align_arg_pointer))
                                 if (nonces[i].num_states_bitarray[odd_even] != old_count) {
                                     nonces[i].all_bitflips_dirty[odd_even] = true;
                                 }
-                                // PrintAndLogEx(NORMAL, "bitflip: %d old: %d, new: %d ", bitflip, old_count, nonces[i].num_states_bitarray[odd_even]);
+                                // PrintAndLogEx(INFO, "bitflip: %d old: %d, new: %d ", bitflip, old_count, nonces[i].num_states_bitarray[odd_even]);
                             }
                         }
                     }
@@ -1193,7 +1187,7 @@ __attribute__((force_align_arg_pointer))
             uint16_t bitflip = all_effective_bitflip[bitflip_idx];
             if (time_budget && timeout()) {
 #if defined (DEBUG_REDUCTION)
-                PrintAndLogEx(NORMAL, "break at bitflip_idx %d...", bitflip_idx);
+                PrintAndLogEx(INFO, "break at bitflip_idx " _YELLOW_("%d") " ...", bitflip_idx);
 #endif
                 return NULL;
             }
@@ -1223,8 +1217,8 @@ __attribute__((force_align_arg_pointer))
                         }
                     }
                 }
-                // PrintAndLogEx(NORMAL, "states_bitarray[0][%" PRIu16 "] contains %d ones.\n", i, count_states(nonces[i].states_bitarray[EVEN_STATE]));
-                // PrintAndLogEx(NORMAL, "states_bitarray[1][%" PRIu16 "] contains %d ones.\n", i, count_states(nonces[i].states_bitarray[ODD_STATE]));
+                // PrintAndLogEx(INFO, "states_bitarray[0][%" PRIu16 "] contains %d ones.\n", i, count_states(nonces[i].states_bitarray[EVEN_STATE]));
+                // PrintAndLogEx(INFO, "states_bitarray[1][%" PRIu16 "] contains %d ones.\n", i, count_states(nonces[i].states_bitarray[ODD_STATE]));
             }
         }
     }
@@ -1267,7 +1261,7 @@ static void check_for_BitFlipProperties(bool time_budget) {
         }
     }
 #if defined (DEBUG_REDUCTION)
-    if (hardnested_stage & CHECK_1ST_BYTES) PrintAndLogEx(NORMAL, "stage 1 not completed yet\n");
+    if (hardnested_stage & CHECK_1ST_BYTES) PrintAndLogEx(INFO, "stage 1 not completed yet\n");
 #endif
 }
 
@@ -1381,7 +1375,7 @@ static void simulate_acquire_nonces(void) {
     } while (!acquisition_completed);
 
     time_t end_time = time(NULL);
-    // PrintAndLogEx(NORMAL, "Acquired a total of %" PRId32" nonces in %1.0f seconds (%1.0f nonces/minute)",
+    // PrintAndLogEx(INFO, "Acquired a total of %" PRId32" nonces in %1.0f seconds (%1.0f nonces/minute)",
     // num_acquired_nonces,
     // difftime(end_time, time1),
     // difftime(end_time, time1)!=0.0?(float)total_num_nonces*60.0/difftime(end_time, time1):INFINITY
@@ -1393,62 +1387,64 @@ static void simulate_acquire_nonces(void) {
 
 
 static int acquire_nonces(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_t trgBlockNo, uint8_t trgKeyType, bool nonce_file_write, bool slow, char *filename) {
+
     last_sample_clock = msclock();
-    sample_period = 2000; // initial rough estimate. Will be refined.
+    hardnested_stage = CHECK_1ST_BYTES;
+    num_acquired_nonces = 0;
+
+    // initial rough estimate. Will be refined.
+    sample_period = 2000;
+
     bool initialize = true;
     bool field_off = false;
-    hardnested_stage = CHECK_1ST_BYTES;
     bool acquisition_completed = false;
-    uint8_t write_buf[9];
-    //uint32_t total_num_nonces = 0;
-    float brute_force_depth;
     bool reported_suma8 = false;
-    char progress_text[80];
+
+    float brute_force_depth;
+
     FILE *fnonces = NULL;
     PacketResponseNG resp;
-    num_acquired_nonces = 0;
 
-    clearCommandBuffer();
+    uint8_t write_buf[9];
+    char progress_text[80];
 
     do {
+
+        if (field_off) {
+            DropField();
+            break;
+        }
+
         uint32_t flags = 0;
         flags |= initialize ? 0x0001 : 0;
         flags |= slow ? 0x0002 : 0;
         flags |= field_off ? 0x0004 : 0;
-
         clearCommandBuffer();
-
-        if (field_off) {
-            SendCommandNG(CMD_FPGA_MAJOR_MODE_OFF, NULL, 0);
-            break;
-        } else {
-            SendCommandMIX(CMD_HF_MIFARE_ACQ_ENCRYPTED_NONCES, blockNo + keyType * 0x100, trgBlockNo + trgKeyType * 0x100, flags, key, 6);
-        }
+        SendCommandMIX(CMD_HF_MIFARE_ACQ_ENCRYPTED_NONCES, blockNo + keyType * 0x100, trgBlockNo + trgKeyType * 0x100, flags, key, 6);
 
         if (initialize) {
 
-            if (!WaitForResponseTimeout(CMD_ACK, &resp, 3000)) {
-                clearCommandBuffer();
-                SendCommandNG(CMD_FPGA_MAJOR_MODE_OFF, NULL, 0);
+            if (WaitForResponseTimeout(CMD_ACK, &resp, 3000) == false) {
+                DropField();
                 return 1;
             }
 
             // error during nested_hard
             if (resp.oldarg[0]) {
-                clearCommandBuffer();
-                SendCommandNG(CMD_FPGA_MAJOR_MODE_OFF, NULL, 0);
+                DropField();
                 return resp.oldarg[0];
             }
 
             cuid = resp.oldarg[1];
             if (nonce_file_write && fnonces == NULL) {
+
                 if ((fnonces = fopen(filename, "wb")) == NULL) {
-                    PrintAndLogEx(WARNING, "Could not create file %s", filename);
-                    clearCommandBuffer();
-                    SendCommandNG(CMD_FPGA_MAJOR_MODE_OFF, NULL, 0);
+                    PrintAndLogEx(WARNING, "Could not create file " _YELLOW_("%s"), filename);
+                    DropField();
                     return 3;
                 }
-                snprintf(progress_text, 80, "Writing acquired nonces to binary file %s", filename);
+
+                snprintf(progress_text, 80, "Writing acquired nonces to binary file " _YELLOW_("%s"), filename);
                 hardnested_print_progress(0, progress_text, (float)(1LL << 47), 0);
                 num_to_bytes(cuid, 4, write_buf);
                 fwrite(write_buf, 1, 4, fnonces);
@@ -1458,17 +1454,19 @@ static int acquire_nonces(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_
             }
         }
 
-        if (!initialize) {
+        if (initialize == false) {
+
             uint16_t num_sampled_nonces = resp.oldarg[2];
             uint8_t *bufp = resp.data.asBytes;
+
             for (uint16_t i = 0; i < num_sampled_nonces; i += 2) {
                 uint32_t nt_enc1 = bytes_to_num(bufp, 4);
                 uint32_t nt_enc2 = bytes_to_num(bufp + 4, 4);
                 uint8_t par_enc = bytes_to_num(bufp + 8, 1);
 
-                //PrintAndLogEx(NORMAL, "Encrypted nonce: %08x, encrypted_parity: %02x\n", nt_enc1, par_enc >> 4);
+                //PrintAndLogEx(INFO, "Encrypted nonce: %08x, encrypted_parity: %02x\n", nt_enc1, par_enc >> 4);
                 num_acquired_nonces += add_nonce(nt_enc1, par_enc >> 4);
-                //PrintAndLogEx(NORMAL, "Encrypted nonce: %08x, encrypted_parity: %02x\n", nt_enc2, par_enc & 0x0f);
+                //PrintAndLogEx(INFO, "Encrypted nonce: %08x, encrypted_parity: %02x\n", nt_enc2, par_enc & 0x0f);
                 num_acquired_nonces += add_nonce(nt_enc2, par_enc & 0x0f);
 
                 if (nonce_file_write) {
@@ -1511,14 +1509,13 @@ static int acquire_nonces(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_
             field_off = true; // switch off field with next SendCommandOLD and then finish
         }
 
-        if (!initialize) {
+        if (initialize == false) {
 
-            if (!WaitForResponseTimeout(CMD_ACK, &resp, 3000)) {
+            if (WaitForResponseTimeout(CMD_ACK, &resp, 3000) == false) {
                 if (nonce_file_write) {
                     fclose(fnonces);
                 }
-                clearCommandBuffer();
-                SendCommandNG(CMD_FPGA_MAJOR_MODE_OFF, NULL, 0);
+                DropField();
                 return 1;
             }
 
@@ -1527,8 +1524,7 @@ static int acquire_nonces(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_
                 if (nonce_file_write) {
                     fclose(fnonces);
                 }
-                clearCommandBuffer();
-                SendCommandNG(CMD_FPGA_MAJOR_MODE_OFF, NULL, 0);
+                DropField();
                 return resp.oldarg[0];
             }
         }
@@ -1538,9 +1534,10 @@ static int acquire_nonces(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_
         if (msclock() - last_sample_clock < sample_period) {
             sample_period = msclock() - last_sample_clock;
         }
+
         last_sample_clock = msclock();
 
-    } while (!acquisition_completed || field_off);
+    } while (field_off || acquisition_completed == false);
 
     if (nonce_file_write) {
         fclose(fnonces);
@@ -1554,7 +1551,7 @@ static inline bool invariant_holds(uint_fast8_t byte_diff, uint_fast32_t state1,
     uint_fast8_t j_1_bit_mask = 0x01 << (bit - 1);
     uint_fast8_t bit_diff = byte_diff & j_1_bit_mask;                                               // difference of (j-1)th bit
     uint_fast8_t filter_diff = filter(state1 >> (4 - state_bit)) ^ filter(state2 >> (4 - state_bit)); // difference in filter function
-    uint_fast8_t mask_y12_y13 = 0xc0 >> state_bit;
+    uint_fast8_t mask_y12_y13 = (0xc0 >> state_bit);
     uint_fast8_t state_bits_diff = (state1 ^ state2) & mask_y12_y13;                                // difference in state bits 12 and 13
     uint_fast8_t all_diff = evenparity8(bit_diff ^ state_bits_diff ^ filter_diff);                  // use parity function to XOR all bits
     return !all_diff;
@@ -1562,9 +1559,9 @@ static inline bool invariant_holds(uint_fast8_t byte_diff, uint_fast32_t state1,
 
 
 static inline bool invalid_state(uint_fast8_t byte_diff, uint_fast32_t state1, uint_fast32_t state2, uint_fast8_t bit, uint_fast8_t state_bit) {
-    uint_fast8_t j_bit_mask = 0x01 << bit;
+    uint_fast8_t j_bit_mask = (0x01 << bit);
     uint_fast8_t bit_diff = byte_diff & j_bit_mask;                                                 // difference of jth bit
-    uint_fast8_t mask_y13_y16 = 0x48 >> state_bit;
+    uint_fast8_t mask_y13_y16 = (0x48 >> state_bit);
     uint_fast8_t state_bits_diff = (state1 ^ state2) & mask_y13_y16;                                // difference in state bits 13 and 16
     uint_fast8_t all_diff = evenparity8(bit_diff ^ state_bits_diff);                                // use parity function to XOR all bits
     return all_diff;
@@ -1672,8 +1669,8 @@ static inline bool bitflips_match(uint8_t byte, uint32_t state, odd_even_t odd_e
     if (!possible) {
 #ifdef DEBUG_KEY_ELIMINATION
         if (!quiet && known_target_key != -1 && state == test_state[odd_even]) {
-            PrintAndLogEx(INFO, "Initial state lists: %s test state eliminated by bitflip property.", odd_even == EVEN_STATE ? "even" : "odd");
-            sprintf(failstr, "Initial %s Byte Bitflip property", odd_even == EVEN_STATE ? "even" : "odd");
+            PrintAndLogEx(INFO, "Initial state lists: " _YELLOW_("%s") " test state eliminated by bitflip property.", odd_even == EVEN_STATE ? "even" : "odd");
+            sprintf(failstr, "Initial " _YELLOW_("%s") " byte Bitflip property", odd_even == EVEN_STATE ? "even" : "odd");
         }
 #endif
         return false;
@@ -1717,7 +1714,7 @@ static bool all_bitflips_match(uint8_t byte, uint32_t state, odd_even_t odd_even
 
 # ifdef DEBUG_KEY_ELIMINATION
             if (known_target_key != -1 && state == test_state[odd_even]) {
-                PrintAndLogEx(NORMAL, "all_bitflips_match() 1st Byte: %s test state (0x%06x): Eliminated. Bytes = %02x, %02x, Common Bits = %d\n",
+                PrintAndLogEx(INFO, "all_bitflips_match() 1st Byte: %s test state (0x%06x): Eliminated. Bytes = %02x, %02x, Common Bits = %d\n",
                               odd_even == ODD_STATE ? "odd" : "even",
                               test_state[odd_even],
                               byte,
@@ -1750,17 +1747,19 @@ static void bitarray_to_list(uint8_t byte, uint32_t *bitarray, uint32_t *state_l
 static void add_cached_states(statelist_t *cands, uint16_t part_sum_a0, uint16_t part_sum_a8, odd_even_t odd_even) {
     cands->states[odd_even] = sl_cache[part_sum_a0 / 2][part_sum_a8 / 2][odd_even].sl;
     cands->len[odd_even] = sl_cache[part_sum_a0 / 2][part_sum_a8 / 2][odd_even].len;
-    return;
 }
 
 
 static void add_matching_states(statelist_t *cands, uint8_t part_sum_a0, uint8_t part_sum_a8, odd_even_t odd_even) {
+
     const uint32_t worstcase_size = 1 << 20;
+
     cands->states[odd_even] = (uint32_t *)malloc(sizeof(uint32_t) * worstcase_size);
     if (cands->states[odd_even] == NULL) {
         PrintAndLogEx(ERR, "Out of memory error in add_matching_states() - statelist.\n");
         exit(4);
     }
+
     uint32_t *cands_bitarray = (uint32_t *)malloc_bitarray(sizeof(uint32_t) * worstcase_size);
     if (cands_bitarray == NULL) {
         PrintAndLogEx(ERR, "Out of memory error in add_matching_states() - bitarray.\n");
@@ -1909,7 +1908,7 @@ __attribute__((force_align_arg_pointer))
         for (uint8_t p = 0; p < NUM_PART_SUMS; p++) {
             for (uint8_t q = 0; q < NUM_PART_SUMS; q++) {
                 if (2 * p * (16 - 2 * q) + (16 - 2 * p) * 2 * q == sum_a0) {
-                    // PrintAndLogEx(NORMAL, "Reducing Partial Statelists (p,q) = (%d,%d) with lengths %d, %d\n",
+                    // PrintAndLogEx(INFO, "Reducing Partial Statelists (p,q) = (%d,%d) with lengths %d, %d",
                     // p, q, partial_statelist[p].len[ODD_STATE], partial_statelist[q].len[EVEN_STATE]);
                     for (uint8_t r = 0; r < NUM_PART_SUMS; r++) {
                         for (uint8_t s = 0; s < NUM_PART_SUMS; s++) {
@@ -1966,20 +1965,20 @@ __attribute__((force_align_arg_pointer))
                                     }
                                 }
 
-                                if (!work_required) {
+                                if (work_required == false) {
                                     pthread_mutex_unlock(&statelist_cache_mutex);
                                     pthread_mutex_unlock(&book_of_work_mutex);
                                 } else {
                                     // we really need to calculate something
                                     if (even_completed) { // we had one cache hit with non-zero even states
-                                        // PrintAndLogEx(NORMAL, "Thread #%u: start working on  odd states p=%2d, r=%2d...\n", my_thread_number, p, r);
+                                        // PrintAndLogEx(INFO, "Thread #%u: start working on  odd states p=%2d, r=%2d...", my_thread_number, p, r);
                                         sl_cache[p][r][ODD_STATE].cache_status = WORK_IN_PROGRESS;
                                         pthread_mutex_unlock(&statelist_cache_mutex);
                                         pthread_mutex_unlock(&book_of_work_mutex);
                                         add_matching_states(current_candidates, 2 * p, 2 * r, ODD_STATE);
                                         work_required = false;
                                     } else if (odd_completed) { // we had one cache hit with non-zero odd_states
-                                        // PrintAndLogEx(NORMAL, "Thread #%u: start working on even states q=%2d, s=%2d...\n", my_thread_number, q, s);
+                                        // PrintAndLogEx(INFO, "Thread #%u: start working on even states q=%2d, s=%2d...", my_thread_number, q, s);
                                         sl_cache[q][s][EVEN_STATE].cache_status = WORK_IN_PROGRESS;
                                         pthread_mutex_unlock(&statelist_cache_mutex);
                                         pthread_mutex_unlock(&book_of_work_mutex);
@@ -1996,7 +1995,7 @@ __attribute__((force_align_arg_pointer))
 
                                     add_matching_states(current_candidates, 2 * p, 2 * r, ODD_STATE);
                                     if (current_candidates->len[ODD_STATE]) {
-                                        // PrintAndLogEx(NORMAL, "Thread #%u: start working on even states q=%2d, s=%2d...\n", my_thread_number, q, s);
+                                        // PrintAndLogEx(INFO, "Thread #%u: start working on even states q=%2d, s=%2d...", my_thread_number, q, s);
                                         add_matching_states(current_candidates, 2 * q, 2 * s, EVEN_STATE);
                                     } else { // no need to calculate even states yet
                                         pthread_mutex_lock(&statelist_cache_mutex);
@@ -2013,16 +2012,16 @@ __attribute__((force_align_arg_pointer))
                                 pthread_mutex_unlock(&book_of_work_mutex);
 
                                 // if ((uint64_t)current_candidates->len[ODD_STATE] * current_candidates->len[EVEN_STATE]) {
-                                // PrintAndLogEx(NORMAL, "Candidates for p=%2u, q=%2u, r=%2u, s=%2u: %" PRIu32 " * %" PRIu32 " = %" PRIu64 " (2^%0.1f)\n",
+                                // PrintAndLogEx(INFO, "Candidates for p=%2u, q=%2u, r=%2u, s=%2u: %" PRIu32 " * %" PRIu32 " = %" PRIu64 " (2^%0.1f)\n",
                                 // 2*p, 2*q, 2*r, 2*s, current_candidates->len[ODD_STATE], current_candidates->len[EVEN_STATE],
                                 // (uint64_t)current_candidates->len[ODD_STATE] * current_candidates->len[EVEN_STATE],
                                 // log((uint64_t)current_candidates->len[ODD_STATE] * current_candidates->len[EVEN_STATE])/log(2));
                                 // uint32_t estimated_odd = estimated_num_states_part_sum(best_first_bytes[0], p, r, ODD_STATE);
                                 // uint32_t estimated_even= estimated_num_states_part_sum(best_first_bytes[0], q, s, EVEN_STATE);
                                 // uint64_t estimated_total = (uint64_t)estimated_odd * estimated_even;
-                                // PrintAndLogEx(NORMAL, "Estimated: %" PRIu32 " * %" PRIu32 " = %" PRIu64 " (2^%0.1f)\n", estimated_odd, estimated_even, estimated_total, log(estimated_total) / log(2));
+                                // PrintAndLogEx(INFO, "Estimated: %" PRIu32 " * %" PRIu32 " = %" PRIu64 " (2^%0.1f)\n", estimated_odd, estimated_even, estimated_total, log(estimated_total) / log(2));
                                 // if (estimated_odd < current_candidates->len[ODD_STATE] || estimated_even < current_candidates->len[EVEN_STATE]) {
-                                // PrintAndLogEx(NORMAL, "############################################################################ERROR! ESTIMATED < REAL !!!\n");
+                                // PrintAndLogEx(INFO, "############################################################################ERROR! ESTIMATED < REAL !!!\n");
                                 // //exit(2);
                                 // }
                                 // }
@@ -2127,7 +2126,7 @@ static void Tests(void) {
     for (odd_even_t odd_even = EVEN_STATE; odd_even <= ODD_STATE; odd_even++) {
         uint32_t *bitset = nonces[best_first_bytes[0]].states_bitarray[odd_even];
         if (!test_bit24(bitset, test_state[odd_even])) {
-            PrintAndLogEx(NORMAL, "\nBUG: known target key's %s state is not member of first nonce byte's (0x%02x) states_bitarray!\n",
+            PrintAndLogEx(WARNING, "BUG: known target key's " _YELLOW_("%s") " state is not member of first nonce byte's ( 0x%02x ) states_bitarray!",
                           odd_even == EVEN_STATE ? "even" : "odd ",
                           best_first_bytes[0]);
         }
@@ -2135,7 +2134,7 @@ static void Tests(void) {
     for (odd_even_t odd_even = EVEN_STATE; odd_even <= ODD_STATE; odd_even++) {
         uint32_t *bitset = all_bitflips_bitarray[odd_even];
         if (!test_bit24(bitset, test_state[odd_even])) {
-            PrintAndLogEx(NORMAL, "\nBUG: known target key's %s state is not member of all_bitflips_bitarray!\n",
+            PrintAndLogEx(WARNING, "BUG: known target key's " _YELLOW_("%s") " state is not member of all_bitflips_bitarray!",
                           odd_even == EVEN_STATE ? "even" : "odd ");
         }
     }
@@ -2149,7 +2148,7 @@ static void Tests2(void) {
     for (odd_even_t odd_even = EVEN_STATE; odd_even <= ODD_STATE; odd_even++) {
         uint32_t *bitset = nonces[best_first_byte_smallest_bitarray].states_bitarray[odd_even];
         if (!test_bit24(bitset, test_state[odd_even])) {
-            PrintAndLogEx(NORMAL, "\nBUG: known target key's %s state is not member of first nonce byte's (0x%02x) states_bitarray!\n",
+            PrintAndLogEx(WARNING, "BUG: known target key's " _YELLOW_("%s") " state is not member of first nonce byte's ( 0x%02x ) states_bitarray!",
                           odd_even == EVEN_STATE ? "even" : "odd ",
                           best_first_byte_smallest_bitarray);
         }
@@ -2158,7 +2157,7 @@ static void Tests2(void) {
     for (odd_even_t odd_even = EVEN_STATE; odd_even <= ODD_STATE; odd_even++) {
         uint32_t *bitset = all_bitflips_bitarray[odd_even];
         if (!test_bit24(bitset, test_state[odd_even])) {
-            PrintAndLogEx(NORMAL, "\nBUG: known target key's %s state is not member of all_bitflips_bitarray!\n",
+            PrintAndLogEx(WARNING, "BUG: known target key's " _YELLOW_("%s") " state is not member of all_bitflips_bitarray!",
                           odd_even == EVEN_STATE ? "even" : "odd ");
         }
     }
@@ -2221,7 +2220,7 @@ int mfnestedhard(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_t trgBloc
     char instr_set[12] = {0};
 
     get_SIMD_instruction_set(instr_set);
-    PrintAndLogEx(SUCCESS, "Using %s SIMD core.", instr_set);
+    PrintAndLogEx(SUCCESS, "Using " _GREEN_("%s") " SIMD core.", instr_set);
 
     // initialize static arrays
     memset(part_sum_count, 0, sizeof(part_sum_count));
@@ -2236,7 +2235,7 @@ int mfnestedhard(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_t trgBloc
         write_stats = true;
         setlocale(LC_NUMERIC, "");
         if ((fstats = fopen("hardnested_stats.txt", "a")) == NULL) {
-            PrintAndLogEx(WARNING, "Could not create/open file hardnested_stats.txt");
+            PrintAndLogEx(WARNING, "Could not create/open file " _YELLOW_("hardnested_stats.txt"));
             return 3;
         }
 
