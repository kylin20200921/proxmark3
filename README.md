commit dfd07ccd698f9620e9cc5e2b7e73f59db3ad63d8
Author: iceman1001 <iceman@iuse.se>
Date:   Wed May 19 10:15:10 2021 +0200

    fix coverity CID 322762

diff --git a/tools/mf_nonce_brute/mf_nonce_brute.c b/tools/mf_nonce_brute/mf_nonce_brute.c
index 401410f1e..a3bc14f95 100644
--- a/tools/mf_nonce_brute/mf_nonce_brute.c
+++ b/tools/mf_nonce_brute/mf_nonce_brute.c
@@ -72,7 +72,7 @@ uint8_t cmds[8][2] = {
 static int global_found = 0;
 static int global_found_candidate = 0;
 static uint64_t global_candiate_key = 0;
-static size_t thread_count = 2;
+static int thread_count = 2;
 
 static int param_getptr(const char *line, int *bg, int *en, int paramnum) {
     int i;
@@ -384,13 +384,12 @@ static void *brute_thread(void *arguments) {
 
         nt = count << 16 | prng_successor(count, 16);
 
-        if (!candidate_nonce(args->xored, nt, args->ev1))
+        if (candidate_nonce(args->xored, nt, args->ev1) == false)
             continue;
 
         p64 = prng_successor(nt, 64);
         ks2 = ar_enc ^ p64;
         ks3 = at_enc ^ prng_successor(p64, 32);
-        free(revstate);
         revstate = lfsr_recovery64(ks2, ks3);
         ks4 = crypto1_word(revstate, 0, 0);
 
@@ -415,14 +414,17 @@ static void *brute_thread(void *arguments) {
 
                 // check if cmd exists
                 uint8_t isOK = checkValidCmd(decrypted);
-                (void)isOK;
+                if (isOK == false) {
+                    printf(_RED_("<-- not a valid cmd\n"));
+                    pthread_mutex_unlock(&print_lock);
+                    continue;
+                }
 
                 // Add a crc-check.
                 isOK = checkCRC(decrypted);
                 if (isOK == false) {
-                    printf(_RED_("<-- not a valid cmd\n"));
+                    printf(_RED_("<-- not a valid crc\n"));
                     pthread_mutex_unlock(&print_lock);
-                    free(revstate);
                     continue;
                 } else {
                     printf("<-- valid cmd\n");
@@ -450,6 +452,7 @@ static void *brute_thread(void *arguments) {
             free(revstate);
             break;
         }
+        free(revstate);
     }
     free(args);
     return NULL;
@@ -468,7 +471,7 @@ static void *brute_key_thread(void *arguments) {
             break;
         }
 
-        key |= count << 32;
+        key |= (count << 32);
 
         // Init cipher with key
         struct Crypto1State *pcs = crypto1_create(key);
@@ -576,7 +579,7 @@ int main(int argc, char *argv[]) {
         thread_count = 2;
 #endif  /* _WIN32 */
 
-    printf("\nBruteforce using " _YELLOW_("%zu") " threads\n", thread_count);
+    printf("\nBruteforce using " _YELLOW_("%d") " threads\n", thread_count);
     printf("looking for the last bytes of the encrypted tagnonce\n");
 
     pthread_t threads[thread_count];
@@ -585,7 +588,7 @@ int main(int argc, char *argv[]) {
     pthread_mutex_init(&print_lock, NULL);
 
     // one thread T0 for none EV1.
-    struct thread_args *a = malloc(sizeof(struct thread_args));
+    struct thread_args *a = calloc(1, sizeof(struct thread_args));
     a->xored = xored;
     a->thread = 0;
     a->idx = 0;
@@ -594,7 +597,7 @@ int main(int argc, char *argv[]) {
 
     // the rest of available threads to EV1 scenario
     for (int i = 0; i < thread_count - 1; ++i) {
-        struct thread_args *b = malloc(sizeof(struct thread_args));
+        struct thread_args *b = calloc(1, sizeof(struct thread_args));
         b->xored = xored;
         b->thread = i + 1;
         b->idx = i;
