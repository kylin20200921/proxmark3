commit a98b9590c6b3008516947b7ce93d6b2c63e61a7f
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Mar 27 11:03:13 2022 +0200

    Disabling aesni detection on OSX

diff --git a/tools/mfd_aes_brute/mfd_multi_brute.c b/tools/mfd_aes_brute/mfd_multi_brute.c
index c819415ad..9bca75642 100644
--- a/tools/mfd_aes_brute/mfd_multi_brute.c
+++ b/tools/mfd_aes_brute/mfd_multi_brute.c
@@ -374,11 +374,15 @@ int main(int argc, char *argv[]) {
 
     uint64_t start_time = atoi(argv[3]);
 
-    const bool support_aesni = platform_aes_hw_available();
-
     printf("Crypto algo............ " _GREEN_("%s") "\n", algostr);
     printf("LCR Random generator... " _GREEN_("%s") "\n", generators[g_idx].Name);
-    printf("AES-NI detected........ " _GREEN_("%s") "\n", (support_aesni) ? "yes" : "no");
+
+    #if defined(__APPLE__) || defined(__MACH__)
+    #else
+        bool support_aesni = platform_aes_hw_available();
+        printf("AES-NI detected........ " _GREEN_("%s") "\n", (support_aesni) ? "yes" : "no");
+    #endif
+
     printf("Starting timestamp..... ");
     print_time(start_time);
 
