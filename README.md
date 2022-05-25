commit 5da59e55351fa23d768e73aecbe951ce53b7e374
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jun 20 08:40:32 2021 +0200

    fix coverity CID 349303

diff --git a/client/src/cmdhfemrtd.c b/client/src/cmdhfemrtd.c
index fb56aa4f6..45ea6887b 100644
--- a/client/src/cmdhfemrtd.c
+++ b/client/src/cmdhfemrtd.c
@@ -698,18 +698,18 @@ static bool emrtd_lds_get_data_by_tag(uint8_t *datain, size_t datainlen, uint8_t
 static bool emrtd_select_and_read(uint8_t *dataout, size_t *dataoutlen, uint16_t file, uint8_t *ks_enc, uint8_t *ks_mac, uint8_t *ssc, bool use_secure) {
     if (use_secure) {
         if (emrtd_secure_select_file_by_ef(ks_enc, ks_mac, ssc, file) == false) {
-            PrintAndLogEx(ERR, "Failed to secure select %s.", file);
+            PrintAndLogEx(ERR, "Failed to secure select %04X", file);
             return false;
         }
     } else {
         if (emrtd_select_file_by_ef(file) == false) {
-            PrintAndLogEx(ERR, "Failed to select %04X.", file);
+            PrintAndLogEx(ERR, "Failed to select %04X", file);
             return false;
         }
     }
 
     if (emrtd_read_file(dataout, dataoutlen, ks_enc, ks_mac, ssc, use_secure) == false) {
-        PrintAndLogEx(ERR, "Failed to read %04X.", file);
+        PrintAndLogEx(ERR, "Failed to read %04X", file);
         return false;
     }
     return true;
