commit b822e0e046dcf2de7fd298aca2bc94a96e37ae6e
Author: iceman1001 <iceman@iuse.se>
Date:   Wed May 19 10:15:45 2021 +0200

    fix coverity CID 322764

diff --git a/tools/mf_nonce_brute/mf_trace_brute.c b/tools/mf_nonce_brute/mf_trace_brute.c
index 18b9c67bd..422465c32 100644
--- a/tools/mf_nonce_brute/mf_trace_brute.c
+++ b/tools/mf_nonce_brute/mf_trace_brute.c
@@ -58,7 +58,7 @@ uint8_t cmds[8][2] = {
 };
 
 static int global_found = 0;
-static size_t thread_count = 2;
+static int thread_count = 2;
 
 static int param_getptr(const char *line, int *bg, int *en, int paramnum) {
     int i;
@@ -279,7 +279,7 @@ int main(int argc, char *argv[]) {
         thread_count = 2;
 #endif  /* _WIN32 */
 
-    printf("\nBruteforce using %zu threads to find upper 16bits of key\n", thread_count);
+    printf("\nBruteforce using %d threads to find upper 16bits of key\n", thread_count);
 
     pthread_t threads[thread_count];
 
@@ -288,7 +288,7 @@ int main(int argc, char *argv[]) {
 
     // threads
     for (int i = 0; i < thread_count; ++i) {
-        struct thread_args *a = malloc(sizeof(struct thread_args));
+        struct thread_args *a = calloc(1, sizeof(struct thread_args));
         a->thread = i;
         a->idx = i;
         a->uid = uid;
