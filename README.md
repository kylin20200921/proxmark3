commit 4db1627e1dd901b171c7919463bc97e4936d588c
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 29 10:23:21 2022 +0100

    coverit #373451

diff --git a/client/src/cmdhfgallagher.c b/client/src/cmdhfgallagher.c
index 4806747bf..fde39b37e 100644
--- a/client/src/cmdhfgallagher.c
+++ b/client/src/cmdhfgallagher.c
@@ -232,13 +232,14 @@ static bool aid_exists(DesfireContext_t *ctx, uint32_t aid, bool verbose) {
  */
 static uint32_t find_available_gallagher_aid(DesfireContext_t *ctx, bool verbose) {
     // Select PICC
-    select_aid(ctx, 0x000000, verbose);
+    int res = select_aid(ctx, 0x000000, verbose);
+    PM3_RET_IF_ERR(res);
 
     // Retrieve the AID list
     uint8_t aid_buf[DESFIRE_BUFFER_SIZE] = {0};
     size_t aid_buf_len = 0;
 
-    int res = DesfireGetAIDList(ctx, aid_buf, &aid_buf_len);
+    res = DesfireGetAIDList(ctx, aid_buf, &aid_buf_len);
     if (res != PM3_SUCCESS) {
         PM3_RET_ERR(0, "Failed retrieving AID list");
     }
