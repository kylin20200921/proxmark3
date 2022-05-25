commit c56d3dd42e1e0679a70243c404f9d06029143048
Author: iceman1001 <iceman@iuse.se>
Date:   Sun May 16 23:04:48 2021 +0200

    remove unused var

diff --git a/tools/mf_nonce_brute/mf_trace_brute.c b/tools/mf_nonce_brute/mf_trace_brute.c
index d5ceb6d3a..67451acf8 100644
--- a/tools/mf_nonce_brute/mf_trace_brute.c
+++ b/tools/mf_nonce_brute/mf_trace_brute.c
@@ -58,7 +58,6 @@ uint8_t cmds[8][2] = {
 };
 
 static int global_found = 0;
-static int global_found_candidate = 0;
 static size_t thread_count = 2;
 
 static int param_getptr(const char *line, int *bg, int *en, int paramnum) {
@@ -305,13 +304,13 @@ int main(int argc, char *argv[]) {
     for (int i = 0; i < thread_count; ++i)
         pthread_join(threads[i], NULL);
 
-    if (!global_found && !global_found_candidate) {
+    if (global_found == false) {
         printf("\nFailed to find a key\n\n");
     }
 
     t1 = msclock() - t1;
     if (t1 > 0)
-        printf("Execution time: %.0f ticks\n", (float)t1);
+        printf("execution time " _YELLOW_("%.2f") " sec\n", (float)t1 / 1000.0);
 
     // clean up mutex
     pthread_mutex_destroy(&print_lock);
