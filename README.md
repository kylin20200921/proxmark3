commit e811b35a6f759859ca765251a5f004d3f868a99d
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Aug 22 21:32:14 2021 +0200

    ntag i2c 2k - fast write

diff --git a/client/src/cmdhflist.c b/client/src/cmdhflist.c
index 8b71d81fd..55f85d99b 100644
--- a/client/src/cmdhflist.c
+++ b/client/src/cmdhflist.c
@@ -45,6 +45,9 @@ void ClearAuthData(void) {
     AuthData.ks3 = 0;
 }
 
+
+static int gs_ntag_i2c_state = 0;
+
 /**
  * @brief iso14443A_CRC_check Checks CRC in command or response
  * @param isResponse
@@ -158,13 +161,17 @@ uint8_t iclass_CRC_check(bool isResponse, uint8_t *d, uint8_t n) {
     return check_crc(CRC_ICLASS, d, n);
 }
 
+
 int applyIso14443a(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize) {
 
-    if ((cmdsize == 6) && (memcmp(cmd+1, "\x00\x00\x00", 3) == 0)) {
+    if ((gs_ntag_i2c_state == 1) && (cmdsize == 6) && (memcmp(cmd+1, "\x00\x00\x00", 3) == 0)) {
         snprintf(exp, size, "SECTOR(%d)", cmd[0]);
+        gs_ntag_i2c_state = 0;
         return 1;
     }
 
+    gs_ntag_i2c_state = 0;
+
     switch (cmd[0]) {
         case ISO14443A_CMD_WUPA:
             snprintf(exp, size, "WUPA");
@@ -240,6 +247,7 @@ int applyIso14443a(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize) {
                 // high probability its SELECT SECTOR COMMAND: 
                 if (cmd[1] == 0xFF) {
                     snprintf(exp, size, "SELECT SECTOR");
+                    gs_ntag_i2c_state = 1;
                 } else {
                     snprintf(exp, size, "RESTORE(%d)", cmd[1]);
                 }
@@ -336,6 +344,11 @@ int applyIso14443a(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize) {
                 snprintf(exp, size, "?");
             break;
         }
+        case NTAG_I2C_FASTWRITE: 
+            if (size == 69) {
+                snprintf(exp, size, "FAST WRITE (%d - %d)", cmd[1], cmd[2]);
+            }
+            break;
         default:
             return 0;
     }
diff --git a/include/protocols.h b/include/protocols.h
index a7481bbc0..50833cb17 100644
--- a/include/protocols.h
+++ b/include/protocols.h
@@ -195,6 +195,7 @@ ISO 7816-4 Basic interindustry commands. For command APDU's.
 // NTAG i2k 2K  uses sector 0, and sector 1 to have access to
 // block 0x00-0xFF.  
 #define NTAG_I2C_SELECT_SECTOR      0xC2
+#define NTAG_I2C_FASTWRITE          0xA6
 
 // mifare 4bit card answers
 #define CARD_ACK      0x0A  // 1010 - ACK
