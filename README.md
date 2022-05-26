commit e6eb8cf51fcb76adf5278ccf1b1f23f904929fb7
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Feb 12 10:00:20 2022 +0100

    change hf mfu dump text output

diff --git a/client/src/cmdhfmfu.c b/client/src/cmdhfmfu.c
index 3a19c4ade..57d134fd2 100644
--- a/client/src/cmdhfmfu.c
+++ b/client/src/cmdhfmfu.c
@@ -52,6 +52,10 @@
 #define MAX_MY_D_MOVE_LEAN  0x0F
 #define MAX_UL_NANO_40      0x0A
 
+#define MFU_BLOCK_SIZE      0x04
+#define MFU_MAX_BLOCKS      0xFF
+#define MFU_MAX_BYTES       (MFU_MAX_BLOCKS * MFU_BLOCK_SIZE)
+
 static int CmdHelp(const char *Cmd);
 
 static uint8_t default_3des_keys[][16] = {
@@ -2057,19 +2061,22 @@ static int CmdHF14AMfURdBl(const char *Cmd) {
 
 void printMFUdumpEx(mfu_dump_t *card, uint16_t pages, uint8_t startpage) {
 
+    PrintAndLogEx(NORMAL, "");
     PrintAndLogEx(INFO, _CYAN_("MFU dump file information"));
     PrintAndLogEx(INFO, "-------------------------------------------------------------");
-    PrintAndLogEx(INFO, "      Version | " _YELLOW_("%s"), sprint_hex(card->version, sizeof(card->version)));
-    PrintAndLogEx(INFO, "        TBD 0 | %s", sprint_hex(card->tbo, sizeof(card->tbo)));
-    PrintAndLogEx(INFO, "        TBD 1 | %s", sprint_hex(card->tbo1, sizeof(card->tbo1)));
-    PrintAndLogEx(INFO, "    Signature | %s", sprint_hex(card->signature, sizeof(card->signature)));
+    PrintAndLogEx(INFO, "Version..... " _YELLOW_("%s"), sprint_hex(card->version, sizeof(card->version)));
+    PrintAndLogEx(INFO, "TBD 0....... %s", sprint_hex(card->tbo, sizeof(card->tbo)));
+    PrintAndLogEx(INFO, "TBD 1....... %s", sprint_hex(card->tbo1, sizeof(card->tbo1)));
+    PrintAndLogEx(INFO, "Signature... %s", sprint_hex(card->signature, 16 ));
+    PrintAndLogEx(INFO, "             %s", sprint_hex(card->signature + 16, sizeof(card->signature) - 16));
     for (uint8_t i = 0; i < 3; i ++) {
-        PrintAndLogEx(INFO, "    Counter %d | %s", i, sprint_hex(card->counter_tearing[i], 3));
-        PrintAndLogEx(INFO, "    Tearing %d | %s", i, sprint_hex(card->counter_tearing[i] + 3, 1));
+        PrintAndLogEx(INFO, "Counter %d... %s", i, sprint_hex(card->counter_tearing[i], 3));
+        PrintAndLogEx(INFO, "Tearing %d... %s", i, sprint_hex(card->counter_tearing[i] + 3, 1));
     }
 
-    PrintAndLogEx(INFO, "Max data page | " _YELLOW_("%d") " (" _YELLOW_("%d") " bytes)", card->pages - 1, card->pages * 4);
-    PrintAndLogEx(INFO, "  Header size | %d", MFU_DUMP_PREFIX_LENGTH);
+    PrintAndLogEx(INFO, "Max data page... " _YELLOW_("%d") " ( " _YELLOW_("%d") " bytes )", card->pages - 1, card->pages * 4);
+    PrintAndLogEx(INFO, "Header size..... %d bytes", MFU_DUMP_PREFIX_LENGTH);
+    PrintAndLogEx(NORMAL, "");
     PrintAndLogEx(INFO, "-------------------------------------------------------------");
     PrintAndLogEx(INFO, "block#   | data        |lck| ascii");
     PrintAndLogEx(INFO, "---------+-------------+---+------");
@@ -4094,8 +4101,8 @@ static int CmdHF14AMfuEView(const char *Cmd) {
     CLIExecWithReturn(ctx, Cmd, argtable, true);
     CLIParserFree(ctx);
 
-    uint16_t blocks = 0xFF;
-    uint16_t bytes = blocks * 4;
+    uint16_t blocks = MFU_MAX_BLOCKS;
+    uint16_t bytes = MFU_MAX_BYTES;
 
     uint8_t *dump = calloc(bytes, sizeof(uint8_t));
     if (dump == NULL) {
