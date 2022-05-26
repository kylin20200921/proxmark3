commit cce9edcd0fe46a5c6e047bbe5b67a64162ec9897
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Mar 27 11:05:49 2022 +0200

    Disabling aesni detection on OSX

diff --git a/tools/mfd_aes_brute/mfd_multi_brute.c b/tools/mfd_aes_brute/mfd_multi_brute.c
index 9bca75642..aaad8baa3 100644
--- a/tools/mfd_aes_brute/mfd_multi_brute.c
+++ b/tools/mfd_aes_brute/mfd_multi_brute.c
@@ -37,10 +37,16 @@
 #include <unistd.h>
 //#include <mbedtls/aes.h>
 #include "util_posix.h"
-#include "aes-ni.h"
-#include "detectaes.h"
 #include "randoms.h"
 
+#include "aes-ni.h"
+
+#if defined(__APPLE__) || defined(__MACH__)
+#else
+    #include "detectaes.h"
+#endif
+
+
 #define AEND  "\x1b[0m"
 #define _RED_(s) "\x1b[31m" s AEND
 #define _GREEN_(s) "\x1b[32m" s AEND
