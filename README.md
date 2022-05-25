commit 65e4d8e1fa8d481e91e8b57b58eac4dc49f7b017
Author: iceman1001 <iceman@iuse.se>
Date:   Sat May 15 10:43:51 2021 +0200

    fix thread and clock issues

diff --git a/tools/mf_nonce_brute/Makefile b/tools/mf_nonce_brute/Makefile
index 17bff27cc..17ae04ddc 100644
--- a/tools/mf_nonce_brute/Makefile
+++ b/tools/mf_nonce_brute/Makefile
@@ -1,5 +1,5 @@
 MYSRCPATHS = ../../common ../../common/crapto1
-MYSRCS = crypto1.c crapto1.c bucketsort.c iso14443crc.c sleep.c
+MYSRCS = crypto1.c crapto1.c bucketsort.c iso14443crc.c sleep.c util_posix.c
 MYINCLUDES = -I../../include -I../../common
 MYCFLAGS =
 MYDEFS =
diff --git a/tools/mf_nonce_brute/mf_nonce_brute.c b/tools/mf_nonce_brute/mf_nonce_brute.c
index e2bdff892..401410f1e 100644
--- a/tools/mf_nonce_brute/mf_nonce_brute.c
+++ b/tools/mf_nonce_brute/mf_nonce_brute.c
@@ -1,11 +1,5 @@
 #define __STDC_FORMAT_MACROS
 
-#if !defined(_WIN64)
-#if defined(_WIN32) || defined(__WIN32__)
-# define _USE_32BIT_TIME_T 1
-#endif
-#endif
-
 #include <inttypes.h>
 #include <stdio.h>
 #include <stdbool.h>
@@ -13,11 +7,11 @@
 #include <pthread.h>
 #include <stdlib.h>
 #include <unistd.h>
-#include <time.h>
 #include <ctype.h>
 #include "crapto1/crapto1.h"
 #include "protocol.h"
 #include "iso14443crc.h"
+#include "util_posix.h"
 
 #define AEND  "\x1b[0m"
 #define _RED_(s) "\x1b[31m" s AEND
@@ -74,11 +68,11 @@ uint8_t cmds[8][2] = {
     {MIFARE_CMD_TRANSFER, 0}
 };
 
