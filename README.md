commit df083bffb8e48fbd7a00e3b82838e35d868bd7ad
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Feb 3 18:35:29 2022 +0100

    remove cflags

diff --git a/tools/mfd_aes_brute/Makefile b/tools/mfd_aes_brute/Makefile
index 968610723..6262ee1cb 100644
--- a/tools/mfd_aes_brute/Makefile
+++ b/tools/mfd_aes_brute/Makefile
@@ -1,7 +1,7 @@
 MYSRCPATHS = ../../common ../../common/mbedtls
 MYSRCS = util_posix.c
 MYINCLUDES =  -I../../include -I../../common -I../../common/mbedtls
-MYCFLAGS = -march=native -Ofast -msse2 -msse -maes
+MYCFLAGS = -march=native -Ofast
 MYDEFS =
 MYLDLIBS = -lcrypto
 
