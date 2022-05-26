commit 06ccaf555c7a9f0617c5426d0b8e5787869c99fd
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Feb 21 21:41:11 2022 +0100

    coverity cid #386958 - tainted value

diff --git a/tools/mfd_aes_brute/mfd_multi_brute.c b/tools/mfd_aes_brute/mfd_multi_brute.c
index 413c47a17..c819415ad 100644
--- a/tools/mfd_aes_brute/mfd_multi_brute.c
+++ b/tools/mfd_aes_brute/mfd_multi_brute.c
@@ -365,6 +365,13 @@ int main(int argc, char *argv[]) {
     }
 
     uint8_t  g_idx = atoi(argv[2]);
+
+    // -2 (zero index and last item is NULL);
+    if (g_idx > ARRAYLEN(generators) - 2) {
+        printf("generator index is out-of-range\n");
+        return 1;
+    }
+
     uint64_t start_time = atoi(argv[3]);
 
     const bool support_aesni = platform_aes_hw_available();
