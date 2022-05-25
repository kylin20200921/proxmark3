commit 51fb73f4c8dc70233d206204cf119bb96236025f
Author: iceman1001 <iceman@iuse.se>
Date:   Fri May 21 11:49:00 2021 +0200

    cppchecker fixes

diff --git a/client/src/cmdhfmfdes.c b/client/src/cmdhfmfdes.c
index 4fb04b324..8f709ad0c 100644
--- a/client/src/cmdhfmfdes.c
+++ b/client/src/cmdhfmfdes.c
@@ -1924,6 +1924,7 @@ static int handler_desfire_readdata(mfdes_data_t *data, MFDES_FILE_TYPE_T type,
 
     size_t plen = 8;
     uint8_t *p = mifare_cryto_preprocess_data(tag, tmp_data, &plen, 0, MDCM_PLAIN | CMAC_COMMAND);
+    (void)p;
     // apdu data does not need the cmd, so use the original read command data.
     apdu.Lc =  7;
     apdu.data = (uint8_t *)data;
@@ -1980,12 +1981,12 @@ static int handler_desfire_writedata(mfdes_data_t *data, MFDES_FILE_TYPE_T type,
         90  3d  00  00  09  02  00  00  00  02  00  00  00  00  00
     */
 
-    if (data->fileno > 0x1F) return PM3_EINVARG;
+    if (data->fileno > 0x1F) {
+        return PM3_EINVARG;
+    }
     uint32_t datatowrite = le24toh(data->length);
     uint32_t offset = le24toh(data->offset);
-    uint32_t datasize;
-    uint32_t pos = 0;
-    uint32_t recvlen = 0;
+    uint32_t datasize, pos, recvlen = 0;
     int res = PM3_SUCCESS;
     uint16_t sw = 0;
     uint8_t tmp[60] = {0};
