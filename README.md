commit 7e9067b176a4b4d13b4a2006fd447b1305c9b4e9
Author: iceman1001 <iceman@iuse.se>
Date:   Wed Jan 26 06:15:08 2022 +0100

    style

diff --git a/client/src/cmdlfgallagher.c b/client/src/cmdlfgallagher.c
index 394669890..0c900e972 100644
--- a/client/src/cmdlfgallagher.c
+++ b/client/src/cmdlfgallagher.c
@@ -210,15 +210,15 @@ static int CmdGallagherClone(const char *Cmd) {
     uint64_t issue_level = arg_get_u64_def(ctx, 7, -1); // uint4
     CLIParserFree(ctx);
 
-    bool use_raw = raw_len > 0;
-
     if (q5 && em) {
         PrintAndLogEx(FAILED, "Can't specify both Q5 and EM4305 at the same time");
         return PM3_EINVARG;
     }
 
+    bool use_raw = (raw_len > 0);
+
     if (region_code == -1 && facility_code == -1 && card_number == -1 && issue_level == -1) {
-        if (!use_raw) {
+        if (use_raw == false) {
             PrintAndLogEx(FAILED, "Must specify either raw data to clone, or rc/fc/cn/il");
             return PM3_EINVARG;
         }
@@ -228,7 +228,7 @@ static int CmdGallagherClone(const char *Cmd) {
             PrintAndLogEx(FAILED, "Can't specify both raw and rc/fc/cn/il at the same time");
             return PM3_EINVARG;
         }
-        if (!gallagher_is_valid_creds(region_code, facility_code, card_number, issue_level)) {
+        if (gallagher_is_valid_creds(region_code, facility_code, card_number, issue_level) == false) {
             return PM3_EINVARG;
         }
     }
@@ -252,6 +252,7 @@ static int CmdGallagherClone(const char *Cmd) {
     //Pac - compat mode, NRZ, data rate 40, 3 data blocks
     blocks[0] = T55x7_MODULATION_MANCHESTER | T55x7_BITRATE_RF_32 | 3 << T55x7_MAXBLOCK_SHIFT;
     char cardtype[16] = {"T55x7"};
+
     // Q5
     if (q5) {
         blocks[0] = T5555_FIXED | T5555_MODULATION_MANCHESTER | T5555_SET_BITRATE(32) | 3 << T5555_MAXBLOCK_SHIFT;
@@ -265,7 +266,9 @@ static int CmdGallagherClone(const char *Cmd) {
     }
 
     PrintAndLogEx(INFO, "Preparing to clone Gallagher to " _YELLOW_("%s") " from %s.",
-                  cardtype, use_raw ? "raw hex" : "specified data");
+                cardtype,
+                use_raw ? "raw hex" : "specified data"
+                );
     print_blocks(blocks,  ARRAYLEN(blocks));
 
     if (em) {
@@ -318,7 +321,7 @@ static int CmdGallagherSim(const char *Cmd) {
     bool use_raw = raw_len > 0;
 
     if (region_code == -1 && facility_code == -1 && card_number == -1 && issue_level == -1) {
-        if (!use_raw) {
+        if (use_raw == false) {
             PrintAndLogEx(FAILED, "Must specify either raw data to clone, or rc/fc/cn/il");
             return PM3_EINVARG;
         }
@@ -328,12 +331,12 @@ static int CmdGallagherSim(const char *Cmd) {
             PrintAndLogEx(FAILED, "Can't specify both raw and rc/fc/cn/il at the same time");
             return PM3_EINVARG;
         }
-        if (!gallagher_is_valid_creds(region_code, facility_code, card_number, issue_level)) {
+        if (gallagher_is_valid_creds(region_code, facility_code, card_number, issue_level) == false) {
             return PM3_EINVARG;
         }
     }
 
-    if (!use_raw) {
+    if (use_raw == false) {
         // generate Gallagher data
         GallagherCredentials_t creds = {
             .region_code = region_code,
