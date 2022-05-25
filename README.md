commit 7c8be44dd68630c62a7cf5848a798a947ed58e39
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jan 9 13:38:16 2022 +0100

    cppcheck stuff

diff --git a/tools/hitag2crack/crack5opencl/threads.c b/tools/hitag2crack/crack5opencl/threads.c
index 3416d6858..50c4c7762 100644
--- a/tools/hitag2crack/crack5opencl/threads.c
+++ b/tools/hitag2crack/crack5opencl/threads.c
@@ -248,7 +248,9 @@ int thread_start_scheduler(thread_ctx_t *ctx, thread_args_t *t_arg, wu_queue_ctx
                         pthread_mutex_lock(&ctx->thread_mutexs[z]);
                         t_arg[z].status = TH_END;
                         t_arg[z].quit = true;
-                        if (cur_status == TH_WAIT) pthread_cond_signal(&ctx->thread_conds[z]);
+                        if (cur_status == TH_WAIT) {
+                            pthread_cond_signal(&ctx->thread_conds[z]);
+                        }
                         pthread_mutex_unlock(&ctx->thread_mutexs[z]);
                     } else {
                         if (ctx->thread_count == 1) {
@@ -306,7 +308,9 @@ int thread_start_scheduler(thread_ctx_t *ctx, thread_args_t *t_arg, wu_queue_ctx
                         printf("[master] thread [%zu], got the signal with new state: %s.\n", z, thread_status_strdesc(t_arg[z].status));
                         fflush(stdout);
 #endif
-                        if (t_arg[z].status == TH_FOUND_KEY) found = true;
+                        if (t_arg[z].status == TH_FOUND_KEY) {
+                            found = true;
+                        }
 
                         pthread_mutex_unlock(&ctx->thread_mutex_usleep);
 #if TDEBUG >= 1
@@ -316,17 +320,18 @@ int thread_start_scheduler(thread_ctx_t *ctx, thread_args_t *t_arg, wu_queue_ctx
                         continue;
                     }
 
-                    if (found) {
+                    // since found is handled before this part (line 237), removing the if-statement
+                    // doesn't change anything according to cppcheck.
 #if TDEBUG >= 1
-                        printf("[master] thread [%zu], the key is found. set TH_END from TH_PROCESSING\n", z);
-                        fflush(stdout);
+                    printf("[master] thread [%zu], the key is found. set TH_END from TH_PROCESSING\n", z);
+                    fflush(stdout);
 #endif
-                        pthread_mutex_lock(&ctx->thread_mutexs[z]);
-                        t_arg[z].status = TH_END;
-                        t_arg[z].quit = true;
-                        pthread_mutex_unlock(&ctx->thread_mutexs[z]);
-                        continue;
-                    }
+                    pthread_mutex_lock(&ctx->thread_mutexs[z]);
+                    t_arg[z].status = TH_END;
+                    t_arg[z].quit = true;
+                    pthread_mutex_unlock(&ctx->thread_mutexs[z]);
+                    continue;
+
                 }
                 if (cur_status == TH_ERROR) {
                     // something went wrong
