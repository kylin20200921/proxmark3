commit 861e06d8af874b1cfbbd6db85621aa9a4fc90abb
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jun 20 10:47:23 2021 +0200

    fix coverity CID 322780 - out-of-bounds

diff --git a/client/src/cmdhfmfdes.c b/client/src/cmdhfmfdes.c
index 52d26bb7f..48cd15b5c 100644
--- a/client/src/cmdhfmfdes.c
+++ b/client/src/cmdhfmfdes.c
@@ -2033,18 +2033,24 @@ static int handler_desfire_writedata(mfdes_data_t *data, MFDES_FILE_TYPE_T type,
     if (data->fileno > 0x1F) {
         return PM3_EINVARG;
     }
+
     uint32_t datatowrite = le24toh(data->length);
     uint32_t offset = le24toh(data->offset);
     uint32_t datasize, recvlen = 0;
     int res = PM3_SUCCESS;
     uint16_t sw = 0;
-    uint8_t tmp[60] = {0};
+
     mfdes_data_t sdata;
     sAPDU apdu = {0x90, MFDES_WRITE_DATA, 0x00, 0x00, 0, (uint8_t *) &sdata}; // 0x3D
+
+    uint8_t tmp[61] = {0};
     tmp[0] = MFDES_WRITE_DATA;
     tmp[1] = data->fileno;
     apdu.data = &tmp[1]; // tmp[0] is holding the OPCODE for macd calc, so we dont want it in the apdu
-    if (type == MFDES_RECORD_FILE) apdu.INS = MFDES_WRITE_RECORD;
+
+    if (type == MFDES_RECORD_FILE) {
+        apdu.INS = MFDES_WRITE_RECORD;
+    }
 
     while (datatowrite) {
 
