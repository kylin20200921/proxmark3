commit c754122a74156ce8ebe8572fca71f653ec3b9a44
Author: iceman1001 <iceman@iuse.se>
Date:   Tue Jan 11 08:56:59 2022 +0100

    coverity fix CID #370724

diff --git a/client/src/emv/emv_pk.c b/client/src/emv/emv_pk.c
index e3f871fd7..b32c703ec 100644
--- a/client/src/emv/emv_pk.c
+++ b/client/src/emv/emv_pk.c
@@ -267,7 +267,7 @@ static size_t emv_pk_write_str(char *out, size_t outlen, const char *str) {
 
 char *emv_pk_dump_pk(const struct emv_pk *pk) {
     size_t outpos = 0;
-    size_t outsize = 1024; // should be enough
+    size_t outsize = 1048;          // should be enough
     char *out = calloc(1, outsize); // should be enough
     if (!out)
         return NULL;
