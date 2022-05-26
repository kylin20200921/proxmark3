commit 8d9dd8b346f742c673988b7207721057ab3fe2e2
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Apr 9 18:48:13 2022 +0200

    make style

diff --git a/armsrc/Standalone/hf_14asniff.c b/armsrc/Standalone/hf_14asniff.c
index eb1bb84e2..041a0b6f0 100644
--- a/armsrc/Standalone/hf_14asniff.c
+++ b/armsrc/Standalone/hf_14asniff.c
@@ -140,5 +140,5 @@ void RunMod(void) {
 #ifdef WITH_FLASH
     DownloadTraceInstructions();
 #endif
-    
+
 }
diff --git a/client/deps/hardnested/hardnested_bf_core.c b/client/deps/hardnested/hardnested_bf_core.c
index 17f296a27..0d4e680fb 100644
--- a/client/deps/hardnested/hardnested_bf_core.c
+++ b/client/deps/hardnested/hardnested_bf_core.c
@@ -589,11 +589,11 @@ static SIMDExecInstr GetSIMDInstr(void) {
         else
 #endif
 #if defined(COMPILER_HAS_SIMD_NEON)
-    if (arm_has_neon())
-        instr = SIMD_NEON;
-    else
+            if (arm_has_neon())
+                instr = SIMD_NEON;
+            else
 #endif
-            instr = SIMD_NONE;
+                instr = SIMD_NONE;
 
     return instr;
 }
