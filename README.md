commit 0a5ae04070aac6870bba5520a63cd6c121f7aaf8
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 8 13:27:22 2022 +0100

    coverity fixes

diff --git a/client/src/cmdlfzx8211.c b/client/src/cmdlfzx8211.c
index ec98745b3..92c674d06 100644
--- a/client/src/cmdlfzx8211.c
+++ b/client/src/cmdlfzx8211.c
@@ -77,7 +77,7 @@ int demodzx(bool verbose) {
 
     // test checksums
 
-    PrintAndLogEx(SUCCESS, "ZX8211 - Card " _GREEN_("%u") ", Raw: %08X", raw1);
+    PrintAndLogEx(SUCCESS, "ZX8211 - Card " _GREEN_("%u"), raw1);
     return PM3_SUCCESS;
 }
 
diff --git a/client/src/ksx6924/ksx6924core.c b/client/src/ksx6924/ksx6924core.c
index 02ca39549..2748ea044 100644
--- a/client/src/ksx6924/ksx6924core.c
+++ b/client/src/ksx6924/ksx6924core.c
@@ -330,7 +330,7 @@ void KSX6924PrintPurseInfo(const struct ksx6924_purse_info *purseInfo) {
     PrintAndLogEx(INFO, "  idCenter (issuer ID) .................. %02x ( %s )", purseInfo->idCenter,
                   KSX6924LookupTMoneyIDCenter(purseInfo->idCenter, KSX6924_UNKNOWN));
     PrintAndLogEx(INFO, "  CSN (card number) ..................... %s", purseInfo->csn);
-    PrintAndLogEx(INFO, "  idtr (card usage authentication ID) ... %i", purseInfo->idtr);
+    PrintAndLogEx(INFO, "  idtr (card usage authentication ID) ... %" PRIu64, purseInfo->idtr);
     PrintAndLogEx(INFO, "  issue date ............................ %04i-%02i-%02i",
                   purseInfo->issueDate.year,
                   purseInfo->issueDate.month,
@@ -343,9 +343,9 @@ void KSX6924PrintPurseInfo(const struct ksx6924_purse_info *purseInfo) {
                   KSX6924LookupTMoneyUserCode(purseInfo->userCode, KSX6924_UNKNOWN));
     PrintAndLogEx(INFO, "  disRate (discount type) ............... %02x ( %s )", purseInfo->disRate,
                   KSX6924LookupTMoneyDisRate(purseInfo->disRate, KSX6924_UNKNOWN));
-    PrintAndLogEx(INFO, "  balMax (in won/cents) ................. %ld", purseInfo->balMax);
+    PrintAndLogEx(INFO, "  balMax (in won/cents) ................. %" PRIu32, purseInfo->balMax);
     PrintAndLogEx(INFO, "  bra (branch code) ..................... %04x", purseInfo->bra);
-    PrintAndLogEx(INFO, "  mmax (one-time transaction limit) ..... %ld", purseInfo->mmax);
+    PrintAndLogEx(INFO, "  mmax (one-time transaction limit) ..... %" PRIu32, purseInfo->mmax);
     PrintAndLogEx(INFO, "  tcode (telecom carrier ID) ............ %02x ( %s )", purseInfo->tcode,
                   KSX6924LookupTMoneyTCode(purseInfo->tcode, KSX6924_UNKNOWN));
     PrintAndLogEx(INFO, "  ccode (credit card company ID) ........ %02x ( %s )", purseInfo->ccode,
diff --git a/client/src/mifare/gallaghercore.c b/client/src/mifare/gallaghercore.c
index e7f3ba54b..80a902bab 100644
--- a/client/src/mifare/gallaghercore.c
+++ b/client/src/mifare/gallaghercore.c
@@ -112,19 +112,19 @@ bool gallagher_is_valid_creds(uint64_t region_code, uint64_t facility_code, uint
 
     // validate input
     if (region_code > 0x0f) {
-        PrintAndLogEx(ERR, "Region code must be 0 <= rc <= 15 (4 bits), received: %d", region_code);
+        PrintAndLogEx(ERR, "Region code must be 0 <= rc <= 15 (4 bits), received: %"PRIu64, region_code);
         is_valid = false;
     }
     if (facility_code > 0xffff) {
-        PrintAndLogEx(ERR, "Facility code must be 0 <= fc <= 65535 (2 bytes), received: %d", facility_code);
+        PrintAndLogEx(ERR, "Facility code must be 0 <= fc <= 65535 (2 bytes), received: %"PRIu64, facility_code);
         is_valid = false;
     }
     if (card_number > 0xffffff) {
-        PrintAndLogEx(ERR, "Card number must be 0 <= cn <= 16777215 (3 bytes), received: %d", card_number);
+        PrintAndLogEx(ERR, "Card number must be 0 <= cn <= 16777215 (3 bytes), received: %"PRIu64, card_number);
         is_valid = false;
     }
     if (issue_level > 0x0f) {
-        PrintAndLogEx(ERR, "Issue level must be 0 <= il <= 15 (4 bits), received: %d", issue_level);
+        PrintAndLogEx(ERR, "Issue level must be 0 <= il <= 15 (4 bits), received: %"PRIu64, issue_level);
         is_valid = false;
     }
     return is_valid;
