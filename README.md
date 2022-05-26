commit ba408be60b5d3df6fec410d82a93d4953214cd72
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Feb 12 17:41:00 2022 +0100

    hf mfu dump - now saves EML too

diff --git a/client/src/cmdhfmfu.c b/client/src/cmdhfmfu.c
index 25d9269c6..03be2214e 100644
--- a/client/src/cmdhfmfu.c
+++ b/client/src/cmdhfmfu.c
@@ -2451,8 +2451,9 @@ static int CmdHF14AMfUDump(const char *Cmd) {
         strcat(filename, "hf-mfu-");
         FillFileNameByUID(filename, uid, "-dump", sizeof(uid));
     }
-    uint16_t datalen = pages * 4 + MFU_DUMP_PREFIX_LENGTH;
+    uint16_t datalen = pages * MFU_BLOCK_SIZE + MFU_DUMP_PREFIX_LENGTH;
     saveFile(filename, ".bin", (uint8_t *)&dump_file_data, datalen);
+    saveFileEML(filename, (uint8_t *)&dump_file_data, datalen, MFU_BLOCK_SIZE);
     saveFileJSON(filename, jsfMfuMemory, (uint8_t *)&dump_file_data, datalen, NULL);
 
     if (is_partial)
