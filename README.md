commit a8175a0f573a56bcd24859e76287756d51ffb74c
Author: iceman1001 <iceman@iuse.se>
Date:   Tue Feb 15 20:10:39 2022 +0100

    coverity #378901, #378902 fixes

diff --git a/client/src/cmdhflegic.c b/client/src/cmdhflegic.c
index 38abf668b..9763292b2 100644
--- a/client/src/cmdhflegic.c
+++ b/client/src/cmdhflegic.c
@@ -656,6 +656,7 @@ int legic_read_mem(uint32_t offset, uint32_t len, uint32_t iv, uint8_t *out, uin
 
     clearCommandBuffer();
     SendCommandNG(CMD_HF_LEGIC_READER, (uint8_t *)payload, sizeof(legic_packet_t));
+    free(payload);
     PacketResponseNG resp;
 
     uint8_t timeout = 0;
@@ -669,7 +670,6 @@ int legic_read_mem(uint32_t offset, uint32_t len, uint32_t iv, uint8_t *out, uin
     }
     PrintAndLogEx(NORMAL, "");
 
-
     *outlen = resp.data.asDwords[0];
     if (resp.status != PM3_SUCCESS) {
         PrintAndLogEx(WARNING, "Failed reading tag");
@@ -819,6 +819,7 @@ static int CmdLegicDump(const char *Cmd) {
 
     clearCommandBuffer();
     SendCommandNG(CMD_HF_LEGIC_READER, (uint8_t *)payload, sizeof(legic_packet_t));
+    free(payload);
     PacketResponseNG resp;
 
     uint8_t timeout = 0;
