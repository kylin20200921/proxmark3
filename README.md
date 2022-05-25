commit c5e9201dd8fcc7e014bf848e3c07ec2ccd798a1f
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 29 10:05:26 2022 +0100

    fix cppcheck shadowing

diff --git a/client/src/cmdhfgallagher.c b/client/src/cmdhfgallagher.c
index 7d46953ef..4806747bf 100644
--- a/client/src/cmdhfgallagher.c
+++ b/client/src/cmdhfgallagher.c
@@ -237,6 +237,7 @@ static uint32_t find_available_gallagher_aid(DesfireContext_t *ctx, bool verbose
     // Retrieve the AID list
     uint8_t aid_buf[DESFIRE_BUFFER_SIZE] = {0};
     size_t aid_buf_len = 0;
+
     int res = DesfireGetAIDList(ctx, aid_buf, &aid_buf_len);
     if (res != PM3_SUCCESS) {
         PM3_RET_ERR(0, "Failed retrieving AID list");
@@ -245,6 +246,7 @@ static uint32_t find_available_gallagher_aid(DesfireContext_t *ctx, bool verbose
     if (verbose) {
         // Print what we got
         PrintAndLogEx(INFO, "Retrieved AID list:" NOLF);
+
         for (int i = 0; i < aid_buf_len; i += 3) {
             PrintAndLogEx(NORMAL, "%s %06X" NOLF,
                           (i == 0) ? "" : ",",
@@ -256,20 +258,23 @@ static uint32_t find_available_gallagher_aid(DesfireContext_t *ctx, bool verbose
 
     // Find lowest available in range F48120 -> F481FE, excluding the CAD
     for (uint8_t aid_increment = 0x20; aid_increment < 0xFF; aid_increment++) {
+
         uint32_t aid = 0x0081F4 | (aid_increment << 16);
         if (aid == CAD_AID) {
             continue;
         }
 
         // Check if AID exists in aid_buf
-        bool aid_exists = false;
+        bool found = false;
         for (uint8_t idx = 0; idx < aid_buf_len; idx += 3) {
+
             if (DesfireAIDByteToUint(&aid_buf[idx]) == aid) {
-                aid_exists = true;
+                found = true;
                 break;
             }
         }
-        if (aid_exists == false) {
+
+        if (found == false) {
             return aid;
         }
     }