diff --git a/client/deps/hardnested/hardnested_bitarray_core.c b/client/deps/hardnested/hardnested_bitarray_core.c
index 2031a9ec5..e0a29c786 100644
--- a/client/deps/hardnested/hardnested_bitarray_core.c
+++ b/client/deps/hardnested/hardnested_bitarray_core.c
@@ -326,17 +326,17 @@ uint32_t *malloc_bitarray_dispatch(uint32_t x) {
 #endif
 
 #if defined(COMPILER_HAS_SIMD_AVX512)
-    if (__builtin_cpu_supports("avx512f")) malloc_bitarray_function_p = &malloc_bitarray_AVX512;
-    else
+        if (__builtin_cpu_supports("avx512f")) malloc_bitarray_function_p = &malloc_bitarray_AVX512;
+        else
 #endif
 #if defined(COMPILER_HAS_SIMD_X86)
-        if (__builtin_cpu_supports("avx2")) malloc_bitarray_function_p = &malloc_bitarray_AVX2;
-        else if (__builtin_cpu_supports("avx")) malloc_bitarray_function_p = &malloc_bitarray_AVX;
-        else if (__builtin_cpu_supports("sse2")) malloc_bitarray_function_p = &malloc_bitarray_SSE2;
-        else if (__builtin_cpu_supports("mmx")) malloc_bitarray_function_p = &malloc_bitarray_MMX;
-        else
+            if (__builtin_cpu_supports("avx2")) malloc_bitarray_function_p = &malloc_bitarray_AVX2;
+            else if (__builtin_cpu_supports("avx")) malloc_bitarray_function_p = &malloc_bitarray_AVX;
+            else if (__builtin_cpu_supports("sse2")) malloc_bitarray_function_p = &malloc_bitarray_SSE2;
+            else if (__builtin_cpu_supports("mmx")) malloc_bitarray_function_p = &malloc_bitarray_MMX;
+            else
 #endif
-            malloc_bitarray_function_p = &malloc_bitarray_NOSIMD;
+                malloc_bitarray_function_p = &malloc_bitarray_NOSIMD;
 
     // call the most optimized function for this CPU
     return (*malloc_bitarray_function_p)(x);
@@ -349,17 +349,17 @@ void free_bitarray_dispatch(uint32_t *x) {
 #endif
 
 #if defined(COMPILER_HAS_SIMD_AVX512)
-    if (__builtin_cpu_supports("avx512f")) free_bitarray_function_p = &free_bitarray_AVX512;
-    else
+        if (__builtin_cpu_supports("avx512f")) free_bitarray_function_p = &free_bitarray_AVX512;
+        else
 #endif
 #if defined(COMPILER_HAS_SIMD_X86)
-        if (__builtin_cpu_supports("avx2")) free_bitarray_function_p = &free_bitarray_AVX2;
-        else if (__builtin_cpu_supports("avx")) free_bitarray_function_p = &free_bitarray_AVX;
-        else if (__builtin_cpu_supports("sse2")) free_bitarray_function_p = &free_bitarray_SSE2;
-        else if (__builtin_cpu_supports("mmx")) free_bitarray_function_p = &free_bitarray_MMX;
-        else
+            if (__builtin_cpu_supports("avx2")) free_bitarray_function_p = &free_bitarray_AVX2;
+            else if (__builtin_cpu_supports("avx")) free_bitarray_function_p = &free_bitarray_AVX;
+            else if (__builtin_cpu_supports("sse2")) free_bitarray_function_p = &free_bitarray_SSE2;
+            else if (__builtin_cpu_supports("mmx")) free_bitarray_function_p = &free_bitarray_MMX;
+            else
 #endif
-            free_bitarray_function_p = &free_bitarray_NOSIMD;
+                free_bitarray_function_p = &free_bitarray_NOSIMD;
 
     // call the most optimized function for this CPU
     (*free_bitarray_function_p)(x);
@@ -372,17 +372,17 @@ uint32_t bitcount_dispatch(uint32_t a) {
 #endif
 
 #if defined(COMPILER_HAS_SIMD_AVX512)
-    if (__builtin_cpu_supports("avx512f")) bitcount_function_p = &bitcount_AVX512;
-    else
+        if (__builtin_cpu_supports("avx512f")) bitcount_function_p = &bitcount_AVX512;
+        else
 #endif
 #if defined(COMPILER_HAS_SIMD_X86)
-        if (__builtin_cpu_supports("avx2")) bitcount_function_p = &bitcount_AVX2;
-        else if (__builtin_cpu_supports("avx")) bitcount_function_p = &bitcount_AVX;
-        else if (__builtin_cpu_supports("sse2")) bitcount_function_p = &bitcount_SSE2;
-        else if (__builtin_cpu_supports("mmx")) bitcount_function_p = &bitcount_MMX;
-        else
+            if (__builtin_cpu_supports("avx2")) bitcount_function_p = &bitcount_AVX2;
+            else if (__builtin_cpu_supports("avx")) bitcount_function_p = &bitcount_AVX;
+            else if (__builtin_cpu_supports("sse2")) bitcount_function_p = &bitcount_SSE2;
+            else if (__builtin_cpu_supports("mmx")) bitcount_function_p = &bitcount_MMX;
+            else
 #endif
-            bitcount_function_p = &bitcount_NOSIMD;
+                bitcount_function_p = &bitcount_NOSIMD;
 
     // call the most optimized function for this CPU
     return (*bitcount_function_p)(a);
@@ -395,17 +395,17 @@ uint32_t count_states_dispatch(uint32_t *bitarray) {
 #endif
 
 #if defined(COMPILER_HAS_SIMD_AVX512)
-    if (__builtin_cpu_supports("avx512f")) count_states_function_p = &count_states_AVX512;
-    else
+        if (__builtin_cpu_supports("avx512f")) count_states_function_p = &count_states_AVX512;
+        else
 #endif
 #if defined(COMPILER_HAS_SIMD_X86)
-        if (__builtin_cpu_supports("avx2")) count_states_function_p = &count_states_AVX2;
-        else if (__builtin_cpu_supports("avx")) count_states_function_p = &count_states_AVX;
-        else if (__builtin_cpu_supports("sse2")) count_states_function_p = &count_states_SSE2;
-        else if (__builtin_cpu_supports("mmx")) count_states_function_p = &count_states_MMX;
-        else
+            if (__builtin_cpu_supports("avx2")) count_states_function_p = &count_states_AVX2;
+            else if (__builtin_cpu_supports("avx")) count_states_function_p = &count_states_AVX;
+            else if (__builtin_cpu_supports("sse2")) count_states_function_p = &count_states_SSE2;
+            else if (__builtin_cpu_supports("mmx")) count_states_function_p = &count_states_MMX;
+            else
 #endif
-            count_states_function_p = &count_states_NOSIMD;
+                count_states_function_p = &count_states_NOSIMD;
 
     // call the most optimized function for this CPU
     return (*count_states_function_p)(bitarray);
@@ -418,17 +418,17 @@ void bitarray_AND_dispatch(uint32_t *A, uint32_t *B) {
 #endif
 
 #if defined(COMPILER_HAS_SIMD_AVX512)
-    if (__builtin_cpu_supports("avx512f")) bitarray_AND_function_p = &bitarray_AND_AVX512;
-    else
+        if (__builtin_cpu_supports("avx512f")) bitarray_AND_function_p = &bitarray_AND_AVX512;
+        else
 #endif
 #if defined(COMPILER_HAS_SIMD_X86)
-        if (__builtin_cpu_supports("avx2")) bitarray_AND_function_p = &bitarray_AND_AVX2;
-        else if (__builtin_cpu_supports("avx")) bitarray_AND_function_p = &bitarray_AND_AVX;
-        else if (__builtin_cpu_supports("sse2")) bitarray_AND_function_p = &bitarray_AND_SSE2;
-        else if (__builtin_cpu_supports("mmx")) bitarray_AND_function_p = &bitarray_AND_MMX;
-        else
+            if (__builtin_cpu_supports("avx2")) bitarray_AND_function_p = &bitarray_AND_AVX2;
+            else if (__builtin_cpu_supports("avx")) bitarray_AND_function_p = &bitarray_AND_AVX;
+            else if (__builtin_cpu_supports("sse2")) bitarray_AND_function_p = &bitarray_AND_SSE2;
+            else if (__builtin_cpu_supports("mmx")) bitarray_AND_function_p = &bitarray_AND_MMX;
+            else
 #endif
-            bitarray_AND_function_p = &bitarray_AND_NOSIMD;
+                bitarray_AND_function_p = &bitarray_AND_NOSIMD;
 
     // call the most optimized function for this CPU
     (*bitarray_AND_function_p)(A, B);
@@ -441,17 +441,17 @@ void bitarray_low20_AND_dispatch(uint32_t *A, uint32_t *B) {
 #endif
 
 #if defined(COMPILER_HAS_SIMD_AVX512)
-    if (__builtin_cpu_supports("avx512f")) bitarray_low20_AND_function_p = &bitarray_low20_AND_AVX512;
-    else
+        if (__builtin_cpu_supports("avx512f")) bitarray_low20_AND_function_p = &bitarray_low20_AND_AVX512;
+        else
 #endif
 #if defined(COMPILER_HAS_SIMD_X86)
-        if (__builtin_cpu_supports("avx2")) bitarray_low20_AND_function_p = &bitarray_low20_AND_AVX2;
-        else if (__builtin_cpu_supports("avx")) bitarray_low20_AND_function_p = &bitarray_low20_AND_AVX;
-        else if (__builtin_cpu_supports("sse2")) bitarray_low20_AND_function_p = &bitarray_low20_AND_SSE2;
-        else if (__builtin_cpu_supports("mmx")) bitarray_low20_AND_function_p = &bitarray_low20_AND_MMX;
-        else
+            if (__builtin_cpu_supports("avx2")) bitarray_low20_AND_function_p = &bitarray_low20_AND_AVX2;
+            else if (__builtin_cpu_supports("avx")) bitarray_low20_AND_function_p = &bitarray_low20_AND_AVX;
+            else if (__builtin_cpu_supports("sse2")) bitarray_low20_AND_function_p = &bitarray_low20_AND_SSE2;
+            else if (__builtin_cpu_supports("mmx")) bitarray_low20_AND_function_p = &bitarray_low20_AND_MMX;
+            else
 #endif
-            bitarray_low20_AND_function_p = &bitarray_low20_AND_NOSIMD;
+                bitarray_low20_AND_function_p = &bitarray_low20_AND_NOSIMD;
 
     // call the most optimized function for this CPU
     (*bitarray_low20_AND_function_p)(A, B);
@@ -464,17 +464,17 @@ uint32_t count_bitarray_AND_dispatch(uint32_t *A, uint32_t *B) {
 #endif
 
 #if defined(COMPILER_HAS_SIMD_AVX512)
-    if (__builtin_cpu_supports("avx512f")) count_bitarray_AND_function_p = &count_bitarray_AND_AVX512;
-    else
+        if (__builtin_cpu_supports("avx512f")) count_bitarray_AND_function_p = &count_bitarray_AND_AVX512;
+        else
 #endif
 #if defined(COMPILER_HAS_SIMD_X86)
-        if (__builtin_cpu_supports("avx2")) count_bitarray_AND_function_p = &count_bitarray_AND_AVX2;
-        else if (__builtin_cpu_supports("avx")) count_bitarray_AND_function_p = &count_bitarray_AND_AVX;
-        else if (__builtin_cpu_supports("sse2")) count_bitarray_AND_function_p = &count_bitarray_AND_SSE2;
-        else if (__builtin_cpu_supports("mmx")) count_bitarray_AND_function_p = &count_bitarray_AND_MMX;
-        else
+            if (__builtin_cpu_supports("avx2")) count_bitarray_AND_function_p = &count_bitarray_AND_AVX2;
+            else if (__builtin_cpu_supports("avx")) count_bitarray_AND_function_p = &count_bitarray_AND_AVX;
+            else if (__builtin_cpu_supports("sse2")) count_bitarray_AND_function_p = &count_bitarray_AND_SSE2;
+            else if (__builtin_cpu_supports("mmx")) count_bitarray_AND_function_p = &count_bitarray_AND_MMX;
+            else
 #endif
-            count_bitarray_AND_function_p = &count_bitarray_AND_NOSIMD;
+                count_bitarray_AND_function_p = &count_bitarray_AND_NOSIMD;
 
     // call the most optimized function for this CPU
     return (*count_bitarray_AND_function_p)(A, B);
@@ -487,17 +487,17 @@ uint32_t count_bitarray_low20_AND_dispatch(uint32_t *A, uint32_t *B) {
 #endif
 
 #if defined(COMPILER_HAS_SIMD_AVX512)
-    if (__builtin_cpu_supports("avx512f")) count_bitarray_low20_AND_function_p = &count_bitarray_low20_AND_AVX512;
-    else
+        if (__builtin_cpu_supports("avx512f")) count_bitarray_low20_AND_function_p = &count_bitarray_low20_AND_AVX512;
+        else
 #endif
 #if defined(COMPILER_HAS_SIMD_X86)
-        if (__builtin_cpu_supports("avx2")) count_bitarray_low20_AND_function_p = &count_bitarray_low20_AND_AVX2;
-        else if (__builtin_cpu_supports("avx")) count_bitarray_low20_AND_function_p = &count_bitarray_low20_AND_AVX;
-        else if (__builtin_cpu_supports("sse2")) count_bitarray_low20_AND_function_p = &count_bitarray_low20_AND_SSE2;
-        else if (__builtin_cpu_supports("mmx")) count_bitarray_low20_AND_function_p = &count_bitarray_low20_AND_MMX;
-        else
+            if (__builtin_cpu_supports("avx2")) count_bitarray_low20_AND_function_p = &count_bitarray_low20_AND_AVX2;
+            else if (__builtin_cpu_supports("avx")) count_bitarray_low20_AND_function_p = &count_bitarray_low20_AND_AVX;
+            else if (__builtin_cpu_supports("sse2")) count_bitarray_low20_AND_function_p = &count_bitarray_low20_AND_SSE2;
+            else if (__builtin_cpu_supports("mmx")) count_bitarray_low20_AND_function_p = &count_bitarray_low20_AND_MMX;
+            else
 #endif
-            count_bitarray_low20_AND_function_p = &count_bitarray_low20_AND_NOSIMD;
+                count_bitarray_low20_AND_function_p = &count_bitarray_low20_AND_NOSIMD;
 
     // call the most optimized function for this CPU
     return (*count_bitarray_low20_AND_function_p)(A, B);
@@ -510,17 +510,17 @@ void bitarray_AND4_dispatch(uint32_t *A, uint32_t *B, uint32_t *C, uint32_t *D)
 #endif
 
 #if defined(COMPILER_HAS_SIMD_AVX512)
-    if (__builtin_cpu_supports("avx512f")) bitarray_AND4_function_p = &bitarray_AND4_AVX512;
-    else
+        if (__builtin_cpu_supports("avx512f")) bitarray_AND4_function_p = &bitarray_AND4_AVX512;
+        else
 #endif
 #if defined(COMPILER_HAS_SIMD_X86)
-        if (__builtin_cpu_supports("avx2")) bitarray_AND4_function_p = &bitarray_AND4_AVX2;
-        else if (__builtin_cpu_supports("avx")) bitarray_AND4_function_p = &bitarray_AND4_AVX;
-        else if (__builtin_cpu_supports("sse2")) bitarray_AND4_function_p = &bitarray_AND4_SSE2;
-        else if (__builtin_cpu_supports("mmx")) bitarray_AND4_function_p = &bitarray_AND4_MMX;
-        else
+            if (__builtin_cpu_supports("avx2")) bitarray_AND4_function_p = &bitarray_AND4_AVX2;
+            else if (__builtin_cpu_supports("avx")) bitarray_AND4_function_p = &bitarray_AND4_AVX;
+            else if (__builtin_cpu_supports("sse2")) bitarray_AND4_function_p = &bitarray_AND4_SSE2;
+            else if (__builtin_cpu_supports("mmx")) bitarray_AND4_function_p = &bitarray_AND4_MMX;
+            else
 #endif
-            bitarray_AND4_function_p = &bitarray_AND4_NOSIMD;
+                bitarray_AND4_function_p = &bitarray_AND4_NOSIMD;
 
     // call the most optimized function for this CPU
     (*bitarray_AND4_function_p)(A, B, C, D);
@@ -533,17 +533,17 @@ void bitarray_OR_dispatch(uint32_t *A, uint32_t *B) {
 #endif
 
 #if defined(COMPILER_HAS_SIMD_AVX512)
-    if (__builtin_cpu_supports("avx512f")) bitarray_OR_function_p = &bitarray_OR_AVX512;
-    else
+        if (__builtin_cpu_supports("avx512f")) bitarray_OR_function_p = &bitarray_OR_AVX512;
+        else
 #endif
 #if defined(COMPILER_HAS_SIMD_X86)
-        if (__builtin_cpu_supports("avx2")) bitarray_OR_function_p = &bitarray_OR_AVX2;
-        else if (__builtin_cpu_supports("avx")) bitarray_OR_function_p = &bitarray_OR_AVX;
-        else if (__builtin_cpu_supports("sse2")) bitarray_OR_function_p = &bitarray_OR_SSE2;
-        else if (__builtin_cpu_supports("mmx")) bitarray_OR_function_p = &bitarray_OR_MMX;
-        else
+            if (__builtin_cpu_supports("avx2")) bitarray_OR_function_p = &bitarray_OR_AVX2;
+            else if (__builtin_cpu_supports("avx")) bitarray_OR_function_p = &bitarray_OR_AVX;
+            else if (__builtin_cpu_supports("sse2")) bitarray_OR_function_p = &bitarray_OR_SSE2;
+            else if (__builtin_cpu_supports("mmx")) bitarray_OR_function_p = &bitarray_OR_MMX;
+            else
 #endif
-            bitarray_OR_function_p = &bitarray_OR_NOSIMD;
+                bitarray_OR_function_p = &bitarray_OR_NOSIMD;
 
     // call the most optimized function for this CPU
     (*bitarray_OR_function_p)(A, B);
@@ -556,17 +556,17 @@ uint32_t count_bitarray_AND2_dispatch(uint32_t *A, uint32_t *B) {
 #endif
 
 #if defined(COMPILER_HAS_SIMD_AVX512)
-    if (__builtin_cpu_supports("avx512f")) count_bitarray_AND2_function_p = &count_bitarray_AND2_AVX512;
-    else
+        if (__builtin_cpu_supports("avx512f")) count_bitarray_AND2_function_p = &count_bitarray_AND2_AVX512;
+        else
 #endif
 #if defined(COMPILER_HAS_SIMD_X86)
-        if (__builtin_cpu_supports("avx2")) count_bitarray_AND2_function_p = &count_bitarray_AND2_AVX2;
-        else if (__builtin_cpu_supports("avx")) count_bitarray_AND2_function_p = &count_bitarray_AND2_AVX;
-        else if (__builtin_cpu_supports("sse2")) count_bitarray_AND2_function_p = &count_bitarray_AND2_SSE2;
-        else if (__builtin_cpu_supports("mmx")) count_bitarray_AND2_function_p = &count_bitarray_AND2_MMX;
-        else
+            if (__builtin_cpu_supports("avx2")) count_bitarray_AND2_function_p = &count_bitarray_AND2_AVX2;
+            else if (__builtin_cpu_supports("avx")) count_bitarray_AND2_function_p = &count_bitarray_AND2_AVX;
+            else if (__builtin_cpu_supports("sse2")) count_bitarray_AND2_function_p = &count_bitarray_AND2_SSE2;
+            else if (__builtin_cpu_supports("mmx")) count_bitarray_AND2_function_p = &count_bitarray_AND2_MMX;
+            else
 #endif
-            count_bitarray_AND2_function_p = &count_bitarray_AND2_NOSIMD;
+                count_bitarray_AND2_function_p = &count_bitarray_AND2_NOSIMD;
 
     // call the most optimized function for this CPU
     return (*count_bitarray_AND2_function_p)(A, B);
@@ -579,17 +579,17 @@ uint32_t count_bitarray_AND3_dispatch(uint32_t *A, uint32_t *B, uint32_t *C) {
 #endif
 
 #if defined(COMPILER_HAS_SIMD_AVX512)
-    if (__builtin_cpu_supports("avx512f")) count_bitarray_AND3_function_p = &count_bitarray_AND3_AVX512;
-    else
+        if (__builtin_cpu_supports("avx512f")) count_bitarray_AND3_function_p = &count_bitarray_AND3_AVX512;
+        else
 #endif
 #if defined(COMPILER_HAS_SIMD_X86)
-        if (__builtin_cpu_supports("avx2")) count_bitarray_AND3_function_p = &count_bitarray_AND3_AVX2;
-        else if (__builtin_cpu_supports("avx")) count_bitarray_AND3_function_p = &count_bitarray_AND3_AVX;
-        else if (__builtin_cpu_supports("sse2")) count_bitarray_AND3_function_p = &count_bitarray_AND3_SSE2;
-        else if (__builtin_cpu_supports("mmx")) count_bitarray_AND3_function_p = &count_bitarray_AND3_MMX;
-        else
+            if (__builtin_cpu_supports("avx2")) count_bitarray_AND3_function_p = &count_bitarray_AND3_AVX2;
+            else if (__builtin_cpu_supports("avx")) count_bitarray_AND3_function_p = &count_bitarray_AND3_AVX;
+            else if (__builtin_cpu_supports("sse2")) count_bitarray_AND3_function_p = &count_bitarray_AND3_SSE2;
+            else if (__builtin_cpu_supports("mmx")) count_bitarray_AND3_function_p = &count_bitarray_AND3_MMX;
+            else
 #endif
-            count_bitarray_AND3_function_p = &count_bitarray_AND3_NOSIMD;
+                count_bitarray_AND3_function_p = &count_bitarray_AND3_NOSIMD;
 
     // call the most optimized function for this CPU
     return (*count_bitarray_AND3_function_p)(A, B, C);
@@ -602,17 +602,17 @@ uint32_t count_bitarray_AND4_dispatch(uint32_t *A, uint32_t *B, uint32_t *C, uin
 #endif
 
 #if defined(COMPILER_HAS_SIMD_AVX512)
-    if (__builtin_cpu_supports("avx512f")) count_bitarray_AND4_function_p = &count_bitarray_AND4_AVX512;
-    else
+        if (__builtin_cpu_supports("avx512f")) count_bitarray_AND4_function_p = &count_bitarray_AND4_AVX512;
+        else
 #endif
 #if defined(COMPILER_HAS_SIMD_X86)
-        if (__builtin_cpu_supports("avx2")) count_bitarray_AND4_function_p = &count_bitarray_AND4_AVX2;
-        else if (__builtin_cpu_supports("avx")) count_bitarray_AND4_function_p = &count_bitarray_AND4_AVX;
-        else if (__builtin_cpu_supports("sse2")) count_bitarray_AND4_function_p = &count_bitarray_AND4_SSE2;
-        else if (__builtin_cpu_supports("mmx")) count_bitarray_AND4_function_p = &count_bitarray_AND4_MMX;
-        else
+            if (__builtin_cpu_supports("avx2")) count_bitarray_AND4_function_p = &count_bitarray_AND4_AVX2;
+            else if (__builtin_cpu_supports("avx")) count_bitarray_AND4_function_p = &count_bitarray_AND4_AVX;
+            else if (__builtin_cpu_supports("sse2")) count_bitarray_AND4_function_p = &count_bitarray_AND4_SSE2;
+            else if (__builtin_cpu_supports("mmx")) count_bitarray_AND4_function_p = &count_bitarray_AND4_MMX;
+            else
 #endif
-            count_bitarray_AND4_function_p = &count_bitarray_AND4_NOSIMD;
+                count_bitarray_AND4_function_p = &count_bitarray_AND4_NOSIMD;
 
     // call the most optimized function for this CPU
     return (*count_bitarray_AND4_function_p)(A, B, C, D);
diff --git a/client/src/cmdhf.c b/client/src/cmdhf.c
index cc31d9222..894fddb4c 100644
--- a/client/src/cmdhf.c
+++ b/client/src/cmdhf.c
@@ -430,7 +430,7 @@ static command_t CommandTable[] = {
     {"mfp",         CmdHFMFP,         AlwaysAvailable, "{ MIFARE Plus RFIDs...                }"},
     {"mfu",         CmdHFMFUltra,     AlwaysAvailable, "{ MIFARE Ultralight RFIDs...          }"},
     {"mfdes",       CmdHFMFDes,       AlwaysAvailable, "{ MIFARE Desfire RFIDs...             }"},
-    {"ntag424",     CmdHF_ntag424,    AlwaysAvailable, "{ NXP NTAG 4242 DNA RFIDs...          }"},    
+    {"ntag424",     CmdHF_ntag424,    AlwaysAvailable, "{ NXP NTAG 4242 DNA RFIDs...          }"},
     {"seos",        CmdHFSeos,        AlwaysAvailable, "{ SEOS RFIDs...                       }"},
     {"st25ta",      CmdHFST25TA,      AlwaysAvailable, "{ ST25TA RFIDs...                     }"},
     {"thinfilm",    CmdHFThinfilm,    AlwaysAvailable, "{ Thinfilm RFIDs...                   }"},
diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index 2e9a7b8f3..028ded92a 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -2459,17 +2459,17 @@ retry_ins:
                         }
 
                         if (verbose == true || sw != 0x6e00) {
-                            PrintAndLogEx(log_level, "Got response for APDU \"%s\": %04X (%s)", 
-                                    sprint_hex_inrow(command, command_n + i),
-                                    sw,
-                                    GetAPDUCodeDescription(sw >> 8, sw & 0xff)
-                                );
+                            PrintAndLogEx(log_level, "Got response for APDU \"%s\": %04X (%s)",
+                                          sprint_hex_inrow(command, command_n + i),
+                                          sw,
+                                          GetAPDUCodeDescription(sw >> 8, sw & 0xff)
+                                         );
 
                             if (response_n > 2) {
                                 PrintAndLogEx(SUCCESS, "Response data is: %s | %s",
-                                    sprint_hex_inrow(response, response_n - 2),
-                                    sprint_ascii(response, response_n - 2)
-                                );
+                                              sprint_hex_inrow(response, response_n - 2),
+                                              sprint_ascii(response, response_n - 2)
+                                             );
                             }
                         }
                     }
diff --git a/client/src/cmdhfntag424.c b/client/src/cmdhfntag424.c
index e41516ca1..dfe118e02 100644
--- a/client/src/cmdhfntag424.c
+++ b/client/src/cmdhfntag424.c
@@ -201,6 +201,22 @@ static int CmdHF_ntag424_info(const char *Cmd) {
 
     PrintAndLogEx(INFO, "not implemented yet");
     PrintAndLogEx(INFO, "Feel free to contribute!");
+
+
+    // has hardcoded application and three files.
+
+
+    /*
+        // Check if the tag reponds to APDUs.
+        PrintAndLogEx(INFO, "Sending a test APDU (select file command) to check if the tag is responding to APDU");
+        param_gethex_to_eol("00a404000aa000000440000101000100", 0, aSELECT_AID, sizeof(aSELECT_AID), &aSELECT_AID_n);
+        int res = ExchangeAPDU14a(aSELECT_AID, aSELECT_AID_n, true, false, response, sizeof(response), &response_n);
+        if (res != PM3_SUCCESS) {
+            PrintAndLogEx(FAILED, "Tag did not respond to a test APDU (select file command). Aborting...");
+            return res;
+        }
+    */
+
     return PM3_SUCCESS;
 }
 
@@ -211,7 +227,8 @@ static command_t CommandTable[] = {
     {"help",     CmdHelp,                AlwaysAvailable,  "This help"},
     {"-----------", CmdHelp,             IfPm3Iso14443a,   "----------------------- " _CYAN_("operations") " -----------------------"},
     {"info",     CmdHF_ntag424_info,     IfPm3Iso14443a,   "Tag information"},
-    {"ndefread", CmdHF_ntag424_sdm,      IfPm3Iso14443a,   "Prints NDEF records from card"},
+//     {"ndefread", CmdHF_ntag424_sdm,      IfPm3Iso14443a,   "Prints NDEF records from card"},
+    {"sdm",      CmdHF_ntag424_sdm,      IfPm3Iso14443a,   "Prints NDEF records from card"},
     {"view",     CmdHF_ntag424_view,     AlwaysAvailable,  "Display content from tag dump file"},
     {NULL, NULL, NULL, NULL}
 };
diff --git a/client/src/cmdnfc.c b/client/src/cmdnfc.c
index 683c4b2a2..a87ec5797 100644
--- a/client/src/cmdnfc.c
+++ b/client/src/cmdnfc.c
@@ -39,25 +39,24 @@ void print_type4_cc_info(uint8_t *d, uint8_t n) {
 
     PrintAndLogEx(NORMAL, "");
     PrintAndLogEx(SUCCESS, "------------ " _CYAN_("Capability Container file") " ------------");
-    PrintAndLogEx(SUCCESS, " len      %u bytes (" _GREEN_("0x%02X") ")", d[1], d[1]);
-    PrintAndLogEx(SUCCESS, " version  %s (" _GREEN_("0x%02X") ")", (d[2] == 0x20) ? "v2.0" : "v1.0", d[2]);
-
+    PrintAndLogEx(SUCCESS, " Version... %s ( " _GREEN_("0x%02X") " )", (d[2] == 0x20) ? "v2.0" : "v1.0", d[2]);
+    PrintAndLogEx(SUCCESS, " Len....... %u bytes ( " _GREEN_("0x%02X") " )", d[1], d[1]);
     uint16_t maxr = (d[3] << 8 | d[4]);
-    PrintAndLogEx(SUCCESS, " max bytes read  %u bytes ( 0x%04X )", maxr, maxr);
+    PrintAndLogEx(SUCCESS, " Max bytes read  %u bytes ( 0x%04X )", maxr, maxr);
     uint16_t maxw = (d[5] << 8 | d[6]);
-    PrintAndLogEx(SUCCESS, " max bytes write %u bytes ( 0x%04X )", maxw, maxw);
+    PrintAndLogEx(SUCCESS, " Max bytes write %u bytes ( 0x%04X )", maxw, maxw);
     PrintAndLogEx(NORMAL, "");
-    PrintAndLogEx(SUCCESS, " NDEF file control TLV  {");
-    PrintAndLogEx(SUCCESS, "    (t) type of file  ( %02X )", d[7]);
-    PrintAndLogEx(SUCCESS, "    (v)               ( %02X )", d[8]);
-    PrintAndLogEx(SUCCESS, "    file id           ( %02X%02X )", d[9], d[10]);
+    PrintAndLogEx(SUCCESS, " NDEF file control TLV");
+    PrintAndLogEx(SUCCESS, "    (t) type of file.... %02X", d[7]);
+    PrintAndLogEx(SUCCESS, "    (v) ................ %02X", d[8]);
+    PrintAndLogEx(SUCCESS, "    file id............. %02X%02X", d[9], d[10]);
 
     uint16_t maxndef = (d[11] << 8 | d[12]);
-    PrintAndLogEx(SUCCESS, "    max NDEF filesize   %u bytes ( 0x%04X )", maxndef, maxndef);
-    PrintAndLogEx(SUCCESS, "    ----- " _CYAN_("access rights") " -------");
+    PrintAndLogEx(SUCCESS, "    Max NDEF filesize... %u bytes ( 0x%04X )", maxndef, maxndef);
+    PrintAndLogEx(SUCCESS, "    " _CYAN_("Access rights"));
     PrintAndLogEx(SUCCESS, "    read   ( %02X ) protection: %s", d[13], ((d[13] & 0x80) == 0x80) ? _RED_("enabled") : _GREEN_("disabled"));
     PrintAndLogEx(SUCCESS, "    write  ( %02X ) protection: %s", d[14], ((d[14] & 0x80) == 0x80) ? _RED_("enabled") : _GREEN_("disabled"));
-    PrintAndLogEx(SUCCESS, " }");
+    PrintAndLogEx(SUCCESS, "");
     PrintAndLogEx(SUCCESS, "----------------- " _CYAN_("raw") " -----------------");
     PrintAndLogEx(SUCCESS, "%s", sprint_hex_inrow(d, n));
     PrintAndLogEx(NORMAL, "");
diff --git a/client/src/comms.c b/client/src/comms.c
index 48c142b13..535b95a61 100644
--- a/client/src/comms.c
+++ b/client/src/comms.c
@@ -413,8 +413,7 @@ __attribute__((force_align_arg_pointer))
                             }
                         }
                     }
-                }
-                else if ((!error) && (length == 0)) { // we received an empty frame
+                } else if ((!error) && (length == 0)) { // we received an empty frame
                     if (rx.ng)
                         rx.length = 0; // set received length to 0
                     else {  // old frames can't be empty
diff --git a/client/src/pm3line_vocabulory.h b/client/src/pm3line_vocabulory.h
index 0564a76f4..4e4c3948e 100644
--- a/client/src/pm3line_vocabulory.h
+++ b/client/src/pm3line_vocabulory.h
@@ -400,6 +400,10 @@ const static vocabulory_t vocabulory[] = {
     { 0, "hf mfdes value" }, 
     { 0, "hf mfdes clearrecfile" }, 
     { 1, "hf mfdes test" }, 
+    { 1, "hf ntag424 help" }, 
+    { 0, "hf ntag424 info" }, 
+    { 0, "hf ntag424 sdm" }, 
+    { 1, "hf ntag424 view" }, 
     { 1, "hf seos help" }, 
     { 0, "hf seos info" }, 
     { 1, "hf seos list" }, 
diff --git a/doc/commands.json b/doc/commands.json
index 19837077f..0f1becdb5 100644
--- a/doc/commands.json
+++ b/doc/commands.json
@@ -1554,7 +1554,7 @@
             "command": "hf 15 raw",
             "description": "Sends raw bytes over ISO-15693 to card",
             "notes": [
-                "hf 15 raw -c -d 260100 -> add crc",
+                "hf 15 raw -sc -d 260100 -> add crc",
                 "hf 15 raw -krc -d 260100 -> add crc, keep field on, skip response"
             ],
             "offline": false,
@@ -1564,9 +1564,10 @@
                 "-c, --crc calculate and append CRC",
                 "-k keep signal field ON after receive",
                 "-r do not read response",
+                "-s active signal field ON with select",
                 "-d, --data <hex> raw bytes to send"
             ],
-            "usage": "hf 15 raw [-h2ckr] -d <hex>"
+            "usage": "hf 15 raw [-h2ckrs] -d <hex>"
         },
         "hf 15 rdbl": {
             "command": "hf 15 rdbl",
@@ -2661,7 +2662,7 @@
         },
         "hf help": {
             "command": "hf help",
-            "description": "-------- ----------------------- High Frequency ----------------------- 14a { ISO14443A RFIDs... } 14b { ISO14443B RFIDs... } 15 { ISO15693 RFIDs... } cipurse { Cipurse transport Cards... } epa { German Identification Card... } emrtd { Machine Readable Travel Document... } felica { ISO18092 / FeliCa RFIDs... } fido { FIDO and FIDO2 authenticators... } gallagher { Gallagher DESFire RFIDs... } ksx6924 { KS X 6924 (T-Money, Snapper+) RFIDs } jooki { Jooki RFIDs... } iclass { ICLASS RFIDs... } legic { LEGIC RFIDs... } lto { LTO Cartridge Memory RFIDs... } mf { MIFARE RFIDs... } mfp { MIFARE Plus RFIDs... } mfu { MIFARE Ultralight RFIDs... } mfdes { MIFARE Desfire RFIDs... } seos { SEOS RFIDs... } st25ta { ST25TA RFIDs... } thinfilm { Thinfilm RFIDs... } topaz { TOPAZ (NFC Type 1) RFIDs... } waveshare { Waveshare NFC ePaper... } ----------- --------------------- General --------------------- help This help list List protocol data in trace buffer search Search for known HF tags",
+            "description": "-------- ----------------------- High Frequency ----------------------- 14a { ISO14443A RFIDs... } 14b { ISO14443B RFIDs... } 15 { ISO15693 RFIDs... } cipurse { Cipurse transport Cards... } epa { German Identification Card... } emrtd { Machine Readable Travel Document... } felica { ISO18092 / FeliCa RFIDs... } fido { FIDO and FIDO2 authenticators... } gallagher { Gallagher DESFire RFIDs... } ksx6924 { KS X 6924 (T-Money, Snapper+) RFIDs } jooki { Jooki RFIDs... } iclass { ICLASS RFIDs... } legic { LEGIC RFIDs... } lto { LTO Cartridge Memory RFIDs... } mf { MIFARE RFIDs... } mfp { MIFARE Plus RFIDs... } mfu { MIFARE Ultralight RFIDs... } mfdes { MIFARE Desfire RFIDs... } ntag424 { NXP NTAG 4242 DNA RFIDs... } seos { SEOS RFIDs... } st25ta { ST25TA RFIDs... } thinfilm { Thinfilm RFIDs... } topaz { TOPAZ (NFC Type 1) RFIDs... } waveshare { Waveshare NFC ePaper... } ----------- --------------------- General --------------------- help This help list List protocol data in trace buffer search Search for known HF tags",
             "notes": [],
             "offline": true,
             "options": [],
@@ -4573,7 +4574,7 @@
             "notes": [
                 "hf mfdes chk --aid 123456 -k 000102030405060708090a0b0c0d0e0f -> check key on aid 0x123456",
                 "hf mfdes chk -d mfdes_default_keys -> check keys from dictionary against all existing aid on card",
-                "hf mfdes chk -d mfdes_default_keys -a 123456 -> check keys from dictionary against aid 0x123456",
+                "hf mfdes chk -d mfdes_default_keys --aid 123456 -> check keys from dictionary against aid 0x123456",
                 "hf mfdes chk --aid 123456 --pattern1b -j keys -> check all 1-byte keys pattern on aid 0x123456 and save found keys to json",
                 "hf mfdes chk --aid 123456 --pattern2b --startp2b FA00 -> check all 2-byte keys pattern on aid 0x123456. Start from key FA00FA00...FA00"
             ],
@@ -6056,6 +6057,52 @@
             ],
             "usage": "hf mfu wrbl [-hl] [-k <hex>] -b <dec> -d <hex> [--force]"
         },
+        "hf ntag424 help": {
+            "command": "hf ntag424 help",
+            "description": "help This help view Display content from tag dump file",
+            "notes": [],
+            "offline": true,
+            "options": [],
+            "usage": ""
+        },
+        "hf ntag424 info": {
+            "command": "hf ntag424 info",
+            "description": "Get info about NXP NTAG424 DNA Family styled tag.",
+            "notes": [
+                "hf ntag424 info"
+            ],
+            "offline": false,
+            "options": [
+                "-h, --help This help"
+            ],
+            "usage": "hf ntag424 info [-h]"
+        },
+        "hf ntag424 sdm": {
+            "command": "hf ntag424 sdm",
+            "description": "Validate a SDM message",
+            "notes": [
+                "hf ntag424 sdm"
+            ],
+            "offline": false,
+            "options": [
+                "-h, --help This help"
+            ],
+            "usage": "hf ntag424 sdm [-h]"
+        },
+        "hf ntag424 view": {
+            "command": "hf ntag424 view",
+            "description": "Print a NTAG 424 DNA dump file (bin/eml/json)",
+            "notes": [
+                "hf ntag424 view -f hf-ntag424-01020304-dump.bin"
+            ],
+            "offline": true,
+            "options": [
+                "-h, --help This help",
+                "-f, --file <fn> Filename of dump",
+                "-v, --verbose Verbose output"
+            ],
+            "usage": "hf ntag424 view [-hv] -f <fn>"
+        },
         "hf plot": {
             "command": "hf plot",
             "description": "Plots HF signal after RF signal path and A/D conversion.",
@@ -6468,8 +6515,8 @@
             "command": "hw connect",
             "description": "Connects to a Proxmark3 device via specified serial port. Baudrate here is only for physical UART or UART-BT, NOT for USB-CDC or blue shark add-on",
             "notes": [
-                "hw connect -p /dev/ttyacm0",
-                "hw connect -p /dev/ttyacm0 -b 115200"
+                "hw connect -p /dev/ttyACM0",
+                "hw connect -p /dev/ttyACM0 -b 115200"
             ],
             "offline": true,
             "options": [
@@ -10935,12 +10982,8 @@
         }
     },
     "metadata": {
-        "commands_extracted": 689,
+        "commands_extracted": 693,
         "extracted_by": "PM3Help2JSON v1.00",
-<<<<<<< HEAD
-        "extracted_on": "2022-03-29T16:50:55"
-=======
-        "extracted_on": "2022-03-29T16:48:37"
->>>>>>> 9caf62db1 (Remove force lowercasing of commands.json)
+        "extracted_on": "2022-04-09T16:34:40"
     }
 }
\ No newline at end of file
diff --git a/doc/commands.md b/doc/commands.md
index b6c2633b0..85e0395c3 100644
--- a/doc/commands.md
+++ b/doc/commands.md
@@ -581,6 +581,18 @@ Check column "offline" for their availability.
 |`hf mfdes test          `|Y       |`Regression crypto tests`
 
 
+### hf ntag424
+
+ { NXP NTAG 4242 DNA RFIDs...          }
+
+|command                  |offline |description
+|-------                  |------- |-----------
+|`hf ntag424 help        `|Y       |`This help`
+|`hf ntag424 info        `|N       |`Tag information`
+|`hf ntag424 sdm         `|N       |`Prints NDEF records from card`
+|`hf ntag424 view        `|Y       |`Display content from tag dump file`
+
+
 ### hf seos
 
  { SEOS RFIDs...                       }
diff --git a/tools/mfd_aes_brute/mfd_multi_brute.c b/tools/mfd_aes_brute/mfd_multi_brute.c
index aaad8baa3..704796255 100644
--- a/tools/mfd_aes_brute/mfd_multi_brute.c
+++ b/tools/mfd_aes_brute/mfd_multi_brute.c
@@ -43,7 +43,7 @@
 
 #if defined(__APPLE__) || defined(__MACH__)
 #else
-    #include "detectaes.h"
+#include "detectaes.h"
 #endif
 
 
@@ -383,11 +383,11 @@ int main(int argc, char *argv[]) {
     printf("Crypto algo............ " _GREEN_("%s") "\n", algostr);
     printf("LCR Random generator... " _GREEN_("%s") "\n", generators[g_idx].Name);
 
-    #if defined(__APPLE__) || defined(__MACH__)
-    #else
-        bool support_aesni = platform_aes_hw_available();
-        printf("AES-NI detected........ " _GREEN_("%s") "\n", (support_aesni) ? "yes" : "no");
-    #endif
+#if defined(__APPLE__) || defined(__MACH__)
+#else
+    bool support_aesni = platform_aes_hw_available();
+    printf("AES-NI detected........ " _GREEN_("%s") "\n", (support_aesni) ? "yes" : "no");
+#endif
 
     printf("Starting timestamp..... ");
     print_time(start_time);
