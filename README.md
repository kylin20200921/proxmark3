commit 0f6b6cb6505fbb1c94862f785e3f481fb4ec7a01
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Jun 21 07:30:46 2021 +0200

    fix coverity CID 349331

diff --git a/client/src/mifare/desfire_crypto.c b/client/src/mifare/desfire_crypto.c
index 01c233de3..0699249ff 100644
--- a/client/src/mifare/desfire_crypto.c
+++ b/client/src/mifare/desfire_crypto.c
@@ -346,6 +346,7 @@ void mifare_kdf_an10922(const desfirekey_t key, const uint8_t *data, size_t len)
     uint8_t *ivect = calloc(kbs, sizeof(uint8_t));
     if (ivect == NULL) {
         PrintAndLogEx(WARNING, "failed to allocate memory");
+        free(buffer);
         return;
     }
 
