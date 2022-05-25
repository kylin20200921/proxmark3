commit 5ff043e5abc779b52931fa117c78465a68dbe4f6
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 8 15:28:36 2022 +0100

    null key fix

diff --git a/client/src/cmdhfgallagher.c b/client/src/cmdhfgallagher.c
index feb61ba4c..fb7e6e46a 100644
--- a/client/src/cmdhfgallagher.c
+++ b/client/src/cmdhfgallagher.c
@@ -60,14 +60,17 @@ int hfgal_diversify_key(uint8_t *site_key, uint8_t *uid, uint8_t uid_len,
     int res = mfdes_kdf_input_gallagher(uid, uid_len, key_num, aid, key_output, &kdf_input_len);
     PM3_RET_IF_ERR_WITH_MSG(res, "Failed generating Gallagher key diversification input");
 
+    uint8_t key[sizeof(DEFAULT_SITE_KEY)];
     if (site_key == NULL) {
         PrintAndLogEx(INFO, "hfgal_diversify_key is using default site key");
-        memcpy(site_key, DEFAULT_SITE_KEY, sizeof(DEFAULT_SITE_KEY));
+        memcpy(key, DEFAULT_SITE_KEY, sizeof(key));
+    } else {
+        memcpy(key, site_key, sizeof(key));
     }
 
     // Make temporary DesfireContext
     DesfireContext_t dctx = {0};
-    DesfireSetKey(&dctx, 0, T_AES, site_key);
+    DesfireSetKey(&dctx, 0, T_AES, key);
 
     // Diversify input & copy to output buffer
     MifareKdfAn10922(&dctx, DCOMasterKey, key_output, kdf_input_len);
