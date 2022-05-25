commit 5fdd9ac392c906451bdd51033573668249f9e673
Author: iceman1001 <iceman@iuse.se>
Date:   Sat May 15 15:23:32 2021 +0200

    fix cppchecker warnings

diff --git a/client/src/cmdhfemrtd.c b/client/src/cmdhfemrtd.c
index 22ec37409..7baaaf72d 100644
--- a/client/src/cmdhfemrtd.c
+++ b/client/src/cmdhfemrtd.c
@@ -1051,8 +1051,6 @@ static bool emrtd_connect(bool *use_14b) {
 }
 
 static bool emrtd_do_auth(char *documentnumber, char *dob, char *expiry, bool BAC_available, bool *BAC, uint8_t *ssc, uint8_t *ks_enc, uint8_t *ks_mac, bool *use_14b) {
-    uint8_t response[EMRTD_MAX_FILE_SIZE] = { 0x00 };
-    int resplen = 0;
 
     // Select MRTD applet
     if (emrtd_select_file(EMRTD_P1_SELECT_BY_NAME, EMRTD_AID_MRTD, *use_14b) == false) {
@@ -1069,6 +1067,8 @@ static bool emrtd_do_auth(char *documentnumber, char *dob, char *expiry, bool BA
         // Select EF_DG1
         emrtd_select_file(EMRTD_P1_SELECT_BY_EF, dg_table[EF_DG1].fileid, *use_14b);
 
+        int resplen = 0;
+        uint8_t response[EMRTD_MAX_FILE_SIZE] = { 0x00 };
         if (emrtd_read_file(response, &resplen, NULL, NULL, NULL, false, *use_14b) == false) {
             *BAC = true;
             PrintAndLogEx(INFO, "Authentication is enforced. Will attempt external authentication.");
@@ -1090,7 +1090,6 @@ static bool emrtd_do_auth(char *documentnumber, char *dob, char *expiry, bool BA
             return false;
         }
     }
-
     return true;
 }
 
@@ -1760,6 +1759,7 @@ static int emrtd_parse_ef_sod_hashes(uint8_t *data, size_t datalen, uint8_t *has
             case 0x30: {
                 // iceman:  if these two calls fails,  feels like we should have a better check in place
                 bool res = emrtd_lds_get_data_by_tag(hashlist + offset + e_fieldlen + 1, e_datalen, hashidstr, &hashidstrlen, 0x02, 0x00, false, false, 0);
+                (void)res;
                 res = emrtd_lds_get_data_by_tag(hashlist + offset + e_fieldlen + 1, e_datalen, hash, &hashlen, 0x04, 0x00, false, false, 0);
                 (void)res;
                 if (hashlen <= 64) {
