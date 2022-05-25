commit eaf968092c2008aad6a7ee3ea0c969974b77d046
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 8 13:14:53 2022 +0100

    coverity fixes

diff --git a/client/src/cmdhfgallagher.c b/client/src/cmdhfgallagher.c
index 5dbc3c4b8..feb61ba4c 100644
--- a/client/src/cmdhfgallagher.c
+++ b/client/src/cmdhfgallagher.c
@@ -251,7 +251,7 @@ static int hfgal_read_creds_app(DesfireContext_t *ctx, uint32_t aid, uint8_t *si
                                 GallagherCredentials_t *creds, bool verbose) {
     // Check that card UID has been set
     if (ctx->uidlen == 0) {
-        PM3_RET_ERR(PM3_EINVARG, "Card UID must be set in DesfireContext (required for key diversification)");
+        PM3_RET_ERR(PM3_EINVARG, "Card UID must be set in DesfireContext (required for key div)");
     }
 
     // Select application & authenticate
@@ -269,7 +269,7 @@ static int hfgal_read_creds_app(DesfireContext_t *ctx, uint32_t aid, uint8_t *si
 
     // Check file contained 16 bytes of data
     if (read_len != 16) {
-        PM3_RET_ERR(PM3_EFAILED, "Failed reading file 0 in AID %06X, expected 16 bytes, got %d bytes",
+        PM3_RET_ERR(PM3_EFAILED, "Failed reading file 0 in AID %06X, expected 16 bytes, got %zu bytes",
                       aid,
                       read_len
                     );
@@ -282,6 +282,7 @@ static int hfgal_read_creds_app(DesfireContext_t *ctx, uint32_t aid, uint8_t *si
 
     if (memcmp(buf, &buf[8], 8) != 0) {
         PM3_RET_ERR(PM3_EFAILED, "Invalid cardholder data in file 0 in AID %06X. Received %s",
+                      aid,
                       sprint_hex_inrow(buf, 16)
                     );
     }
@@ -414,7 +415,7 @@ static int hfgal_create_creds_file(DesfireContext_t *ctx, uint8_t *site_key, uin
     // Write file
     DesfireSetCommMode(ctx, DCMEncrypted);
     res = DesfireWriteFile(ctx, file_id, 0, ARRAYLEN(contents), contents);
-    PM3_RET_IF_ERR_WITH_MSG(res, "Failed writing data to file 0 in AID %06X");
+    PM3_RET_IF_ERR_WITH_MSG(res, "Failed writing data to file 0 in AID %06X", aid);
 
     PrintAndLogEx(INFO, "Successfully wrote cardholder credentials to file " _YELLOW_("0") " in AID " _YELLOW_("%06X"), aid);
     return PM3_SUCCESS;
@@ -510,7 +511,7 @@ static int hfgal_create_cad(DesfireContext_t *ctx, uint8_t *site_key, bool verbo
 
     DesfireSetCommMode(ctx, DCMMACed);
     res = DesfireCreateApplication(ctx, data, ARRAYLEN(data));
-    PM3_RET_IF_ERR_WITH_MSG(res, "Failed creating Card Application Directory. Does it already exist?", CAD_AID);
+    PM3_RET_IF_ERR_WITH_MSG(res, "Failed creating Card Application Directory (AID " _YELLOW_("%06X")"). Does it already exist?", CAD_AID);
 
     if (verbose) {
         PrintAndLogEx(INFO, "Created Card Application Directory (AID " _YELLOW_("%06X") ", empty contents & blank keys)",
diff --git a/client/src/cmdhfksx6924.c b/client/src/cmdhfksx6924.c
index 8a9ec747a..d405459c9 100644
--- a/client/src/cmdhfksx6924.c
+++ b/client/src/cmdhfksx6924.c
@@ -56,7 +56,7 @@ static int get_and_print_balance(void) {
         return PM3_ESOFT;
     }
 
-    PrintAndLogEx(SUCCESS, "Current balance: " _YELLOW_("%ld") " won/cents", balance);
+    PrintAndLogEx(SUCCESS, "Current balance: " _YELLOW_("%u") " won/cents", balance);
     return PM3_SUCCESS;
 }
 
