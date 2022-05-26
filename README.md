commit 6d5bb3eace740b802f20b23ee61295fe7c9e4436
Author: iceman1001 <iceman@iuse.se>
Date:   Wed Feb 16 22:56:38 2022 +0100

    fix compilation

diff --git a/tools/mfd_aes_brute/aes-ni.h b/tools/mfd_aes_brute/aes-ni.h
index 73db84d16..2e40715bd 100644
--- a/tools/mfd_aes_brute/aes-ni.h
+++ b/tools/mfd_aes_brute/aes-ni.h
@@ -2,9 +2,9 @@
 #define __AES_NI_H__
 
 #include "aes-ni.h"
-#include <stdint.h>     //for int8_t
-#include <string.h>     //for memcmp
-#include <wmmintrin.h>  //for intrinsics for AES-NI
+// #include <stdint.h>     //for int8_t
+// #include <string.h>     //for memcmp
+// #include <wmmintrin.h>  //for intrinsics for AES-NI
 //compile using gcc and following arguments: -g;-O0;-Wall;-msse2;-msse;-march=native;-maes
 
 /*
