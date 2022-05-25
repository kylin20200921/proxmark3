commit b9bf84dbf14a2fc470dc7530f74a02667cb6d2af
Author: iceman1001 <iceman@iuse.se>
Date:   Thu May 20 10:15:16 2021 +0200

    desfire readdata fct, now deals with both INS cases

diff --git a/client/src/cmdhfmfdes.c b/client/src/cmdhfmfdes.c
index 1f7689fb4..121108c62 100644
--- a/client/src/cmdhfmfdes.c
+++ b/client/src/cmdhfmfdes.c
@@ -1903,16 +1903,18 @@ static int handler_desfire_debit(mfdes_value_t *value, uint8_t cs) {
 }
 
 static int handler_desfire_readdata(mfdes_data_t *data, MFDES_FILE_TYPE_T type, uint8_t cs) {
-    if (data->fileno > 0x1F) return PM3_EINVARG;
-    sAPDU apdu = {0x90, MFDES_READ_DATA, 0x00, 0x00, 1 + 3 + 3, (uint8_t *)data}; // 0xBD
-    if (type == MFDES_RECORD_FILE) apdu.INS = MFDES_READ_RECORDS; //0xBB
+    if (data->fileno > 0x1F) {
+        return PM3_EINVARG;
+    }
 
-    uint16_t sw = 0;
-    uint32_t resplen = 0;
+    sAPDU apdu = {0x90, MFDES_READ_DATA, 0x00, 0x00, 1 + 3 + 3, (uint8_t *)data}; // 0xBD
+    if (type == MFDES_RECORD_FILE) {
+        apdu.INS = MFDES_READ_RECORDS; //0xBB
+    } 
 
     // we need the CMD 0xBD <data> to calc the CMAC
     uint8_t tmp_data[8]; // Since the APDU is hardcoded to 7 bytes of payload 7+1 = 8 is enough.
-    tmp_data[0] = 0xBD;
+    tmp_data[0] = apdu.INS;
     memcpy(&tmp_data[1], data, 7);
 
     // size_t plen = apdu.Lc;
@@ -1926,6 +1928,8 @@ static int handler_desfire_readdata(mfdes_data_t *data, MFDES_FILE_TYPE_T type,
     apdu.Lc =  7;
     apdu.data = (uint8_t *)data;
 
+    uint16_t sw = 0;
+    uint32_t resplen = 0;
     int res = send_desfire_cmd(&apdu, false, data->data, &resplen, &sw, 0, true);
     if (res != PM3_SUCCESS) {
         PrintAndLogEx(WARNING, _RED_("   Can't read data -> %s"), GetErrorString(res, &sw));
