commit ba76aabdf8d61a627988da8ced0d9c29d5f00708
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Jun 21 09:15:18 2021 +0200

    hf mf fchk - output style

diff --git a/client/src/mifare/mifarehost.c b/client/src/mifare/mifarehost.c
index d068aaac0..9b0c20d36 100644
--- a/client/src/mifare/mifarehost.c
+++ b/client/src/mifare/mifarehost.c
@@ -197,16 +197,20 @@ int mfCheckKeys_fast(uint8_t sectorsCnt, uint8_t firstChunk, uint8_t lastChunk,
                      uint32_t size, uint8_t *keyBlock, sector_t *e_sector, bool use_flashmemory) {
 
     uint64_t t2 = msclock();
-    uint32_t timeout = 0;
 
     // send keychunk
     clearCommandBuffer();
     SendCommandOLD(CMD_HF_MIFARE_CHKKEYS_FAST, (sectorsCnt | (firstChunk << 8) | (lastChunk << 12)), ((use_flashmemory << 8) | strategy), size, keyBlock, 6 * size);
     PacketResponseNG resp;
 
+    uint32_t timeout = 0;
     while (!WaitForResponseTimeout(CMD_ACK, &resp, 2000)) {
+
+        PrintAndLogEx((timeout == 0) ? INFO : NORMAL, "." NOLF);
+        fflush(stdout);
+
         timeout++;
-        PrintAndLogEx(NORMAL, "." NOLF);
+
         // max timeout for one chunk of 85keys, 60*3sec = 180seconds
         // s70 with 40*2 keys to check, 80*85 = 6800 auth.
         // takes about 97s, still some margin before abort
@@ -217,6 +221,10 @@ int mfCheckKeys_fast(uint8_t sectorsCnt, uint8_t firstChunk, uint8_t lastChunk,
     }
     t2 = msclock() - t2;
 
+    if (timeout) {
+        PrintAndLogEx(NORMAL, "");
+    }
+
     // time to convert the returned data.
     uint8_t curr_keys = resp.oldarg[0];
 
