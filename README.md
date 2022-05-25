commit 5f9d8273e6faef96562b8db2485be8bd1665b24f
Author: iceman1001 <iceman@iuse.se>
Date:   Tue Jan 11 10:54:14 2022 +0100

    fix outofbounds

diff --git a/client/src/emv/emv_pk.c b/client/src/emv/emv_pk.c
index b32c703ec..8df7a2067 100644
--- a/client/src/emv/emv_pk.c
+++ b/client/src/emv/emv_pk.c
@@ -269,23 +269,27 @@ char *emv_pk_dump_pk(const struct emv_pk *pk) {
     size_t outpos = 0;
     size_t outsize = 1048;          // should be enough
     char *out = calloc(1, outsize); // should be enough
-    if (!out)
+    if (out == NULL) {
         return NULL;
+    }
 
     size_t rc = emv_pk_write_bin(out + outpos, outsize - outpos, pk->rid, 5);
-    if (rc == 0)
+    if (rc == 0) {
         goto err;
+    }
 
     outpos += rc;
 
     rc = emv_pk_write_bin(out + outpos, outsize - outpos, &pk->index, 1);
-    if (rc == 0)
+    if (rc == 0) {
         goto err;
+    }
 
     outpos += rc;
 
-    if (outpos + 7 > outsize)
+    if (outpos + 7 >= outsize) {
         goto err;
+    }
     out[outpos++] = TOHEX((pk->expire >> 20) & 0xf);
     out[outpos++] = TOHEX((pk->expire >> 16) & 0xf);
     out[outpos++] = TOHEX((pk->expire >> 12) & 0xf);
@@ -296,13 +300,15 @@ char *emv_pk_dump_pk(const struct emv_pk *pk) {
 
     if (pk->pk_algo == PK_RSA) {
         rc = emv_pk_write_str(out + outpos, outsize - outpos, "rsa");
-        if (rc == 0)
+        if (rc == 0) {
             goto err;
+        }
         outpos += rc;
         out[outpos++] = ' ';
     } else {
-        if (outpos + 4 > outsize)
+        if (outpos + 4 >= outsize) {
             goto err;
+        }
         out[outpos++] = '?';
         out[outpos++] = '?';
         out[outpos++] = TOHEX(pk->pk_algo >> 4);
@@ -310,24 +316,28 @@ char *emv_pk_dump_pk(const struct emv_pk *pk) {
     }
 
     rc = emv_pk_write_bin(out + outpos, outsize - outpos, pk->exp, pk->elen);
-    if (rc == 0)
+    if (rc == 0) {
         goto err;
+    }
     outpos += rc;
 
     rc = emv_pk_write_bin(out + outpos, outsize - outpos, pk->modulus, pk->mlen);
-    if (rc == 0)
+    if (rc == 0) {
         goto err;
+    }
     outpos += rc;
 
     if (pk->hash_algo == HASH_SHA_1) {
         rc = emv_pk_write_str(out + outpos, outsize - outpos, "sha1");
-        if (rc == 0)
+        if (rc == 0) {
             goto err;
+        }
         outpos += rc;
         out[outpos++] = ' ';
     } else {
-        if (outpos + 4 > outsize)
+        if (outpos + 4 >= outsize) {
             goto err;
+        }
         out[outpos++] = '?';
         out[outpos++] = '?';
         out[outpos++] = TOHEX(pk->pk_algo >> 4);
@@ -336,12 +346,12 @@ char *emv_pk_dump_pk(const struct emv_pk *pk) {
 
 
     rc = emv_pk_write_bin(out + outpos, outsize - outpos, pk->hash, 20);
-    if (rc == 0)
+    if (rc == 0) {
         goto err;
-    outpos += rc;
+    }
 
+    outpos += rc;
     out[outpos - 1] = '\0';
-
     return out;
 
 err:
