commit 61c9252e15cef7f2f3951bac7fbeb0572d153c5c
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jan 9 00:35:43 2022 +0100

    calloc

diff --git a/client/deps/hardnested/hardnested_bf_core.c b/client/deps/hardnested/hardnested_bf_core.c
index 0e7eabaf5..76beb9873 100644
--- a/client/deps/hardnested/hardnested_bf_core.c
+++ b/client/deps/hardnested/hardnested_bf_core.c
@@ -238,7 +238,7 @@ uint64_t CRACK_STATES_BITSLICED(uint32_t cuid, uint8_t *best_first_bytes, statel
     memset(bs_zeroes.bytes, 0x00, VECTOR_SIZE);
 
     // bitslice all the even states
-    bitslice_t **restrict bitsliced_even_states = (bitslice_t **)malloc(((p->len[EVEN_STATE] - 1) / MAX_BITSLICES + 1) * sizeof(bitslice_t *));
+    bitslice_t **restrict bitsliced_even_states = (bitslice_t **)calloc(1, ((p->len[EVEN_STATE] - 1) / MAX_BITSLICES + 1) * sizeof(bitslice_t *));
     if (bitsliced_even_states == NULL) {
         PrintAndLogEx(WARNING, "Out of memory error in brute_force. Aborting...");
         exit(4);
diff --git a/client/deps/hardnested/hardnested_bruteforce.c b/client/deps/hardnested/hardnested_bruteforce.c
index 71ff09144..2516f150e 100644
--- a/client/deps/hardnested/hardnested_bruteforce.c
+++ b/client/deps/hardnested/hardnested_bruteforce.c
@@ -436,8 +436,8 @@ static bool read_bench_data(statelist_t *test_candidates) {
 float brute_force_benchmark(void) {
     statelist_t test_candidates[NUM_BRUTE_FORCE_THREADS];
 
-    test_candidates[0].states[ODD_STATE] = malloc((TEST_BENCH_SIZE + 1) * sizeof(uint32_t));
-    test_candidates[0].states[EVEN_STATE] = malloc((TEST_BENCH_SIZE + 1) * sizeof(uint32_t));
+    test_candidates[0].states[ODD_STATE] = calloc(1, (TEST_BENCH_SIZE + 1) * sizeof(uint32_t));
+    test_candidates[0].states[EVEN_STATE] = calloc(1, (TEST_BENCH_SIZE + 1) * sizeof(uint32_t));
     for (uint8_t i = 0; i < NUM_BRUTE_FORCE_THREADS - 1; i++) {
         test_candidates[i].next = test_candidates + i + 1;
         test_candidates[i + 1].states[ODD_STATE] = test_candidates[0].states[ODD_STATE];
