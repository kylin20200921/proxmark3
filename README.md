commit ce74a25d064639e6961aeb2ef3be221eba366ece
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 29 15:12:42 2022 +0100

    use macros instead

diff --git a/tools/mfd_aes_brute/brute_key.c b/tools/mfd_aes_brute/brute_key.c
index 00db53074..340ff797b 100644
--- a/tools/mfd_aes_brute/brute_key.c
+++ b/tools/mfd_aes_brute/brute_key.c
@@ -25,10 +25,12 @@
 #include <stdio.h>
 #include <stdint.h>
 #include <stdlib.h>
+#include <ctype.h>
 #include <limits.h>
+#include <string.h>
 #include <openssl/evp.h>
 #include <openssl/err.h>
-#include <string.h>
+
 
 uint32_t seed = 0;
 
@@ -149,7 +151,7 @@ int main (int argc, char* argv[]) {
         if (dec_tag[15] != dec_rdr[30]) continue;
 
 
-        printf("\btimestamp: %lu\nkey: ", timestamp);
+        printf("\btimestamp: %" PRIu64 "\nkey: ", timestamp);
         for (int i = 0; i < 16; i++) {
             printf("%02x", key[i]);
         }
