commit 6a6dcd4f56ddd3fca08a55cb9c1b6c8f8b4c386c
Author: iceman1001 <iceman@iuse.se>
Date:   Fri May 21 21:07:38 2021 +0200

    fix cppchecker

diff --git a/client/src/cmdhfmfdes.c b/client/src/cmdhfmfdes.c
index 8f709ad0c..22242e67e 100644
--- a/client/src/cmdhfmfdes.c
+++ b/client/src/cmdhfmfdes.c
@@ -1986,7 +1986,7 @@ static int handler_desfire_writedata(mfdes_data_t *data, MFDES_FILE_TYPE_T type,
     }
     uint32_t datatowrite = le24toh(data->length);
     uint32_t offset = le24toh(data->offset);
-    uint32_t datasize, pos, recvlen = 0;
+    uint32_t datasize, recvlen = 0;
     int res = PM3_SUCCESS;
     uint16_t sw = 0;
     uint8_t tmp[60] = {0};
@@ -2013,15 +2013,14 @@ static int handler_desfire_writedata(mfdes_data_t *data, MFDES_FILE_TYPE_T type,
         tmp[7] = (datasize >> 16) & 0xFF;
         memcpy(&tmp[8], (uint8_t *)&data->data[offset], datasize);
 
-//        size_t plen = datasize;
-//        uint8_t *p = mifare_cryto_preprocess_data(tag, (uint8_t *)&data->data[pos], &plen, 0, cs | MAC_COMMAND | CMAC_COMMAND | ENC_COMMAND);
         size_t plen = datasize + 8;
         uint8_t *p = mifare_cryto_preprocess_data(tag, tmp, &plen, 8, cs | MAC_COMMAND | CMAC_COMMAND | ENC_COMMAND);
 
         // Copy actual data as needed to create APDU Format
         if (plen != -1) {
             memcpy(&tmp[8], &p[8], plen - 8);
-            apdu.Lc = plen - 1; //need to drop the OpCode from plen
+            // need to drop the OpCode from plen
+            apdu.Lc = plen - 1;
         }
 
         /*
@@ -2041,7 +2040,6 @@ static int handler_desfire_writedata(mfdes_data_t *data, MFDES_FILE_TYPE_T type,
         }
         offset += datasize;
         datatowrite -= datasize;
-        pos += datasize;
     }
     if (type == MFDES_RECORD_FILE) {
         if (handler_desfire_commit_transaction() != PM3_SUCCESS) {
