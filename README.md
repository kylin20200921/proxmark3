commit 09dc29dd9d5fbe319f3814a2ca75f203cef1a8fa
Author: iceman1001 <iceman@iuse.se>
Date:   Sun May 16 23:03:40 2021 +0200

    fix cppchecker, lesser scope

diff --git a/client/src/cmdhficlass.c b/client/src/cmdhficlass.c
index f30b54e95..dc4d16a33 100644
--- a/client/src/cmdhficlass.c
+++ b/client/src/cmdhficlass.c
@@ -1217,11 +1217,10 @@ static int CmdHFiClassDecrypt(const char *Cmd) {
     mbedtls_des3_context ctx;
     mbedtls_des3_set2key_dec(&ctx, key);
 
-    uint8_t dec_data[8] = {0};
-
     // decrypt user supplied data
     if (have_data) {
 
+        uint8_t dec_data[8] = {0};
         if (use_sc) {
             Decrypt(enc_data, dec_data);
         } else {
