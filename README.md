commit 90fcec0152207acaa911f99f3f40603e5eac9c2e
Author: iceman1001 <iceman@iuse.se>
Date:   Sun May 8 09:36:56 2022 +0200

    add ledcontrol

diff --git a/armsrc/lfops.c b/armsrc/lfops.c
index ae31a57ba..c574ca339 100644
--- a/armsrc/lfops.c
+++ b/armsrc/lfops.c
@@ -2226,21 +2226,22 @@ void T55xxWakeUp(uint32_t pwd, uint8_t flags, bool ledcontrol) {
 /*-------------- Cloning routines -----------*/
 static void WriteT55xx(const uint32_t *blockdata, uint8_t startblock, uint8_t numblocks, bool ledcontrol) {
     t55xx_write_block_t cmd;
-    cmd.pwd     = 0;
-    cmd.flags   = 0;
+    cmd.pwd = 0;
+    cmd.flags = 0;
 
     for (uint8_t i = numblocks + startblock; i > startblock; i--) {
-        cmd.data    = blockdata[i - 1];
+        cmd.data = blockdata[i - 1];
         cmd.blockno = i - 1;
         T55xxWriteBlock((uint8_t *)&cmd, ledcontrol);
     }
 }
 
-static void WriteEM4x05(uint32_t *blockdata, uint8_t startblock, uint8_t numblocks) {
+static void WriteEM4x05(uint32_t *blockdata, uint8_t startblock, uint8_t numblocks, bool ledcontrol) {
     if (g_dbglevel == DBG_DEBUG) {
         Dbprintf("# | data ( EM4x05 )");
         Dbprintf("--+----------------");
     }
+
     for (uint8_t i = startblock; i < startblock + numblocks; i++) {
         if (i > 4) {
             blockdata[i - startblock] = reflect(blockdata[i - startblock], 32);
@@ -2249,11 +2250,13 @@ static void WriteEM4x05(uint32_t *blockdata, uint8_t startblock, uint8_t numbloc
             Dbprintf("%i | %08x", i, blockdata[i - startblock]);
         }
     }
+
     if (g_dbglevel == DBG_DEBUG) {
         Dbprintf("--+----------------");
     }
+
     for (uint8_t i = numblocks + startblock; i > startblock; i--) {
-        EM4xWriteWord(i - 1, blockdata[i - 1 - startblock], 0, 0, false);
+        EM4xWriteWord(i - 1, blockdata[i - 1 - startblock], 0, 0, ledcontrol);
     }
 }
 
@@ -2308,7 +2311,7 @@ void CopyHIDtoT55x7(uint32_t hi2, uint32_t hi, uint32_t lo, uint8_t longFMT, boo
 
     if (ledcontrol) LED_D_ON();
     if (em) {
-        WriteEM4x05(data, 4, last_block + 1);
+        WriteEM4x05(data, 4, last_block + 1, ledcontrol);
     } else {
         WriteT55xx(data, 0, last_block + 1, ledcontrol);
     }
@@ -2331,7 +2334,7 @@ void CopyVikingtoT55xx(uint8_t *blocks, bool q5, bool em, bool ledcontrol) {
 
     // Program the data blocks for supplied ID and the block 0 config
     if (em) {
-        WriteEM4x05(data, 4, 3);
+        WriteEM4x05(data, 4, 3, ledcontrol);
     } else {
         WriteT55xx(data, 0, 3, ledcontrol);
     }
@@ -2425,7 +2428,7 @@ int copy_em410x_to_t55xx(uint8_t card, uint8_t clock, uint32_t id_hi, uint32_t i
         data[0] = T5555_SET_BITRATE(clock) | T5555_MODULATION_MANCHESTER | (2 << T5555_MAXBLOCK_SHIFT);
     }
     if (card == 2) {
-        WriteEM4x05(data, 4, 3);
+        WriteEM4x05(data, 4, 3, ledcontrol);
     } else {
         WriteT55xx(data, 0, 3, ledcontrol);
     }
