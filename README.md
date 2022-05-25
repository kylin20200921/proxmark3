commit 8cd19d7c8786a790f4c55887acd0f63d7d484961
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 8 13:01:10 2022 +0100

    textual / style

diff --git a/client/src/cmdhfgallagher.c b/client/src/cmdhfgallagher.c
index c9b078146..ec89e75c7 100644
--- a/client/src/cmdhfgallagher.c
+++ b/client/src/cmdhfgallagher.c
@@ -30,10 +30,12 @@
 #include "cliparser.h"
 #include "ui.h"
 
-/** Application ID for the Gallagher Card Application Directory */
+static int CmdHelp(const char *cmd);
+
+// Application ID for the Gallagher Card Application Directory
 static const uint32_t CAD_AID = 0x2F81F4;
 
-/** Default MIFARE Site Key */
+// Default MIFARE site key (16 bytes)
 static const uint8_t DEFAULT_SITE_KEY[] = {
     0x31, 0x12, 0xB7, 0x38, 0xD8, 0x86, 0x2C, 0xCD,
     0x34, 0x30, 0x2E, 0xB2, 0x99, 0xAA, 0xB4, 0x56,
@@ -59,9 +61,8 @@ int hfgal_diversify_key(uint8_t *site_key, uint8_t *uid, uint8_t uid_len,
     PM3_RET_IF_ERR_WITH_MSG(res, "Failed generating Gallagher key diversification input");
 
     if (site_key == NULL) {
-        PrintAndLogEx(INFO, "hfgal_diversify_key is using default site key: %s",
-                      sprint_hex_inrow(DEFAULT_SITE_KEY, ARRAYLEN(DEFAULT_SITE_KEY)));
-        site_key = (uint8_t *) &DEFAULT_SITE_KEY;
+        PrintAndLogEx(INFO, "hfgal_diversify_key is using default site key");
+        memcpy(site_key, DEFAULT_SITE_KEY, sizeof(DEFAULT_SITE_KEY));
     }
 
     // Make temporary DesfireContext
@@ -79,6 +80,7 @@ int hfgal_diversify_key(uint8_t *site_key, uint8_t *uid, uint8_t uid_len,
  * @brief Reverses the bytes in AID. Used when parsing CLI args
  * (because Proxmark displays AIDs in reverse byte order).
  */
+// iceman todo:  use commonutil.c fct
 static void reverse_aid(uint8_t *aid) {
     uint8_t tmp = aid[0];
     aid[0] = aid[2];
@@ -89,6 +91,8 @@ static void reverse_aid(uint8_t *aid) {
  * @brief Converts a Card Application Directory format application ID to an integer.
  * Note: the CAD stores AIDs in reverse order, so this is different to DesfireAIDByteToUint().
  */
+
+// iceman todo:  use commonutil.c fct
 static uint32_t cad_aid_byte_to_uint(const uint8_t *data) {
     return data[2] + (data[1] << 8) + (data[0] << 16);
 }
@@ -97,6 +101,7 @@ static uint32_t cad_aid_byte_to_uint(const uint8_t *data) {
  * @brief Converts an integer application ID to Card Application Directory format.
  * Note: the CAD stores AIDs in reverse order, so this is different to DesfireAIDUintToByte().
  */
+// iceman todo:  use commonutil.c fct
 static void cad_aid_uint_to_byte(uint32_t aid, uint8_t *data) {
     data[2] = aid & 0xff;
     data[1] = (aid >> 8) & 0xff;
@@ -108,7 +113,8 @@ static void cad_aid_uint_to_byte(uint32_t aid, uint8_t *data) {
  * is for the specified region & facility, false otherwise.
  */
 static bool cad_facility_match(const uint8_t *entry, uint8_t region_code, uint16_t facility_code) {
-    return entry[0] == region_code && (entry[1] << 8) + entry[2] == facility_code;
+    return (entry[0] == region_code) && 
+           ((entry[1] << 8) + entry[2] == facility_code);
 }
 
 /**
@@ -142,16 +148,20 @@ static int authenticate(DesfireContext_t *ctx, bool verbose) {
 
     int res = DesfireAuthenticate(ctx, DACEV1, false);
     if (res != PM3_SUCCESS) {
-        PrintAndLogEx(ERR, "Desfire authenticate " _RED_("error")
-                      ". Result: [%d] %s", res, DesfireAuthErrorToStr(res));
+        PrintAndLogEx(ERR, "Desfire authenticate " _RED_("error") ". Result: [%d] %s",
+                       res,
+                       DesfireAuthErrorToStr(res)
+                    );
         return res;
     }
 
     if (DesfireIsAuthenticated(ctx)) {
-        if (verbose)
-            PrintAndLogEx(INFO, "Authenticated to AID %06X", ctx->selectedAID);
-    } else
+        if (verbose) {
+            PrintAndLogEx(INFO, "Authenticated to AID " _YELLOW_("%06X"), ctx->selectedAID);
+        }
+    } else {
         return 201;
+    }
 
     return PM3_SUCCESS;
 }
@@ -179,15 +189,18 @@ static bool aid_exists(DesfireContext_t *ctx, uint32_t aid, bool verbose) {
     DesfireSetCommandSet(ctx, DCCNativeISO);
 
     int res = DesfireSelectAIDHex(ctx, aid, false, 0);
-    if (res != PM3_SUCCESS && res != PM3_EAPDU_FAIL)
-        PM3_RET_ERR(false, "Select failed with error %d, assuming AID %06X "
-                      "does not exist", res, aid);
+    if (res != PM3_SUCCESS && res != PM3_EAPDU_FAIL) {
+        PM3_RET_ERR(false, "Select failed with error %d, assuming AID %06X does not exist", res, aid);
+    }
 
-    if (verbose)
-        PrintAndLogEx(INFO, "AID %06X %s", aid,
-                      res == PM3_SUCCESS ? "exists" : "does not exist");
+    if (verbose) {
+        PrintAndLogEx(INFO, "AID %06X %s",
+                      aid,
+                      res == PM3_SUCCESS ? "exists" : "does not exist"
+                    );
+    }
 
-    return res == PM3_SUCCESS;
+    return (res == PM3_SUCCESS);
 }
 
 /**
@@ -197,8 +210,9 @@ static bool aid_exists(DesfireContext_t *ctx, uint32_t aid, bool verbose) {
 static uint32_t find_available_gallagher_aid(DesfireContext_t *ctx, bool verbose) {
     for (uint8_t i = 0x0; i <= 0xB; i++) {
         uint32_t aid = 0x2081F4 | (i << 16);
-        if (!aid_exists(ctx, aid, verbose))
+        if (aid_exists(ctx, aid, verbose) == false) {
             return aid;
+        }
     }
     return 0;
 }
@@ -222,7 +236,7 @@ static int hfgal_delete_app(DesfireContext_t *ctx, uint8_t *site_key,
     res = DesfireDeleteApplication(ctx, aid);
     PM3_RET_IF_ERR_WITH_MSG(res, "Failed deleting AID %06X", aid);
 
-    PrintAndLogEx(INFO, "Successfully deleted AID %06X", aid);
+    PrintAndLogEx(INFO, "Successfully deleted AID " _YELLOW_("%06X"), aid);
     return PM3_SUCCESS;
 }
 
@@ -236,9 +250,9 @@ static int hfgal_delete_app(DesfireContext_t *ctx, uint8_t *site_key,
 static int hfgal_read_creds_app(DesfireContext_t *ctx, uint32_t aid, uint8_t *site_key,
                                 GallagherCredentials_t *creds, bool verbose) {
     // Check that card UID has been set
-    if (ctx->uidlen == 0)
-        PM3_RET_ERR(PM3_EINVARG, "Card UID must be set in DesfireContext "
-                      "(required for key diversification)");
+    if (ctx->uidlen == 0) {
+        PM3_RET_ERR(PM3_EINVARG, "Card UID must be set in DesfireContext (required for key diversification)");
+    }
 
     // Select application & authenticate
     DesfireSetKeyNoClear(ctx, 2, T_AES, site_key);
@@ -254,16 +268,23 @@ static int hfgal_read_creds_app(DesfireContext_t *ctx, uint32_t aid, uint8_t *si
     PM3_RET_IF_ERR_WITH_MSG(res, "Failed reading file 0 in AID %06X", aid);
 
     // Check file contained 16 bytes of data
-    if (read_len != 16)
-        PM3_RET_ERR(PM3_EFAILED, "Failed reading file 0 in AID %06X, expected "
-                      "16 bytes but received %d bytes", aid, read_len);
+    if (read_len != 16) {
+        PM3_RET_ERR(PM3_EFAILED, "Failed reading file 0 in AID %06X, expected 16 bytes, got %d bytes",
+                      aid,
+                      read_len
+                    );
+    }
 
     // Check second half of file is the bitwise inverse of the first half
-    for (uint8_t i = 8; i < 16; i++)
+    for (uint8_t i = 8; i < 16; i++) {
         buf[i] ^= 0xFF;
-    if (memcmp(buf, &buf[8], 8) != 0)
-        PM3_RET_ERR(PM3_EFAILED, "Invalid cardholder data in file 0 in "
-                      "AID %06X. Received %s", sprint_hex_inrow(buf, 16));
+    }
+
+    if (memcmp(buf, &buf[8], 8) != 0) {
+        PM3_RET_ERR(PM3_EFAILED, "Invalid cardholder data in file 0 in AID %06X. Received %s",
+                      sprint_hex_inrow(buf, 16)
+                    );
+    }
 
     gallagher_decode_creds(buf, creds);
 
@@ -293,7 +314,7 @@ static int hfgal_create_creds_app(DesfireContext_t *ctx, uint8_t *site_key, uint
     DesfireCryptoAlgorithm app_algo = T_AES;
     uint8_t num_keys = 3;
     uint8_t ks1 = 0x0B;
-    uint8_t ks2 = (DesfireKeyAlgoToType(app_algo) << 6) | num_keys;;
+    uint8_t ks2 = (DesfireKeyAlgoToType(app_algo) << 6) | num_keys;
 
     uint8_t data[5] = {0};
     DesfireAIDUintToByte(aid, &data[0]);
@@ -302,12 +323,11 @@ static int hfgal_create_creds_app(DesfireContext_t *ctx, uint8_t *site_key, uint
 
     DesfireSetCommMode(ctx, DCMMACed);
     res = DesfireCreateApplication(ctx, data, ARRAYLEN(data));
-    PM3_RET_IF_ERR_WITH_MSG(res, "Failed creating application %06X. "
-                              "Does it already exist?", aid);
+    PM3_RET_IF_ERR_WITH_MSG(res, "Failed creating application %06X. Does it already exist?", aid);
 
-    if (verbose)
-        PrintAndLogEx(INFO, "Created application %06X (currently has empty "
-                      "contents & blank keys)", aid);
+    if (verbose) {
+        PrintAndLogEx(INFO, "Created application " _YELLOW_("%06X") " (empty contents & blank keys)", aid);
+    }
 
     // Select the new application
     res = select_aid(ctx, aid, verbose);
@@ -321,26 +341,29 @@ static int hfgal_create_creds_app(DesfireContext_t *ctx, uint8_t *site_key, uint
         PM3_RET_IF_ERR_WITH_MSG(res, "Failed diversifying key %d for AID %06X", i, aid);
 
         PrintAndLogEx(INFO, "Diversified key %d for AID %06X: " _GREEN_("%s"),
-                      i, aid, sprint_hex_inrow(buf, ARRAYLEN(buf)));
+                      i,
+                      aid,
+                      sprint_hex_inrow(buf, ARRAYLEN(buf))
+                    );
 
         // Authenticate
         uint8_t blank_key[CRYPTO_AES128_KEY_SIZE] = {0};
         DesfireSetKeyNoClear(ctx, 0, T_AES, blank_key);
         DesfireSetKdf(ctx, MFDES_KDF_ALGO_NONE, NULL, 0);
         res = authenticate(ctx, verbose);
-        PM3_RET_IF_ERR_WITH_MSG(res, "Desfire authenticate error. Result: "
-                                  "[%d] %s", res, DesfireAuthErrorToStr(res));
+        PM3_RET_IF_ERR_WITH_MSG(res, "Desfire authenticate error. Result: [%d] %s", res, DesfireAuthErrorToStr(res));
 
         // Change key
         DesfireSetCommMode(ctx, DCMEncryptedPlain);
         res = DesfireChangeKey(ctx, false, i, app_algo, 1, buf, app_algo, blank_key, verbose);
         PM3_RET_IF_ERR_WITH_MSG(res, "Failed setting key %d for AID %06X", i, aid);
 
-        if (verbose)
-            PrintAndLogEx(INFO, "Successfully set key %d for AID %06X", i, aid);
+        if (verbose) {
+            PrintAndLogEx(INFO, "Successfully set key " _YELLOW_("%d") " for AID " _YELLOW_("%06X"), i, aid);
+        }
     }
 
-    PrintAndLogEx(INFO, "Successfully created credentials application %06X", aid);
+    PrintAndLogEx(INFO, "Successfully created credentials application " _YELLOW_("%06X"), aid);
     return PM3_SUCCESS;
 }
 
@@ -377,22 +400,23 @@ static int hfgal_create_creds_file(DesfireContext_t *ctx, uint8_t *site_key, uin
     res = DesfireCreateFile(ctx, file_type, data, ARRAYLEN(data), false);
     PM3_RET_IF_ERR_WITH_MSG(res, "Failed creating file 0 in AID %06X", aid);
 
-    if (verbose)
-        PrintAndLogEx(INFO, "Created file 0 in AID %06X (currently has empty contents)", aid);
+    if (verbose) {
+        PrintAndLogEx(INFO, "Created file 0 in AID " _YELLOW_("%06X") " (empty contents)", aid);
+    }
 
     // Create file contents (2nd half is the bitwise inverse of the encoded creds)
     uint8_t contents[16] = {0};
     gallagher_encode_creds(contents, creds);
-    for (int i = 0; i < 8; i++)
+    for (int i = 0; i < 8; i++) {
         contents[i + 8] = contents[i] ^ 0xFF;
+    }
 
     // Write file
     DesfireSetCommMode(ctx, DCMEncrypted);
     res = DesfireWriteFile(ctx, file_id, 0, ARRAYLEN(contents), contents);
     PM3_RET_IF_ERR_WITH_MSG(res, "Failed writing data to file 0 in AID %06X");
 
-    PrintAndLogEx(INFO, "Successfully wrote cardholder credentials to "
-                  "file 0 in AID %06X", aid);
+    PrintAndLogEx(INFO, "Successfully wrote cardholder credentials to file " _YELLOW_("0") " in AID " _YELLOW_("%06X"), aid);
     return PM3_SUCCESS;
 }
 
@@ -406,32 +430,37 @@ static int hfgal_create_creds_file(DesfireContext_t *ctx, uint8_t *site_key, uin
 static int hfgal_read_cad(DesfireContext_t *ctx, uint8_t *dest_buf,
                           uint8_t dest_buf_len, uint8_t *num_entries_out, bool verbose) {
     if (dest_buf_len < 3 * 36) {
-        PrintAndLogEx(ERR, "hfgal_read_cad destination buffer is incorrectly sized. "
-                      "Received length %d, must be at least %d", dest_buf_len, 3 * 36);
+        PrintAndLogEx(ERR, "hfgal_read_cad destination buffer is incorrectly sized. Received len %d, must be at least %d",
+                      dest_buf_len,
+                      3 * 36
+                    );
         return PM3_EINVARG;
     }
 
     // Get card AIDs from Card Application Directory (which contains 1 to 3 files)
     int res = select_aid(ctx, CAD_AID, verbose);
-    PM3_RET_IF_ERR_WITH_MSG(res, "Failed selecting Card Application "
-                              "Directory, does AID %06X exist?", CAD_AID);
+    PM3_RET_IF_ERR_WITH_MSG(res, "Failed selecting Card Application Directory, does AID %06X exist?", CAD_AID);
 
     // Read up to 3 files with 6x 6-byte entries each
     for (uint8_t i = 0; i < 3; i++) {
         size_t read_len;
         res = DesfireReadFile(ctx, i, 0, 36, &dest_buf[i * 36], &read_len);
-        if (res != PM3_SUCCESS && res != PM3_EAPDU_FAIL)
-            PM3_RET_ERR(res, "Failed reading file %d in Card Application "
-                          "Directory (AID %06X)", i, CAD_AID);
+        if (res != PM3_SUCCESS && res != PM3_EAPDU_FAIL) {
+            PM3_RET_ERR(res, "Failed reading file %d in Card Application Directory (AID %06X)", i, CAD_AID);
+        }
 
         // end if the last entry is NULL
-        if (memcmp(&dest_buf[36 * i + 30], "\0\0\0\0\0\0", 6) == 0) break;
+        if (memcmp(&dest_buf[36 * i + 30], "\0\0\0\0\0\0", 6) == 0) {
+            break;
+        }
     }
 
     // Count number of entries (i.e. count until we hit a NULL entry)
     uint8_t num_entries = 0;
     for (uint8_t i = 0; i < dest_buf_len; i += 6) {
-        if (memcmp(&dest_buf[i], "\0\0\0\0\0\0", 6) == 0) break;
+        if (memcmp(&dest_buf[i], "\0\0\0\0\0\0", 6) == 0) {
+            break;
+        }
         num_entries++;
     }
     *num_entries_out = num_entries;
@@ -440,10 +469,14 @@ static int hfgal_read_cad(DesfireContext_t *ctx, uint8_t *dest_buf,
         PrintAndLogEx(WARNING, "Card Application Directory is empty");
     } else if (verbose) {
         // Print what we found
+        // iceman maybe on seperate lines for easier reading.
         PrintAndLogEx(SUCCESS, "Card Application Directory contains:" NOLF);
-        for (int i = 0; i < num_entries; i++)
-            PrintAndLogEx(NORMAL, "%s %06X" NOLF, (i == 0) ? "" : ",",
-                          cad_aid_byte_to_uint(&dest_buf[i * 6 + 3]));
+        for (int i = 0; i < num_entries; i++) {
+            PrintAndLogEx(NORMAL, "%s %06X" NOLF,
+                        (i == 0) ? "" : ",",
+                        cad_aid_byte_to_uint(&dest_buf[i * 6 + 3])
+                    );
+        }
         PrintAndLogEx(NORMAL, "");
     }
 
@@ -458,8 +491,7 @@ static int hfgal_read_cad(DesfireContext_t *ctx, uint8_t *dest_buf,
 static int hfgal_create_cad(DesfireContext_t *ctx, uint8_t *site_key, bool verbose) {
     // Check that card UID has been set
     if (ctx->uidlen == 0)
-        PM3_RET_ERR(PM3_EINVARG, "Card UID must be set in DesfireContext "
-                      "(required for key diversification)");
+        PM3_RET_ERR(PM3_EINVARG, "Card UID must be set in DesfireContext (required for key div)");
 
     // Select application & authenticate
     int res = select_aid_and_authenticate(ctx, 0x000000, verbose);
@@ -478,12 +510,13 @@ static int hfgal_create_cad(DesfireContext_t *ctx, uint8_t *site_key, bool verbo
 
     DesfireSetCommMode(ctx, DCMMACed);
     res = DesfireCreateApplication(ctx, data, ARRAYLEN(data));
-    PM3_RET_IF_ERR_WITH_MSG(res, "Failed creating Card Application Directory. "
-                              "Does it already exist?", CAD_AID);
+    PM3_RET_IF_ERR_WITH_MSG(res, "Failed creating Card Application Directory. Does it already exist?", CAD_AID);
 
-    if (verbose)
-        PrintAndLogEx(INFO, "Created Card Application Directory (AID %06X, "
-                      "currently has empty contents & blank keys)", CAD_AID);
+    if (verbose) {
+        PrintAndLogEx(INFO, "Created Card Application Directory (AID " _YELLOW_("%06X") ", empty contents & blank keys)",
+                     CAD_AID
+                    );
+    }
 
     // Select application & authenticate
     uint8_t blank_key[DESFIRE_MAX_KEY_SIZE] = {0};
@@ -497,8 +530,10 @@ static int hfgal_create_cad(DesfireContext_t *ctx, uint8_t *site_key, bool verbo
     res = hfgal_diversify_key(site_key, ctx->uid, ctx->uidlen, 0, CAD_AID, buf);
     PM3_RET_IF_ERR_WITH_MSG(res, "Failed diversifying key 0 for AID %06X", CAD_AID);
 
-    PrintAndLogEx(INFO, "Diversified key 0 for CAD (AID %06X): " _GREEN_("%s"),
-                  CAD_AID, sprint_hex_inrow(buf, ARRAYLEN(buf)));
+    PrintAndLogEx(INFO, "Diversified key " _YELLOW_("0") " for CAD (AID " _YELLOW_("%06X") "): " _GREEN_("%s"),
+                  CAD_AID,
+                  sprint_hex_inrow(buf, ARRAYLEN(buf))
+                );
 
     // Change key
     DesfireSetCommMode(ctx, DCMEncryptedPlain);
@@ -506,10 +541,9 @@ static int hfgal_create_cad(DesfireContext_t *ctx, uint8_t *site_key, bool verbo
     PM3_RET_IF_ERR_WITH_MSG(res, "Failed setting key 0 for CAD");
 
     if (verbose)
-        PrintAndLogEx(INFO, "Successfully set key 0 for CAD");
+        PrintAndLogEx(INFO, "Successfully set key " _YELLOW_("0") " for CAD");
 
-    PrintAndLogEx(INFO, "Successfully created Card Application Directory "
-                  "(AID %06X)", CAD_AID);
+    PrintAndLogEx(INFO, "Successfully created Card Application Directory (AID " _YELLOW_("%06X") ")", CAD_AID);
     return PM3_SUCCESS;
 }
 
@@ -526,32 +560,38 @@ static int hfgal_add_aid_to_cad(DesfireContext_t *ctx, uint8_t *site_key, uint32
     uint8_t cad[36 * 3] = {0};
     uint8_t num_entries = 0;
     if (aid_exists(ctx, CAD_AID, false)) {
-        if (verbose)
+        if (verbose) {
             PrintAndLogEx(INFO, "Card Application Directory exists, reading entries...");
+        }
 
         int res = hfgal_read_cad(ctx, cad, ARRAYLEN(cad), &num_entries, verbose);
         PM3_RET_IF_ERR(res);
 
         // Check that there is space for the new entry
-        if (num_entries >= 18)
+        if (num_entries >= 18) {
             PM3_RET_ERR(PM3_EFATAL, "Card application directory is full");
+        }
+
     } else {
         // CAD doesn't exist, we need to create it
-        if (verbose)
+        if (verbose) {
             PrintAndLogEx(INFO, "Card Application Directory does not exist, creating it now...");
+        }
 
         int res = hfgal_create_cad(ctx, site_key, verbose);
         PM3_RET_IF_ERR(res);
     }
 
-    uint8_t file_id = num_entries / 6; // 6 entries per file
+    // 6 entries per file
+    uint8_t file_id = num_entries / 6; 
     uint8_t entry_num = num_entries % 6;
 
     // Check if facility already exists in CAD.
     for (uint8_t i = 0; i < ARRAYLEN(cad); i += 6) {
         if (cad_facility_match(&cad[i], creds->region_code, creds->facility_code))
-            PM3_RET_ERR(PM3_EFATAL, "Facility already exists in CAD, delete or "
-                          "update AID %06X instead", cad_aid_byte_to_uint(&cad[i + 3]));
+            PM3_RET_ERR(PM3_EFATAL, "Facility already exists in CAD, delete or update AID %06X",
+                           cad_aid_byte_to_uint(&cad[i + 3])
+                        );
     }
 
     // Create entry
@@ -561,9 +601,13 @@ static int hfgal_add_aid_to_cad(DesfireContext_t *ctx, uint8_t *site_key, uint32
     entry[2] = creds->facility_code & 0xFF;
     cad_aid_uint_to_byte(aid, &entry[3]);
 
-    if (verbose)
-        PrintAndLogEx(INFO, "Adding entry to CAD (position %d in file %d): %s",
-                      entry_num, file_id, sprint_hex_inrow(entry, 6));
+    if (verbose) {
+        PrintAndLogEx(INFO, "Adding entry to CAD (position " _YELLOW_("%d") " in file " _YELLOW_("%d") "): %s",
+                      entry_num,
+                      file_id,
+                      sprint_hex_inrow(entry, 6)
+                    );
+    }
 
     // Select application & authenticate
     DesfireSetKeyNoClear(ctx, 0, T_AES, site_key);
@@ -573,8 +617,9 @@ static int hfgal_add_aid_to_cad(DesfireContext_t *ctx, uint8_t *site_key, uint32
 
     // Create file if necessary
     if (entry_num == 0) {
-        if (verbose)
+        if (verbose) {
             PrintAndLogEx(INFO, "Creating new file in CAD");
+        }
 
         // Prepare create file command
         uint8_t file_type = 0; // standard data file
@@ -591,23 +636,19 @@ static int hfgal_add_aid_to_cad(DesfireContext_t *ctx, uint8_t *site_key, uint32
 
         // Create file
         res = DesfireCreateFile(ctx, file_type, data, ARRAYLEN(data), false);
-        PM3_RET_IF_ERR_WITH_MSG(res, "Failed creating file %d in CAD "
-                                  "(AID %06X)", file_id, CAD_AID);
+        PM3_RET_IF_ERR_WITH_MSG(res, "Failed creating file %d in CAD (AID %06X)", file_id, CAD_AID);
 
         if (verbose)
-            PrintAndLogEx(INFO, "Created file %d in CAD (currently has "
-                          "empty contents)", file_id);
+            PrintAndLogEx(INFO, "Created file " _YELLOW_("%d") " in CAD (empty contents)", file_id);
 
         // Write file
         res = DesfireWriteFile(ctx, file_id, 0, 36, &cad[file_id * 36]);
     } else
         // Write file
         res = DesfireWriteFile(ctx, file_id, entry_num * 6, 6, entry);
-    PM3_RET_IF_ERR_WITH_MSG(res, "Failed writing data to file %d in CAD "
-                              "(AID %06X)", file_id, CAD_AID);
+    PM3_RET_IF_ERR_WITH_MSG(res, "Failed writing data to file %d in CAD AID %06X)", file_id, CAD_AID);
 
-    PrintAndLogEx(INFO, "Successfully added new entry for %06X to the Card "
-                  "Application Directory", aid);
+    PrintAndLogEx(INFO, "Successfully added new entry for " _YELLOW_("%06X") " to the Card Application Directory", aid);
     return PM3_SUCCESS;
 }
 
@@ -629,12 +670,13 @@ static int hfgal_remove_aid_from_cad(DesfireContext_t *ctx, uint8_t *site_key,
     // Check if facility already exists in CAD
     uint8_t entry_idx;
     for (entry_idx = 0; entry_idx < num_entries; entry_idx++) {
-        if (aid > 0 && aid == cad_aid_byte_to_uint(&cad[entry_idx * 6 + 3]))
+        if (aid > 0 && aid == cad_aid_byte_to_uint(&cad[entry_idx * 6 + 3])) {
             break;
+        }
+    }
+    if (entry_idx >= num_entries) {
+        PM3_RET_ERR(PM3_EINVARG, "Specified facility or AID does not exist in the Card Application Directory");
     }
-    if (entry_idx >= num_entries)
-        PM3_RET_ERR(PM3_EINVARG, "Specified facility or AID does not exist "
-                      "in the Card Application Directory");
 
     // Remove entry (shift all entries left, then clear the last entry)
     memmove(
@@ -658,11 +700,11 @@ static int hfgal_remove_aid_from_cad(DesfireContext_t *ctx, uint8_t *site_key,
     for (uint8_t file_id = file_id_start; file_id <= file_id_stop - delete_last_file; file_id++) {
         // Write file
         res = DesfireWriteFile(ctx, file_id, 0, 36, &cad[file_id * 36]);
-        PM3_RET_IF_ERR_WITH_MSG(res, "Failed writing data to file %d in CAD "
-                                  "(AID %06X)", file_id, CAD_AID);
+        PM3_RET_IF_ERR_WITH_MSG(res, "Failed writing data to file %d in CAD (AID %06X)", file_id, CAD_AID);
 
-        if (verbose)
-            PrintAndLogEx(INFO, "Updated file %d in CAD", file_id);
+        if (verbose) {
+            PrintAndLogEx(INFO, "Updated file " _YELLOW_("%d") " in CAD", file_id);
+        }
     }
 
     // Delete empty file if necessary
@@ -673,11 +715,15 @@ static int hfgal_remove_aid_from_cad(DesfireContext_t *ctx, uint8_t *site_key,
         res = DesfireDeleteFile(ctx, file_id);
         PM3_RET_IF_ERR_WITH_MSG(res, "Failed deleting file %d from CAD (AID %06X)", file_id, CAD_AID);
 
-        if (verbose)
-            PrintAndLogEx(INFO, "Deleted unnecessary file %d from CAD (AID %06X)", file_id, CAD_AID);
+        if (verbose) {
+            PrintAndLogEx(INFO, "Deleted unnecessary file " _YELLOW_("%d") " from CAD (AID " _YELLOW_("%06X")")",
+                         file_id,
+                         CAD_AID
+                    );
+        }
     }
 
-    PrintAndLogEx(INFO, "Successfully removed %06X from the Card Application Directory", aid);
+    PrintAndLogEx(INFO, "Successfully removed " _YELLOW_("%06X") " from the Card Application Directory", aid);
     return PM3_SUCCESS;
 }
 
@@ -689,6 +735,7 @@ static int hfgal_remove_aid_from_cad(DesfireContext_t *ctx, uint8_t *site_key,
  * @param site_key MIFARE site key.
  * @param quiet Suppress error messages. Used when in continuous reader mode.
  */
+// iceman,  verbose and quiet... one should be enough.
 static int hfgal_read_card(uint32_t aid, uint8_t *site_key, bool verbose, bool quiet) {
     DropField();
     clearCommandBuffer();
@@ -719,11 +766,15 @@ static int hfgal_read_card(uint32_t aid, uint8_t *site_key, bool verbose, bool q
         uint32_t current_aid = cad_aid_byte_to_uint(&cad[i + 3]);
 
         if (verbose) {
-            if (region_code > 0 || facility_code > 0)
-                PrintAndLogEx(INFO, "Reading AID: %06X, region: %u, facility: %u",
-                              current_aid, region_code, facility_code);
-            else
-                PrintAndLogEx(INFO, "Reading AID: %06X", current_aid);
+            if (region_code > 0 || facility_code > 0) {
+                PrintAndLogEx(INFO, "Reading AID: " _YELLOW_("%06X") ", region: " _YELLOW_("%u") ", facility: " _YELLOW_("%u"),
+                              current_aid,
+                              region_code,
+                              facility_code
+                            );
+            } else {
+                PrintAndLogEx(INFO, "Reading AID: " _YELLOW_("%06X"), current_aid);
+            }
         }
 
         // Read & decode credentials
@@ -731,29 +782,34 @@ static int hfgal_read_card(uint32_t aid, uint8_t *site_key, bool verbose, bool q
         res = hfgal_read_creds_app(&dctx, current_aid, site_key, &creds, verbose);
         PM3_RET_IF_ERR_MAYBE_MSG(res, !quiet, "Failed reading card application credentials");
 
-        PrintAndLogEx(SUCCESS, "GALLAGHER (AID %06X) - Region: " _GREEN_("%u") ", Facility: " _GREEN_("%u")
-                      ", Card No.: " _GREEN_("%u") ", Issue Level: " _GREEN_("%u"), current_aid,
-                      creds.region_code, creds.facility_code, creds.card_number, creds.issue_level);
+        PrintAndLogEx(SUCCESS, "Gallagher (AID %06X) - region: " _GREEN_("%u") 
+                      ", fc: " _GREEN_("%u")
+                      ", cn: " _GREEN_("%u")
+                      ", issue level: " _GREEN_("%u"),
+                      current_aid,
+                      creds.region_code,
+                      creds.facility_code,
+                      creds.card_number,
+                      creds.issue_level
+                    );
     }
-
     return PM3_SUCCESS;
 }
 
 static int CmdGallagherReader(const char *cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf gallagher reader",
-                  "Read a Gallagher DESFire tag",
-                  "hf gallagher reader --aid 2081f4 --sitekey 00112233445566778899aabbccddeeff"
-                  " -> act as a reader that skips reading the Card Application Directory and uses a non-default site key\n"
-                  "hf gallagher reader -@ -> continuous reader mode"
+                  "Read a Gallagher DESFire tag from the Card Application Directory, CAD\n"
+                  "Specify site key is required if using non-default key\n",
+                  "hf gallagher reader -@ -> continuous reader mode\n"
+                  "hf gallagher reader --aid 2081f4 --sitekey 00112233445566778899aabbccddeeff -> skip CAD\n"
                  );
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str0(NULL, "aid",        "<hex>", "Application ID to read (3 bytes). If specified, then the Card Application Directory is not used"),
-        arg_str0(NULL, "sitekey",    "<hex>", "MIFARE site key to compute diversified keys (16 bytes, required if using non-default key)"),
+        arg_str0(NULL, "aid",        "<hex>", "Application ID to read (3 bytes). If specified, the CAD is not used"),
+        arg_str0(NULL, "sitekey",    "<hex>", "Site key to compute diversified keys (16 bytes)"),
         arg_lit0("@",  "continuous",          "Continuous reader mode"),
-
         arg_lit0(NULL, "apdu",                "Show APDU requests and responses"),
         arg_lit0("v",  "verbose",             "Verbose mode"),
         arg_param_end
@@ -763,8 +819,9 @@ static int CmdGallagherReader(const char *cmd) {
     int aid_len = 0;
     uint8_t aid_buf[3] = {0};
     CLIGetHexWithReturn(ctx, 1, aid_buf, &aid_len);
-    if (aid_len > 0 && aid_len != 3)
+    if (aid_len > 0 && aid_len != 3) {
         PM3_RET_ERR(PM3_EINVARG, "--aid must be 3 bytes");
+    }
 
     reverse_aid(aid_buf); // PM3 displays AIDs backwards
     uint32_t aid = DesfireAIDByteToUint(aid_buf);
@@ -773,47 +830,54 @@ static int CmdGallagherReader(const char *cmd) {
     uint8_t site_key[16] = {0};
     memcpy(site_key, DEFAULT_SITE_KEY, ARRAYLEN(site_key));
     CLIGetHexWithReturn(ctx, 2, site_key, &site_key_len);
-    if (site_key_len > 0 && site_key_len != 16)
+    if (site_key_len > 0 && site_key_len != 16) {
         PM3_RET_ERR(PM3_EINVARG, "--sitekey must be 16 bytes");
+    }
 
     bool continuous_mode = arg_get_lit(ctx, 3);
     SetAPDULogging(arg_get_lit(ctx, 4));
     bool verbose = arg_get_lit(ctx, 5);
     CLIParserFree(ctx);
 
-    if (!continuous_mode)
+    if (continuous_mode == false) {
         // Read single card
         return hfgal_read_card(aid, site_key, verbose, false);
+    }
 
     // Loop until <Enter> is pressed
     PrintAndLogEx(INFO, "Press " _GREEN_("<Enter>") " to exit");
-    while (!kbd_enter_pressed())
+    while (kbd_enter_pressed() == false) {
         hfgal_read_card(aid, site_key, verbose, !verbose);
+    }
     return PM3_SUCCESS;
 }
 
 static int CmdGallagherClone(const char *cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf gallagher clone",
-                  "Clone Gallagher credentials to a writable DESFire card",
+                  "Clone Gallagher credentials to a writable DESFire card\n"
+                  "Specify site key is required if using non-default key\n"
+                  "Key, lengths for the different crypto: \n"
+                  "   DES 8 bytes\n"
+                  "   2TDEA or AES 16 bytes\n"
+                  "   3TDEA 24 bytes\n"
+                  "AID id, default finds lowest available in range 0x2?81F4, where 0 <= ? <= 0xB.",
                   "hf gallagher clone --rc 1 --fc 22 --cn 3333 --il 4 --sitekey 00112233445566778899aabbccddeeff"
                  );
 
     void *argtable[] = {
         arg_param_begin,
-        arg_int0("n",  "keynum",  "<decimal>",  "Key number [default=0]"),
-        arg_str0("t",  "algo",    "<DES/2TDEA/3TDEA/AES>", "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
-        arg_str0("k",  "key",     "<hex>",      "Key for authentication to the PICC (HEX 8(DES), 16(2TDEA or AES) or 24(3TDEA) bytes)"),
-
-        arg_u64_1(NULL, "rc",      "<decimal>", "Region code. 4 bits max"),
-        arg_u64_1(NULL, "fc",      "<decimal>", "Facility code. 2 bytes max"),
-        arg_u64_1(NULL, "cn",      "<decimal>", "Card number. 3 bytes max"),
-        arg_u64_1(NULL, "il",      "<decimal>", "Issue level. 4 bits max"),
-        arg_str0(NULL,  "aid",     "<hex>",     "Application ID to write (3 bytes) [default finds lowest available in range 0x2?81F4, where 0 <= ? <= 0xB]"),
-        arg_str0(NULL,  "sitekey", "<hex>",     "MIFARE site key to compute diversified keys (16 bytes, required if using non-default key)"),
-
-        arg_lit0(NULL, "apdu",                  "Show APDU requests and responses"),
-        arg_lit0("v",  "verbose",               "Verbose mode"),
+        arg_int0("n",  "keynum",   "<dec>", "Key number [default = 0]"),
+        arg_str0("t",  "algo",     "<DES|2TDEA|3TDEA|AES>", "Crypt algo: DES, 2TDEA, 3TDEA, AES"),
+        arg_str0("k",  "key",      "<hex>", "Key for authentication to the PICC"),
+        arg_u64_1(NULL, "rc",      "<dec>", "Region code. 4 bits max"),
+        arg_u64_1(NULL, "fc",      "<dec>", "Facility code. 2 bytes max"),
+        arg_u64_1(NULL, "cn",      "<dec>", "Card number. 3 bytes max"),
+        arg_u64_1(NULL, "il",      "<dec>", "Issue level. 4 bits max"),
+        arg_str0(NULL,  "aid",     "<hex>", "Application ID to write (3 bytes)"),
+        arg_str0(NULL,  "sitekey", "<hex>", "Site key to compute diversified keys (16 bytes)"),
+        arg_lit0(NULL, "apdu",              "Show APDU requests and responses"),
+        arg_lit0("v",  "verbose",           "Verbose mode"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, cmd, argtable, false);
@@ -821,16 +885,24 @@ static int CmdGallagherClone(const char *cmd) {
     int key_num = arg_get_int_def(ctx, 1, 0);
 
     int key_algo = T_DES;
-    if (CLIGetOptionList(arg_get_str(ctx, 2), DesfireAlgoOpts, &key_algo)) return PM3_ESOFT;
+    if (CLIGetOptionList(arg_get_str(ctx, 2), DesfireAlgoOpts, &key_algo)) {
+        return PM3_ESOFT;
+    }
 
     int key_len = 0;
     uint8_t key[DESFIRE_MAX_KEY_SIZE] = {0};
     CLIGetHexWithReturn(ctx, 3, key, &key_len);
-    if (key_len && key_len != desfire_get_key_length(key_algo))
-        PM3_RET_ERR(PM3_EINVARG, "%s key must have %d bytes length instead of %d", CLIGetOptionListStr(DesfireAlgoOpts, key_algo), desfire_get_key_length(key_algo), key_len);
-    if (key_len == 0)
+    if (key_len && key_len != desfire_get_key_length(key_algo)) {
+        PM3_RET_ERR(PM3_EINVARG, "%s key must have %d bytes length instead of %d",
+                     CLIGetOptionListStr(DesfireAlgoOpts, key_algo),
+                     desfire_get_key_length(key_algo),
+                     key_len
+                    );
+    }
+    if (key_len == 0) {
         // Default to a key of all zeros
         key_len = desfire_get_key_length(key_algo);
+    }
 
     uint64_t region_code = arg_get_u64(ctx, 4); // uint4, input will be validated later
     uint64_t facility_code = arg_get_u64(ctx, 5); // uint16
@@ -842,29 +914,34 @@ static int CmdGallagherClone(const char *cmd) {
     uint32_t aid = 0;
     CLIGetHexWithReturn(ctx, 8, aid_buf, &aid_len);
     if (aid_len > 0) {
-        if (aid_len != 3)
+        if (aid_len != 3) {
             PM3_RET_ERR(PM3_EINVARG, "--aid must be 3 bytes");
+        }
         reverse_aid(aid_buf); // PM3 displays AIDs backwards
         aid = DesfireAIDByteToUint(aid_buf);
 
         // Check that the AID is in the expected range
-        if (memcmp(aid_buf, "\xF4\x81", 2) != 0 || aid_buf[2] < 0x20 || aid_buf[2] > 0x2B)
-            // TODO: this should probably be a warning, but key diversification will throw an error later even if we don't
-            PM3_RET_ERR(PM3_EINVARG, "Invalid Gallagher AID %06X, expected 2?81F4, where 0 <= ? <= 0xB", aid);
+        if ((memcmp(aid_buf, "\xF4\x81", 2) != 0) ||
+            (aid_buf[2] < 0x20) ||
+            (aid_buf[2] > 0x2B)) {
+                // TODO: this should probably be a warning, but key diversification will throw an error later even if we don't
+                PM3_RET_ERR(PM3_EINVARG, "Invalid Gallagher AID %06X, expected 2?81F4, where 0 <= ? <= 0xB", aid);
+            }
     }
 
     int site_key_len = 0;
     uint8_t site_key[16] = {0};
     memcpy(site_key, DEFAULT_SITE_KEY, ARRAYLEN(site_key));
     CLIGetHexWithReturn(ctx, 9, site_key, &site_key_len);
-    if (site_key_len > 0 && site_key_len != 16)
+    if (site_key_len > 0 && site_key_len != 16) {
         PM3_RET_ERR(PM3_EINVARG, "--sitekey must be 16 bytes");
+    }
 
     SetAPDULogging(arg_get_lit(ctx, 10));
     bool verbose = arg_get_lit(ctx, 11);
     CLIParserFree(ctx);
 
-    if (!gallagher_is_valid_creds(region_code, facility_code, card_number, issue_level))
+    if (gallagher_is_valid_creds(region_code, facility_code, card_number, issue_level) == false)
         return PM3_EINVARG;
 
     GallagherCredentials_t creds = {
@@ -886,8 +963,9 @@ static int CmdGallagherClone(const char *cmd) {
     // Find available Gallagher AID if the user did not specify one
     if (aid_len == 0) {
         aid = find_available_gallagher_aid(&dctx, verbose);
-        if (aid == 0)
+        if (aid == 0) {
             PM3_RET_ERR(PM3_EFATAL, "Could not find an available AID, card is full");
+        }
     }
 
     // Update Card Application Directory
@@ -915,15 +993,15 @@ static int CmdGallagherClone(const char *cmd) {
 static int CmdGallagherDelete(const char *cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf gallagher delete",
-                  "Delete Gallagher application from a DESFire card",
+                  "Delete Gallagher application from a DESFire card\n"
+                  "Specify site key is required if using non-default key",
                   "hf gallagher delete --aid 2081f4 --sitekey 00112233445566778899aabbccddeeff"
                  );
 
     void *argtable[] = {
         arg_param_begin,
         arg_str1(NULL, "aid",     "<hex>", "Application ID to delete (3 bytes)"),
-        arg_str0(NULL, "sitekey", "<hex>", "MIFARE site key to compute diversified keys (16 bytes, required if using non-default key)"),
-
+        arg_str0(NULL, "sitekey", "<hex>", "Site key to compute diversified keys (16 bytes)"),
         arg_lit0(NULL, "apdu",             "Show APDU requests and responses"),
         arg_lit0("v",  "verbose",          "Verbose mode"),
         arg_param_end
@@ -935,22 +1013,27 @@ static int CmdGallagherDelete(const char *cmd) {
     uint32_t aid = 0;
     CLIGetHexWithReturn(ctx, 1, aid_buf, &aid_len);
 
-    if (aid_len != 3)
+    if (aid_len != 3) {
         PM3_RET_ERR(PM3_EINVARG, "--aid must be 3 bytes");
+    }
     reverse_aid(aid_buf); // PM3 displays AIDs backwards
     aid = DesfireAIDByteToUint(aid_buf);
 
     // Check that the AID is in the expected range
-    if (memcmp(aid_buf, "\xF4\x81", 2) != 0 || aid_buf[2] < 0x20 || aid_buf[2] > 0x2B)
+    if ((memcmp(aid_buf, "\xF4\x81", 2) != 0) ||
+        (aid_buf[2] < 0x20) ||
+        (aid_buf[2] > 0x2B)) {
         // TODO: this should probably be a warning, but key diversification will throw an error later even if we don't
         PM3_RET_ERR(PM3_EINVARG, "Invalid Gallagher AID %06X, expected 2?81F4, where 0 <= ? <= 0xB", aid);
+    }
 
     int site_key_len = 0;
     uint8_t site_key[16] = {0};
     memcpy(site_key, DEFAULT_SITE_KEY, ARRAYLEN(site_key));
     CLIGetHexWithReturn(ctx, 2, site_key, &site_key_len);
-    if (site_key_len > 0 && site_key_len != 16)
+    if (site_key_len > 0 && site_key_len != 16) {
         PM3_RET_ERR(PM3_EINVARG, "--sitekey must be 16 bytes");
+    }
 
     SetAPDULogging(arg_get_lit(ctx, 3));
     bool verbose = arg_get_lit(ctx, 4);
@@ -981,18 +1064,18 @@ static int CmdGallagherDelete(const char *cmd) {
 static int CmdGallagherDiversify(const char *cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf gallagher diversify",
-                  "Diversify Gallagher key",
+                  "Diversify Gallagher key\n"
+                  "Specify site key is required if using non-default key",
                   "hf gallagher diversify --uid 11223344556677 --aid 2081f4"
                  );
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str1(NULL,  "aid",     "<hex>",     "Application ID for diversification (3 bytes)"),
-        arg_int0(NULL,  "keynum",  "<decimal>", "Key number [default=0]"),
-        arg_str0(NULL,  "uid",     "<hex>",     "Card UID to delete (4 or 7 bytes)"),
-        arg_str0(NULL,  "sitekey", "<hex>",     "MIFARE site key to compute diversified keys (16 bytes, required if using non-default key)"),
-
-        arg_lit0(NULL, "apdu",                  "Show APDU requests and responses"),
+        arg_str1(NULL,  "aid",     "<hex>", "Application ID for diversification (3 bytes)"),
+        arg_int0(NULL,  "keynum",  "<dec>", "Key number [default = 0]"),
+        arg_str0(NULL,  "uid",     "<hex>", "Card UID to delete (4 or 7 bytes)"),
+        arg_str0(NULL,  "sitekey", "<hex>", "Site key to compute diversified keys (16 bytes)"),
+        arg_lit0(NULL, "apdu",              "Show APDU requests and responses"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, cmd, argtable, false);
@@ -1002,30 +1085,37 @@ static int CmdGallagherDiversify(const char *cmd) {
     uint32_t aid = 0;
     CLIGetHexWithReturn(ctx, 1, aid_buf, &aid_len);
 
-    if (aid_len != 3)
+    if (aid_len != 3) {
         PM3_RET_ERR(PM3_EINVARG, "--aid must be 3 bytes");
+    }
+
     reverse_aid(aid_buf); // PM3 displays AIDs backwards
     aid = DesfireAIDByteToUint(aid_buf);
 
     // Check that the AID is in the expected range
-    if (memcmp(aid_buf, "\xF4\x81", 2) != 0 || aid_buf[2] < 0x20 || aid_buf[2] > 0x2B)
+    if ((memcmp(aid_buf, "\xF4\x81", 2) != 0) || 
+        (aid_buf[2] < 0x20) || 
+        (aid_buf[2] > 0x2B)) {
         // TODO: this should probably be a warning, but key diversification will throw an error later even if we don't
         PM3_RET_ERR(PM3_EINVARG, "Invalid Gallagher AID %06X, expected 2?81F4, where 0 <= ? <= 0xB", aid);
+    }
 
     int key_num = arg_get_int_def(ctx, 2, 0);
 
     int uid_len = 0;
     uint8_t uid[7] = {0};
     CLIGetHexWithReturn(ctx, 3, uid, &uid_len);
-    if (uid_len > 0 && uid_len != 4 && uid_len != 7)
+    if (uid_len > 0 && uid_len != 4 && uid_len != 7) {
         PM3_RET_ERR(PM3_EINVARG, "--uid must be 4 or 7 bytes");
+    }
 
     int site_key_len = 0;
     uint8_t site_key[16] = {0};
     memcpy(site_key, DEFAULT_SITE_KEY, ARRAYLEN(site_key));
     CLIGetHexWithReturn(ctx, 4, site_key, &site_key_len);
-    if (site_key_len > 0 && site_key_len != 16)
+    if (site_key_len > 0 && site_key_len != 16) {
         PM3_RET_ERR(PM3_EINVARG, "--sitekey must be 16 bytes");
+    }
 
     SetAPDULogging(arg_get_lit(ctx, 5));
     CLIParserFree(ctx);
@@ -1052,15 +1142,16 @@ static int CmdGallagherDiversify(const char *cmd) {
     char *key_str = sprint_hex_inrow(key, ARRAYLEN(key));
     PrintAndLogEx(SUCCESS, "Successfully diversified key: " _GREEN_("%s"), key_str);
 
-    if (IfPm3Iso14443())
-        PrintAndLogEx(HINT, "Hint: try "
-                      _YELLOW_("`hf mfdes auth --aid %06X --keyno %d --algo AES --key %s`")
-                      " to verify", aid, key_num, key_str);
+    if (IfPm3Iso14443()) {
+        PrintAndLogEx(HINT, "Hint: try " _YELLOW_("`hf mfdes auth --aid %06X --keyno %d --algo AES --key %s`") " to verify",
+                      aid,
+                      key_num,
+                      key_str
+                    );
+    }
     return PM3_SUCCESS;
 }
 
-static int CmdHelp(const char *cmd);
-
 static command_t CommandTable[] = {
     {"help",         CmdHelp,               AlwaysAvailable, "This help"},
     {"reader",       CmdGallagherReader,    IfPm3Iso14443,   "Read & decode all Gallagher credentials on a DESFire card"},
