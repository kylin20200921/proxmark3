commit 3d8b165033c5842937a237e9e2ae265ca90aedb2
Author: iceman1001 <iceman@iuse.se>
Date:   Wed May 19 09:06:35 2021 +0200

    fix coverity CID 344398

diff --git a/client/src/cmdflashmem.c b/client/src/cmdflashmem.c
index fd701dd37..649809283 100644
--- a/client/src/cmdflashmem.c
+++ b/client/src/cmdflashmem.c
@@ -474,13 +474,13 @@ static int CmdFlashMemInfo(const char *Cmd) {
 
         // load private
         res = mbedtls_pk_parse_keyfile(&pkctx, path, NULL);
+        free(path);
         //res = mbedtls_pk_parse_public_keyfile(&pkctx, path);
         if (res == 0) {
             PrintAndLogEx(NORMAL, " ( " _GREEN_("ok") " )");
         } else {
             PrintAndLogEx(NORMAL, " ( " _RED_("fail") " )");
             mbedtls_pk_free(&pkctx);
-            free(path);
             return PM3_EFILE;
         }
 
@@ -489,7 +489,6 @@ static int CmdFlashMemInfo(const char *Cmd) {
             PrintAndLogEx(FAILED, "failed to allocate rsa context memory");
             return PM3_EMALLOC;
         }
-        free(path);
         got_private = true;
     } else {
 
