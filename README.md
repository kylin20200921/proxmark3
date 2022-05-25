commit f6534e18bee22e0f6ec334e47e0f5d73a1842726
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jun 20 16:36:31 2021 +0200

    style

diff --git a/armsrc/desfire_crypto.c b/armsrc/desfire_crypto.c
index 782466be2..c32d6e4df 100644
--- a/armsrc/desfire_crypto.c
+++ b/armsrc/desfire_crypto.c
@@ -103,8 +103,9 @@ void tdes_nxp_send(const void *in, void *out, size_t length, const void *key, un
     uint8_t *tout = (uint8_t *) out;
 
     while (length > 0) {
-        for (i = 0; i < 8; i++)
+        for (i = 0; i < 8; i++) {
             tin[i] = (unsigned char)(tin[i] ^ iv[i]);
+        }
 
         mbedtls_des3_crypt_ecb(&ctx3, tin, tout);
 
@@ -121,8 +122,9 @@ void tdes_nxp_send(const void *in, void *out, size_t length, const void *key, un
 void Desfire_des_key_new(const uint8_t value[8], desfirekey_t key) {
     uint8_t data[8];
     memcpy(data, value, 8);
-    for (int n = 0; n < 8; n++)
-        data[n] &= 0xfe;
+    for (int n = 0; n < 8; n++) {
+        data[n] &= 0xFE;
+    }
     Desfire_des_key_new_with_version(data, key);
 }
 
@@ -138,10 +140,12 @@ void Desfire_des_key_new_with_version(const uint8_t value[8], desfirekey_t key)
 void Desfire_3des_key_new(const uint8_t value[16], desfirekey_t key) {
     uint8_t data[16];
     memcpy(data, value, 16);
-    for (int n = 0; n < 8; n++)
-        data[n] &= 0xfe;
-    for (int n = 8; n < 16; n++)
+    for (int n = 0; n < 8; n++) {
+        data[n] &= 0xFE;
+    }
+    for (int n = 8; n < 16; n++) {
         data[n] |= 0x01;
+    }
     Desfire_3des_key_new_with_version(data, key);
 }
 
@@ -156,8 +160,9 @@ void Desfire_3des_key_new_with_version(const uint8_t value[16], desfirekey_t key
 void Desfire_3k3des_key_new(const uint8_t value[24], desfirekey_t key) {
     uint8_t data[24];
     memcpy(data, value, 24);
-    for (int n = 0; n < 8; n++)
-        data[n] &= 0xfe;
+    for (int n = 0; n < 8; n++) {
+        data[n] &= 0xFE;
+    }
     Desfire_3k3des_key_new_with_version(data, key);
 }
 
@@ -194,13 +199,13 @@ uint8_t Desfire_key_get_version(desfirekey_t key) {
 void Desfire_key_set_version(desfirekey_t key, uint8_t version) {
     for (int n = 0; n < 8; n++) {
         uint8_t version_bit = ((version & (1 << (7 - n))) >> (7 - n));
-        key->data[n] &= 0xfe;
+        key->data[n] &= 0xFE;
         key->data[n] |= version_bit;
         if (key->type == T_DES) {
             key->data[n + 8] = key->data[n];
         } else {
             // Write ~version to avoid turning a 3DES key into a DES key
-            key->data[n + 8] &= 0xfe;
+            key->data[n + 8] &= 0xFE;
             key->data[n + 8] |= ~version_bit;
         }
     }
@@ -267,23 +272,32 @@ void cmac_generate_subkeys(desfirekey_t key) {
 
     // Used to compute CMAC on complete blocks
     memcpy(key->cmac_sk1, l, kbs);
+
     txor = l[0] & 0x80;
+
     lsl(key->cmac_sk1, kbs);
-    if (txor)
+
+    if (txor) {
         key->cmac_sk1[kbs - 1] ^= R;
+    }
 
     // Used to compute CMAC on the last block if non-complete
     memcpy(key->cmac_sk2, key->cmac_sk1, kbs);
+
     txor = key->cmac_sk1[0] & 0x80;
+
     lsl(key->cmac_sk2, kbs);
-    if (txor)
+
+    if (txor) {
         key->cmac_sk2[kbs - 1] ^= R;
+    }
 }
 
 void cmac(const desfirekey_t key, uint8_t *ivect, const uint8_t *data, size_t len, uint8_t *cmac) {
     int kbs = key_block_size(key);
-    if (kbs == 0)
+    if (kbs == 0) {
         return;
+    }
 
     uint8_t *buffer = BigBuf_malloc(padded_data_length(len, kbs));
 
@@ -306,8 +320,10 @@ void cmac(const desfirekey_t key, uint8_t *ivect, const uint8_t *data, size_t le
 }
 
 size_t key_block_size(const desfirekey_t key) {
-    if (key == NULL)
+    if (key == NULL) {
         return 0;
+    }
+
     size_t block_size = 8;
     switch (key->type) {
         case T_DES:
@@ -830,10 +846,12 @@ void mifare_cypher_blocks_chained(desfiretag_t tag, desfirekey_t key, uint8_t *i
     size_t block_size;
 
     if (tag) {
-        if (!key)
+        if (key == NULL) {
             key = DESFIRE(tag)->session_key;
-        if (!ivect)
+        }
+        if (ivect == NULL) {
             ivect = DESFIRE(tag)->ivect;
+        }
 
         switch (DESFIRE(tag)->authentication_scheme) {
             case AS_LEGACY:
diff --git a/client/src/mifare/desfire_crypto.c b/client/src/mifare/desfire_crypto.c
index 1cf043a7b..01c233de3 100644
--- a/client/src/mifare/desfire_crypto.c
+++ b/client/src/mifare/desfire_crypto.c
@@ -66,10 +66,14 @@ void des_decrypt(void *out, const void *in, const void *key) {
 }
 
 void tdes_nxp_receive(const void *in, void *out, size_t length, const void *key, unsigned char iv[8], int keymode) {
-    if (length % 8) return;
+    if (length % 8)
+        return;
+    
     mbedtls_des3_context ctx3;
-    if (keymode == 2) mbedtls_des3_set2key_dec(&ctx3, key);
-    else mbedtls_des3_set3key_dec(&ctx3, key);
+    if (keymode == 2)
+        mbedtls_des3_set2key_dec(&ctx3, key);
+    else
+        mbedtls_des3_set3key_dec(&ctx3, key);
 
     uint8_t i;
     unsigned char temp[8];
@@ -81,8 +85,9 @@ void tdes_nxp_receive(const void *in, void *out, size_t length, const void *key,
 
         mbedtls_des3_crypt_ecb(&ctx3, tin, tout);
 
-        for (i = 0; i < 8; i++)
+        for (i = 0; i < 8; i++) {
             tout[i] = (unsigned char)(tout[i] ^ iv[i]);
+        }
 
         memcpy(iv, temp, 8);
 
@@ -93,18 +98,24 @@ void tdes_nxp_receive(const void *in, void *out, size_t length, const void *key,
 }
 
 void tdes_nxp_send(const void *in, void *out, size_t length, const void *key, unsigned char iv[8], int keymode) {
-    if (length % 8) return;
+    if (length % 8) 
+        return;
+
     mbedtls_des3_context ctx3;
-    if (keymode == 2) mbedtls_des3_set2key_enc(&ctx3, key);
-    else mbedtls_des3_set3key_enc(&ctx3, key);
+
+    if (keymode == 2)
+        mbedtls_des3_set2key_enc(&ctx3, key);
+    else
+        mbedtls_des3_set3key_enc(&ctx3, key);
 
     uint8_t i;
     uint8_t *tin = (uint8_t *) in;
     uint8_t *tout = (uint8_t *) out;
 
     while (length > 0) {
-        for (i = 0; i < 8; i++)
+        for (i = 0; i < 8; i++) {
             tin[i] = (unsigned char)(tin[i] ^ iv[i]);
+        }
 
         mbedtls_des3_crypt_ecb(&ctx3, tin, tout);
 
@@ -120,8 +131,9 @@ void tdes_nxp_send(const void *in, void *out, size_t length, const void *key, un
 void Desfire_des_key_new(const uint8_t value[8], desfirekey_t key) {
     uint8_t data[8];
     memcpy(data, value, 8);
-    for (int n = 0; n < 8; n++)
-        data[n] &= 0xfe;
+    for (int n = 0; n < 8; n++) {
+        data[n] &= 0xFE;
+    }
     Desfire_des_key_new_with_version(data, key);
 }
 
@@ -137,10 +149,12 @@ void Desfire_des_key_new_with_version(const uint8_t value[8], desfirekey_t key)
 void Desfire_3des_key_new(const uint8_t value[16], desfirekey_t key) {
     uint8_t data[16];
     memcpy(data, value, 16);
-    for (int n = 0; n < 8; n++)
-        data[n] &= 0xfe;
-    for (int n = 8; n < 16; n++)
+    for (int n = 0; n < 8; n++) {
+        data[n] &= 0xFE;
+    }
+    for (int n = 8; n < 16; n++) {
         data[n] |= 0x01;
+    }
     Desfire_3des_key_new_with_version(data, key);
 }
 
@@ -155,8 +169,9 @@ void Desfire_3des_key_new_with_version(const uint8_t value[16], desfirekey_t key
 void Desfire_3k3des_key_new(const uint8_t value[24], desfirekey_t key) {
     uint8_t data[24];
     memcpy(data, value, 24);
-    for (int n = 0; n < 8; n++)
-        data[n] &= 0xfe;
+    for (int n = 0; n < 8; n++) {
+        data[n] &= 0xFE;
+    }
     Desfire_3k3des_key_new_with_version(data, key);
 }
 
@@ -173,7 +188,6 @@ void Desfire_aes_key_new(const uint8_t value[16], desfirekey_t key) {
 }
 
 void Desfire_aes_key_new_with_version(const uint8_t value[16], uint8_t version, desfirekey_t key) {
-
     if (key != NULL) {
         memcpy(key->data, value, 16);
         key->type = T_AES;
@@ -193,13 +207,15 @@ uint8_t Desfire_key_get_version(desfirekey_t key) {
 void Desfire_key_set_version(desfirekey_t key, uint8_t version) {
     for (int n = 0; n < 8; n++) {
         uint8_t version_bit = ((version & (1 << (7 - n))) >> (7 - n));
-        key->data[n] &= 0xfe;
+
+        key->data[n] &= 0xFE;
         key->data[n] |= version_bit;
+
         if (key->type == T_DES) {
             key->data[n + 8] = key->data[n];
         } else {
             // Write ~version to avoid turning a 3DES key into a DES key
-            key->data[n + 8] &= 0xfe;
+            key->data[n + 8] &= 0xFE;
             key->data[n + 8] |= ~version_bit;
         }
     }
@@ -268,15 +284,17 @@ void cmac_generate_subkeys(desfirekey_t key, MifareCryptoDirection direction) {
     memcpy(key->cmac_sk1, l, kbs);
     txor = l[0] & 0x80;
     lsl(key->cmac_sk1, kbs);
-    if (txor)
+    if (txor) {
         key->cmac_sk1[kbs - 1] ^= R;
+    }
 
     // Used to compute CMAC on the last block if non-complete
     memcpy(key->cmac_sk2, key->cmac_sk1, kbs);
     txor = key->cmac_sk1[0] & 0x80;
     lsl(key->cmac_sk2, kbs);
-    if (txor)
+    if (txor) {
         key->cmac_sk2[kbs - 1] ^= R;
+    }
 }
 
 void cmac(const desfirekey_t key, uint8_t *ivect, const uint8_t *data, size_t len, uint8_t *cmac) {
@@ -286,6 +304,10 @@ void cmac(const desfirekey_t key, uint8_t *ivect, const uint8_t *data, size_t le
     }
 
     uint8_t *buffer = calloc(padded_data_length(len, kbs), sizeof(uint8_t));
+    if (buffer == NULL) {
+        PrintAndLogEx(WARNING, "failed to allocate memory");
+        return;
+    }
 
     memcpy(buffer, data, len);
 
@@ -315,10 +337,17 @@ void mifare_kdf_an10922(const desfirekey_t key, const uint8_t *data, size_t len)
 
     cmac_generate_subkeys(key, MCD_SEND);
 
-    uint8_t *buffer = calloc(kbs2, sizeof(uint8_t));
+    // reserv atleast 32bytes.
+    uint8_t *buffer = calloc(len, sizeof(uint8_t));
+    if (buffer == NULL) {
+        PrintAndLogEx(WARNING, "failed to allocate memory");
+        return;
+    }
     uint8_t *ivect = calloc(kbs, sizeof(uint8_t));
-
-    memset(ivect, 0, kbs);
+    if (ivect == NULL) {
+        PrintAndLogEx(WARNING, "failed to allocate memory");
+        return;
+    }
 
     buffer[0] = 0x01;
     memcpy(&buffer[1], data, len++);
@@ -345,8 +374,9 @@ void mifare_kdf_an10922(const desfirekey_t key, const uint8_t *data, size_t len)
 }
 
 size_t key_block_size(const desfirekey_t key) {
-    if (key == NULL)
+    if (key == NULL) {
         return 0;
+    }
     size_t block_size = 8;
     switch (key->type) {
         case T_DES:
