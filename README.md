commit 7b34b210b4ba6f0dd6d16f7c9fb2e278a25896aa
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 8 15:02:16 2022 +0100

    cppcheck fixes

diff --git a/tools/hitag2crack/crack5opencl/dolphin_macro.h b/tools/hitag2crack/crack5opencl/dolphin_macro.h
index ea9708bab..8fca6907c 100644
--- a/tools/hitag2crack/crack5opencl/dolphin_macro.h
+++ b/tools/hitag2crack/crack5opencl/dolphin_macro.h
@@ -27,12 +27,76 @@ License: GNU General Public License v3 or any later version (see LICENSE.txt)
 // too many allocations, too many free to manage, I need dolphin macros :)
 // they could be buggy, but if you know how to fix them, do it
 
-#define MEMORY_FREE_ADD(a)      { freeList[freeListIdx++] = (void *)(a); }
-#define MEMORY_FREE_ALL         { int t=freeListIdx; while (t-- > 0) if (freeList[t]!=NULL) { free (freeList[t]); freeList[t]=NULL; } if (freeList!=NULL) { free (freeList); freeList=NULL; } }
-#define MEMORY_FREE_DEL(a)      { for (int i=0;i<freeListIdx;i++) { if(freeList[i] && a==freeList[i]) { free(freeList[i]); freeList[i]=NULL; break; } } }
-#define MEMORY_FREE_LIST(a,i)   { if (i > 0) { int t=(int)i; do { if (a[t]!=NULL) { free(a[t]); a[t]=NULL; } } while (--t >= 0); MEMORY_FREE_DEL(a) } }
-#define MEMORY_FREE_LIST_Z(a,i) { int t=(int)i; do { if (a[t]!=NULL) { free(a[t]); a[t]=NULL; } } while (--t >= 0); MEMORY_FREE_DEL(a) }
-#define MEMORY_FREE_OPENCL(c,i) { int t=(int)i; do { if (c.contexts[t]) clReleaseContext (c.contexts[t]); if (c.keystreams[t]) clReleaseMemObject (c.keystreams[t]); \
-                                  if (c.candidates[t]) clReleaseMemObject (c.candidates[t]); if (c.matches[t]) clReleaseMemObject (c.matches[t]); \
-                                  if (c.matches_found[t]) clReleaseMemObject (c.matches_found[t]); if (c.commands[t]) clReleaseCommandQueue (c.commands[t]); \
-                                  if (c.kernels[t]) clReleaseKernel (c.kernels[t]); if (c.programs[t]) clReleaseProgram (c.programs[t]); } while (--t >= 0); }
+#define MEMORY_FREE_ADD(a)      { \
+    freeList[freeListIdx++] = (void *)(a); \
+}
+
+#define MEMORY_FREE_ALL         { \
+    int t = freeListIdx; \
+    while (t-- > 0) { \
+        if (freeList[t] != NULL) { \
+            free (freeList[t]); \
+            freeList[t] = NULL; \
+        }\
+        if (freeList != NULL) { \
+            free (freeList); \
+            freeList = NULL; \
+        } \
+    } \
+}
+
+#define MEMORY_FREE_DEL(a)      { \
+    for (int i = 0; i < freeListIdx; i++) { \
+        if (freeList[i] && a == freeList[i]) { \
+            free(freeList[i]); \
+            freeList[i] = NULL; \
+            break; \
+        } \
+    } \
+}
+
+#define MEMORY_FREE_LIST(a,i)   { \
+    if (i > 0) { \
+        int t=(int)i; \
+        do { \
+            if (a[t] != NULL) { \
+                free(a[t]); \
+                a[t]=NULL; \
+            } \
+        } while (--t >= 0); \
+        MEMORY_FREE_DEL(a) \
+    } \
+}
+
+#define MEMORY_FREE_LIST_Z(a,i) { \
+    int t = (int)i; \
+    do { \
+        if (a[t] != NULL) { \
+            free(a[t]); \
+            a[t] = NULL; \
+        } \
+    } while (--t >= 0); \
+    MEMORY_FREE_DEL(a) \
+}
+
+#define MEMORY_FREE_OPENCL(c,i) { \
+    int t = (int)i; \
+    do { \
+        if (c.contexts[t]) \
+            clReleaseContext (c.contexts[t]); \
+        if (c.keystreams[t]) \
+            clReleaseMemObject (c.keystreams[t]); \
+        if (c.candidates[t]) \
+            clReleaseMemObject (c.candidates[t]); \
+        if (c.matches[t]) \
+            clReleaseMemObject (c.matches[t]); \
+        if (c.matches_found[t]) \
+            clReleaseMemObject (c.matches_found[t]); \
+        if (c.commands[t]) \
+            clReleaseCommandQueue (c.commands[t]); \
+        if (c.kernels[t]) \
+            clReleaseKernel (c.kernels[t]); \
+        if (c.programs[t]) \
+            clReleaseProgram (c.programs[t]); \
+    } while (--t >= 0); \
+ }
diff --git a/tools/hitag2crack/crack5opencl/hitag2.h b/tools/hitag2crack/crack5opencl/hitag2.h
index 964482c58..e53e98381 100644
--- a/tools/hitag2crack/crack5opencl/hitag2.h
+++ b/tools/hitag2crack/crack5opencl/hitag2.h
@@ -7,11 +7,11 @@
 // as the HITAG2 original implementation, with some minor changes
 
 #define i4(x,a,b,c,d) ((uint32_t)((((x)>>(a))&1)<<3)|(((x)>>(b))&1)<<2|(((x)>>(c))&1)<<1|(((x)>>(d))&1))
-#define f(state) ((0xdd3929b >> ( (((0x3c65 >> i4(state, 2, 3, 5, 6) ) & 1) <<4) \
+#define f(state) ((uint32_t)((0xdd3929b >> ( (((0x3c65 >> i4(state, 2, 3, 5, 6) ) & 1) <<4) \
                                 | ((( 0xee5 >> i4(state, 8,12,14,15) ) & 1) <<3) \
                                 | ((( 0xee5 >> i4(state,17,21,23,26) ) & 1) <<2) \
                                 | ((( 0xee5 >> i4(state,28,29,31,33) ) & 1) <<1) \
-                                | (((0x3c65 >> i4(state,34,43,44,46) ) & 1) ))) & 1)
+                                | (((0x3c65 >> i4(state,34,43,44,46) ) & 1) ))) & 1))
 
 #define get_bit(n, word) ((word >> (n)) & 1)
 
diff --git a/tools/hitag2crack/crack5opencl/ht2crack5opencl.c b/tools/hitag2crack/crack5opencl/ht2crack5opencl.c
index 9afeeddfe..74fbd3764 100644
--- a/tools/hitag2crack/crack5opencl/ht2crack5opencl.c
+++ b/tools/hitag2crack/crack5opencl/ht2crack5opencl.c
@@ -53,7 +53,7 @@
 typedef unsigned int __attribute__((aligned(VECTOR_SIZE))) __attribute__((vector_size(VECTOR_SIZE))) bitslice_value_t;
 typedef union {
     bitslice_value_t value;
-    uint8_t bytes[MAX_BITSLICES / 8];
+    uint8_t bytes[VECTOR_SIZE];
 } bitslice_t;
 
 static bitslice_t keystream[32];
@@ -94,8 +94,8 @@ static uint64_t expand(uint64_t mask, uint64_t value) {
 static void bitslice(const uint64_t value, bitslice_t *restrict bitsliced_value) {
     // set constants
     bitslice_t bs_zeroes, bs_ones;
-    memset(bs_ones.bytes, 0xff, VECTOR_SIZE);
-    memset(bs_zeroes.bytes, 0x00, VECTOR_SIZE);
+    memset((uint8_t*)&bs_ones, 0xff, VECTOR_SIZE);
+    memset((uint8_t*)&bs_zeroes, 0x00, VECTOR_SIZE);
 
     for (size_t bit_idx = 0; bit_idx < 32; bit_idx++) {
         const bool bit = get_bit(32 - 1 - bit_idx, value);