-int global_counter = 0;
-int global_found = 0;
-int global_found_candidate = 0;
-uint64_t global_candiate_key = 0;
-size_t thread_count = 2;
+//static int global_counter = 0;
+static int global_found = 0;
+static int global_found_candidate = 0;
+static uint64_t global_candiate_key = 0;
+static size_t thread_count = 2;
 
 static int param_getptr(const char *line, int *bg, int *en, int paramnum) {
     int i;
@@ -396,6 +390,7 @@ static void *brute_thread(void *arguments) {
         p64 = prng_successor(nt, 64);
         ks2 = ar_enc ^ p64;
         ks3 = at_enc ^ prng_successor(p64, 32);
+        free(revstate);
         revstate = lfsr_recovery64(ks2, ks3);
         ks4 = crypto1_word(revstate, 0, 0);
 
@@ -440,7 +435,6 @@ static void *brute_thread(void *arguments) {
             lfsr_rollback_word(revstate, nr_enc, 1);
             lfsr_rollback_word(revstate, uid ^ nt, 0);
             crypto1_get_lfsr(revstate, &key);
-            free(revstate);
 
             if (args->ev1) {
                 // if it was EV1,  we know for sure xxxAAAAAAAA recovery
@@ -450,9 +444,10 @@ static void *brute_thread(void *arguments) {
                 printf("\nKey candidate [ " _GREEN_("....%08" PRIx64) " ]\n\n", key & 0xFFFFFFFF);
                 __sync_fetch_and_add(&global_found, 1);
             }
-            __sync_fetch_and_add(&global_candiate_key, key);
             //release lock
             pthread_mutex_unlock(&print_lock);
+            __sync_fetch_and_add(&global_candiate_key, key);
+            free(revstate);
             break;
         }
     }
@@ -463,7 +458,7 @@ static void *brute_thread(void *arguments) {
 static void *brute_key_thread(void *arguments) {
 
     struct thread_key_args *args = (struct thread_key_args *) arguments;
-    uint64_t key;
+    uint64_t key = args->part_key;
     uint8_t local_enc[args->enc_len];
     memcpy(local_enc, args->enc, args->enc_len);
 
@@ -473,7 +468,7 @@ static void *brute_key_thread(void *arguments) {
             break;
         }
 
-        key = (count << 32 | args->part_key);
+        key |= count << 32;
 
         // Init cipher with key
         struct Crypto1State *pcs = crypto1_create(key);
@@ -489,13 +484,13 @@ static void *brute_key_thread(void *arguments) {
         for (int i = 0; i < args->enc_len; i++)
             dec[i] = crypto1_byte(pcs, 0x00, 0) ^ local_enc[i];
 
-        crypto1_deinit(pcs);
+        crypto1_destroy(pcs);
 
         // check if cmd exists
-        uint8_t isOK = checkValidCmdByte(dec, args->enc_len);
-        if (isOK == false) {
+        if (checkValidCmdByte(dec, args->enc_len) == false) {
             continue;
         }
+        __sync_fetch_and_add(&global_found, 1);
 
         // lock this section to avoid interlacing prints from different threats
         pthread_mutex_lock(&print_lock);
@@ -503,7 +498,6 @@ static void *brute_key_thread(void *arguments) {
         printf("dec:  %s\n", sprint_hex_inrow_ex(dec, args->enc_len, 0));
         printf("\nValid Key found [ " _GREEN_("%012" PRIx64) " ]\n\n", key);
         pthread_mutex_unlock(&print_lock);
-        __sync_fetch_and_add(&global_found, 1);
         break;
     }
     free(args);
@@ -568,7 +562,7 @@ int main(int argc, char *argv[]) {
         printf("next encrypted cmd... %s\n", sprint_hex_inrow_ex(enc, enc_len, 0));
     }
 
-    clock_t t1 = clock();
+    uint64_t t1 = msclock();
     uint16_t nt_par = parity_from_err(nt_enc, nt_par_err);
     uint16_t ar_par = parity_from_err(ar_enc, ar_par_err);
     uint16_t at_par = parity_from_err(at_enc, at_par_err);
@@ -612,8 +606,8 @@ int main(int argc, char *argv[]) {
     for (int i = 0; i < thread_count; ++i)
         pthread_join(threads[i], NULL);
 
-    t1 = clock() - t1;
-    printf("execution time %.2f sec\n", (float)t1 / 1000000.0);
+    t1 = msclock() - t1;
+    printf("execution time " _YELLOW_("%.2f") " sec\n", (float)t1 / 1000.0);
 
 
     if (!global_found && !global_found_candidate) {
@@ -631,11 +625,11 @@ int main(int argc, char *argv[]) {
     global_found_candidate = 0;
 
     printf("\n----------- " _CYAN_("Phase 2") " ------------------------\n");
-    printf("uid.......... %08x\n", uid);
-    printf("partial key.. %08x\n", (uint32_t)(global_candiate_key & 0xFFFFFFFF));
-    printf("nt enc....... %08x\n", nt_enc);
-    printf("nr enc....... %08x\n", nr_enc);
-    printf("next encrypted cmd: %s\n", sprint_hex_inrow_ex(enc, enc_len, 0));
+    printf("uid.................. %08x\n", uid);
+    printf("partial key.......... %08x\n", (uint32_t)(global_candiate_key & 0xFFFFFFFF));
+    printf("nt enc............... %08x\n", nt_enc);
+    printf("nr enc............... %08x\n", nr_enc);
+    printf("next encrypted cmd... %s\n", sprint_hex_inrow_ex(enc, enc_len, 0));
     printf("\nlooking for the upper 16 bits of key\n");
     fflush(stdout);
 
diff --git a/tools/mf_nonce_brute/mf_trace_brute.c b/tools/mf_nonce_brute/mf_trace_brute.c
index 0217bcdee..d5ceb6d3a 100644
--- a/tools/mf_nonce_brute/mf_trace_brute.c
+++ b/tools/mf_nonce_brute/mf_trace_brute.c
@@ -9,12 +9,6 @@
 
 #define __STDC_FORMAT_MACROS
 
-#if !defined(_WIN64)
-#if defined(_WIN32) || defined(__WIN32__)
-# define _USE_32BIT_TIME_T 1
-#endif
-#endif
-
 #include <inttypes.h>
 #include <stdio.h>
 #include <stdbool.h>
@@ -23,10 +17,16 @@
 #include <stdlib.h>
 #include <unistd.h>
 #include "ctype.h"
-#include <time.h>
 #include "crapto1/crapto1.h"
 #include "protocol.h"
 #include "iso14443crc.h"
+#include <util_posix.h>
+
+#define AEND  "\x1b[0m"
+#define _RED_(s) "\x1b[31m" s AEND
+#define _GREEN_(s) "\x1b[32m" s AEND
+#define _YELLOW_(s) "\x1b[33m" s AEND
+#define _CYAN_(s) "\x1b[36m" s AEND
 
 // a global mutex to prevent interlaced printing from different threads
 pthread_mutex_t print_lock;
@@ -41,26 +41,25 @@ typedef struct thread_args {
     uint32_t part_key;
     uint32_t nt_enc;
     uint32_t nr_enc;
+    uint16_t enc_len;
     uint8_t enc[ENC_LEN];  // next encrypted command + a full read/write
 } targs;
 
 //------------------------------------------------------------------
-uint8_t cmds[] = {
-    ISO14443A_CMD_READBLOCK,
-    ISO14443A_CMD_WRITEBLOCK,
-    MIFARE_AUTH_KEYA,
-    MIFARE_AUTH_KEYB,
-    MIFARE_CMD_INC,
-    MIFARE_CMD_DEC,
-    MIFARE_CMD_RESTORE,
-    MIFARE_CMD_TRANSFER
+uint8_t cmds[8][2] = {
+    {ISO14443A_CMD_READBLOCK, 18},
+    {ISO14443A_CMD_WRITEBLOCK, 18},
+    {MIFARE_AUTH_KEYA, 0},
+    {MIFARE_AUTH_KEYB, 0},
+    {MIFARE_CMD_INC, 6},
+    {MIFARE_CMD_DEC, 6},
+    {MIFARE_CMD_RESTORE, 6},
+    {MIFARE_CMD_TRANSFER, 0}
 };
 
-int global_counter = 0;
-int global_fin_flag = 0;
-int global_found = 0;
-int global_found_candidate = 0;
-size_t thread_count = 2;
+static int global_found = 0;
+static int global_found_candidate = 0;
+static size_t thread_count = 2;
 
 static int param_getptr(const char *line, int *bg, int *en, int paramnum) {
     int i;
@@ -174,67 +173,71 @@ static char *sprint_hex_inrow_ex(const uint8_t *data, const size_t len, const si
     return buf;
 }
 
-static void *brute_thread(void *arguments) {
+static bool checkValidCmdByte(uint8_t *cmd, uint16_t n) {
 
-    //int shift = (int)arg;
-    struct thread_args *args = (struct thread_args *) arguments;
+    bool ok = false;
+    for (int i = 0; i < 8; ++i) {
+        if (cmd[0] == cmds[i][0]) {
+
+            if (n >= 4)
+                ok = CheckCrc14443(CRC_14443_A, cmd, 4);
+
+            if (cmds[i][1] > 0 && n >= cmds[i][1])
+                ok = CheckCrc14443(CRC_14443_A, cmd + 4, cmds[i][1]);
+
+            if (ok) {
+                return true;
+            }
+        }
+    }
+    return false;
+}
 
-    uint64_t key;     // recovered key candidate
-    int found = 0;
-    struct Crypto1State mpcs = {0, 0};
-    struct Crypto1State *pcs = &mpcs;
+static void *brute_thread(void *arguments) {
 
-    uint8_t local_enc[ENC_LEN] = {0};
-    memcpy(local_enc, args->enc, sizeof(local_enc));
+    struct thread_args *args = (struct thread_args *) arguments;
+    uint64_t key = args->part_key; 
+    uint8_t local_enc[args->enc_len];
+    memcpy(local_enc, args->enc, args->enc_len);
 
     for (uint64_t count = args->idx; count < 0xFFFF; count += thread_count) {
 
-        found = global_found;
-        if (found) {
+        if (__atomic_load_n(&global_found, __ATOMIC_ACQUIRE) == 1) {
             break;
         }
 
-        key = (count << 32 | args->part_key);
+        key |= count << 32;
 
         // Init cipher with key
-        pcs = crypto1_create(key);
+        struct Crypto1State *pcs = crypto1_create(key);
 
         // NESTED decrypt nt with help of new key
-//        if (args->use_nested)
-//            crypto1_word(pcs, args->nt_enc ^ args->uid, 1) ^ args->nt_enc;
-//        else
         crypto1_word(pcs, args->nt_enc ^ args->uid, 1);
-
         crypto1_word(pcs, args->nr_enc, 1);
         crypto1_word(pcs, 0, 0);
         crypto1_word(pcs, 0, 0);
 
         // decrypt 22 bytes
-        uint8_t dec[ENC_LEN] = {0};
-        for (int i = 0; i < ENC_LEN; i++)
+        uint8_t dec[args->enc_len];
+        for (int i = 0; i < args->enc_len; i++)
             dec[i] = crypto1_byte(pcs, 0x00, 0) ^ local_enc[i];
 
-        crypto1_deinit(pcs);
-
-        if (CheckCrc14443(CRC_14443_A, dec, 4)) {
-
-            // check crc-16 in the end
-
-            if (CheckCrc14443(CRC_14443_A, dec + 4, 18)) {
+        crypto1_destroy(pcs);
 
-                // lock this section to avoid interlacing prints from different threats
-                pthread_mutex_lock(&print_lock);
-                printf("\nValid Key found: [%012" PRIx64 "]\n", key);
-
-                printf("enc:  %s\n", sprint_hex_inrow_ex(local_enc, ENC_LEN, 0));
-                printf("      xx  crcA                                crcA\n");
-                printf("dec:  %s\n", sprint_hex_inrow_ex(dec, ENC_LEN, 0));
-                pthread_mutex_unlock(&print_lock);
-
-                __sync_fetch_and_add(&global_found, 1);
-            }
+        if (checkValidCmdByte(dec, args->enc_len) == false) {
+            continue;
         }
+        __sync_fetch_and_add(&global_found, 1);
+
+        // lock this section to avoid interlacing prints from different threats
+        pthread_mutex_lock(&print_lock);
+        printf("\nenc:  %s\n", sprint_hex_inrow_ex(local_enc, args->enc_len, 0));
+        printf("dec:  %s\n", sprint_hex_inrow_ex(dec, args->enc_len, 0));
+        printf("\nValid Key found [ " _GREEN_("%012" PRIx64) " ]\n\n", key);
+        pthread_mutex_unlock(&print_lock);
+        break;
     }
+
     free(args);
     return NULL;
 }
@@ -245,7 +248,7 @@ static int usage(void) {
 }
 
 int main(int argc, char *argv[]) {
-    printf("Mifare classic nested auth key recovery. Phase 2.\n");
+    printf("Mifare classic nested auth key recovery Phase 2\n");
     if (argc < 3) return usage();
 
     uint32_t uid = 0;      // serial number
@@ -263,13 +266,13 @@ int main(int argc, char *argv[]) {
     param_gethex_to_eol(argv[5], 0, enc, sizeof(enc), &enc_len);
 
     printf("-------------------------------------------------\n");
-    printf("uid.......... %08x\n", uid);
-    printf("partial key.. %08x\n", part_key);
-    printf("nt enc....... %08x\n", nt_enc);
-    printf("nr enc....... %08x\n", nr_enc);
-    printf("next encrypted cmd: %s\n", sprint_hex_inrow_ex(enc, ENC_LEN, 0));
+    printf("uid.................. %08x\n", uid);
+    printf("partial key.......... %08x\n", part_key);
+    printf("nt enc............... %08x\n", nt_enc);
+    printf("nr enc............... %08x\n", nr_enc);
+    printf("next encrypted cmd... %s\n", sprint_hex_inrow_ex(enc, enc_len, 0));
 
-    clock_t t1 = clock();
+    uint64_t t1 = msclock();
 
 #if !defined(_WIN32) || !defined(__WIN32__)
     thread_count = sysconf(_SC_NPROCESSORS_CONF);
@@ -293,7 +296,8 @@ int main(int argc, char *argv[]) {
         a->part_key = part_key;
         a->nt_enc = nt_enc;
         a->nr_enc = nr_enc;
-        memcpy(a->enc, enc, sizeof(a->enc));
+        a->enc_len = enc_len;
+        memcpy(a->enc, enc, enc_len);
         pthread_create(&threads[i], NULL, brute_thread, (void *)a);
     }
 
@@ -305,7 +309,7 @@ int main(int argc, char *argv[]) {
         printf("\nFailed to find a key\n\n");
     }
 
-    t1 = clock() - t1;
+    t1 = msclock() - t1;
     if (t1 > 0)
         printf("Execution time: %.0f ticks\n", (float)t1);
 
diff --git a/tools/mf_nonce_brute/util_posix.c b/tools/mf_nonce_brute/util_posix.c
new file mode 100644
index 000000000..050040a32
--- /dev/null
+++ b/tools/mf_nonce_brute/util_posix.c
@@ -0,0 +1,137 @@
+//-----------------------------------------------------------------------------
+// Copyright (C) 2010 iZsh <izsh at fail0verflow.com>
+//
+// This code is licensed to you under the terms of the GNU GPL, version 2 or,
+// at your option, any later version. See the LICENSE.txt file for the text of
+// the license.
+//-----------------------------------------------------------------------------
+// utilities requiring Posix library functions
+//-----------------------------------------------------------------------------
+
+// ensure availability even with -std=c99; must be included before
+#if !defined(_WIN32)
+//#define _POSIX_C_SOURCE 199309L // need nanosleep()
+#define _POSIX_C_SOURCE 200112L  // need localtime_r()
+#else
+#include <windows.h>
+#endif
+
+#include "util_posix.h"
+#include <stdint.h>
+#include <time.h>
+
+
+// Timer functions
+#if !defined (_WIN32)
+#include <errno.h>
+
+static void nsleep(uint64_t n) {
+    struct timespec timeout;
+    timeout.tv_sec = n / 1000000000;
+    timeout.tv_nsec = n % 1000000000;
+    while (nanosleep(&timeout, &timeout) && errno == EINTR);
+}
+
+void msleep(uint32_t n) {
+    nsleep(1000000 * (uint64_t)n);
+}
+#endif // _WIN32
+
+#ifdef __APPLE__
+
+#ifndef CLOCK_MONOTONIC
+#define CLOCK_MONOTONIC (1)
+#endif
+#ifndef CLOCK_REALTIME
+#define CLOCK_REALTIME (2)
+#endif
+
+#include <sys/time.h>
+#include <mach/clock.h>
+#include <mach/mach.h>
+#include <mach/mach_time.h>
+
+/* clock_gettime is not implemented on OSX prior to 10.12 */
+int _civet_clock_gettime(int clk_id, struct timespec *t);
+
+int _civet_clock_gettime(int clk_id, struct timespec *t) {
+    memset(t, 0, sizeof(*t));
+    if (clk_id == CLOCK_REALTIME) {
+        struct timeval now;
+        int rv = gettimeofday(&now, NULL);
+        if (rv) {
+            return rv;
+        }
+        t->tv_sec = now.tv_sec;
+        t->tv_nsec = now.tv_usec * 1000;
+        return 0;
+
+    } else if (clk_id == CLOCK_MONOTONIC) {
+        static uint64_t clock_start_time = 0;
+        static mach_timebase_info_data_t timebase_info = {0, 0};
+
+        uint64_t now = mach_absolute_time();
+
+        if (clock_start_time == 0) {
+
+            mach_timebase_info(&timebase_info);
+            clock_start_time = now;
+        }
+
+        now = (uint64_t)((double)(now - clock_start_time)
+                         * (double)timebase_info.numer
+                         / (double)timebase_info.denom);
+
+        t->tv_sec = now / 1000000000;
+        t->tv_nsec = now % 1000000000;
+        return 0;
+    }
+    return -1; // EINVAL - Clock ID is unknown
+}
+
+/* if clock_gettime is declared, then __CLOCK_AVAILABILITY will be defined */
+#ifdef __CLOCK_AVAILABILITY
+/* If we compiled with Mac OSX 10.12 or later, then clock_gettime will be declared
+ * but it may be NULL at runtime. So we need to check before using it. */
+int _civet_safe_clock_gettime(int clk_id, struct timespec *t);
+
+int _civet_safe_clock_gettime(int clk_id, struct timespec *t) {
+    if (clock_gettime) {
+        return clock_gettime(clk_id, t);
+    }
+    return _civet_clock_gettime(clk_id, t);
+}
+#define clock_gettime _civet_safe_clock_gettime
+#else
+#define clock_gettime _civet_clock_gettime
+#endif
+
+#endif
+
+
+// a milliseconds timer for performance measurement
+uint64_t msclock(void) {
+#if defined(_WIN32)
+#include <sys/types.h>
+
+    // WORKAROUND FOR MinGW (some versions - use if normal code does not compile)
+    // It has no _ftime_s and needs explicit inclusion of timeb.h
+#include <sys/timeb.h>
+    struct _timeb t;
+    _ftime(&t);
+    return 1000 * (uint64_t)t.time + t.millitm;
+
+// NORMAL CODE (use _ftime_s)
+    //struct _timeb t;
+    //if (_ftime_s(&t)) {
+    //  return 0;
+    //} else {
+    //  return 1000 * t.time + t.millitm;
+    //}
+#else
+    struct timespec t;
+    clock_gettime(CLOCK_MONOTONIC, &t);
+    return (1000 * (uint64_t)t.tv_sec + t.tv_nsec / 1000000);
+#endif
+}
+
diff --git a/tools/mf_nonce_brute/util_posix.h b/tools/mf_nonce_brute/util_posix.h
new file mode 100644
index 000000000..546b4ea35
--- /dev/null
+++ b/tools/mf_nonce_brute/util_posix.h
@@ -0,0 +1,26 @@
+//-----------------------------------------------------------------------------
+// Copyright (C) 2010 iZsh <izsh at fail0verflow.com>
+//
+// This code is licensed to you under the terms of the GNU GPL, version 2 or,
+// at your option, any later version. See the LICENSE.txt file for the text of
+// the license.
+//-----------------------------------------------------------------------------
+// utilities requiring Posix library functions
+//-----------------------------------------------------------------------------
+
+#ifndef UTIL_POSIX_H__
+#define UTIL_POSIX_H__
+
+#include "common.h"
+
+#ifdef _WIN32
+# include <windows.h>
+# define sleep(n) Sleep(1000 *(n))
+# define msleep(n) Sleep((n))
+#else
+void msleep(uint32_t n); // sleep n milliseconds
+#endif // _WIN32
+
+uint64_t msclock(void);      // a milliseconds clock
+
+#endif
