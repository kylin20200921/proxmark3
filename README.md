commit 5b803d2dd4bae4cb09682cf20c256a865c520886
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Jan 27 06:10:20 2022 +0100

    should fix thread safe call on Mingw (thanks to @gator96100)

diff --git a/tools/mfd_aes_brute/mfd_aes_brute.c b/tools/mfd_aes_brute/mfd_aes_brute.c
index a10d43f7a..4e39ea93f 100644
--- a/tools/mfd_aes_brute/mfd_aes_brute.c
+++ b/tools/mfd_aes_brute/mfd_aes_brute.c
@@ -18,6 +18,10 @@
 
 #define __STDC_FORMAT_MACROS
 
+#if !defined(_WIN32)
+    #define _POSIX_C_SOURCE 200112L  // need localtime_r()
+#endif
+
 #include <stdio.h>
 #include <stdint.h>
 #include <stdlib.h>
@@ -128,7 +132,12 @@ static void print_time(uint64_t at) {
 
     time_t t = at;
     struct tm lt;
-    (void) localtime_r(&t, &lt);
+
+#if defined(_WIN32)
+        (void)localtime_s(&lt, &t);
+#else
+        (void)localtime_r(&t, &lt);
+#endif
 
     char res[32];
     strftime(res, sizeof(res), "%Y-%m-%d %H:%M:%S", &lt);
