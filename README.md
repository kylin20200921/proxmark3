commit 9c2cbe9fbb1ba8e006e3c479c27c95b5d0e453ec
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Aug 22 21:16:33 2021 +0200

    ntag i2c 2k select sector annotation

diff --git a/client/src/cmdhflist.c b/client/src/cmdhflist.c
index a1c2362d2..8b71d81fd 100644
--- a/client/src/cmdhflist.c
+++ b/client/src/cmdhflist.c
@@ -159,6 +159,12 @@ uint8_t iclass_CRC_check(bool isResponse, uint8_t *d, uint8_t n) {
 }
 
 int applyIso14443a(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize) {
+
+    if ((cmdsize == 6) && (memcmp(cmd+1, "\x00\x00\x00", 3) == 0)) {
+        snprintf(exp, size, "SECTOR(%d)", cmd[0]);
+        return 1;
+    }
+
     switch (cmd[0]) {
         case ISO14443A_CMD_WUPA:
             snprintf(exp, size, "WUPA");
@@ -233,12 +239,11 @@ int applyIso14443a(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize) {
                 // cmd0 == 0xC2 and cmd1 == 0xFF
                 // high probability its SELECT SECTOR COMMAND: 
                 if (cmd[1] == 0xFF) {
-                    snprintf(exp, size, "SELECT SECTOR(%d)", cmd[1]);                    
+                    snprintf(exp, size, "SELECT SECTOR");
                 } else {
                     snprintf(exp, size, "RESTORE(%d)", cmd[1]);
                 }
             else
-
                 return 0;
             break;
         case MIFARE_CMD_TRANSFER:
