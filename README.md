commit edc300ea97ba51c7410f1fa041512590a553ea1f
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Aug 22 21:02:11 2021 +0200

    ntag i2c 2k - select sector annotation

diff --git a/client/src/cmdhflist.c b/client/src/cmdhflist.c
index c91306032..a1c2362d2 100644
--- a/client/src/cmdhflist.c
+++ b/client/src/cmdhflist.c
@@ -228,9 +228,17 @@ int applyIso14443a(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize) {
             snprintf(exp, size, "DEC(%d)", cmd[1]);
             break;
         case MIFARE_CMD_RESTORE:
+
             if (cmdsize == 4)
-                snprintf(exp, size, "RESTORE(%d)", cmd[1]);
+                // cmd0 == 0xC2 and cmd1 == 0xFF
+                // high probability its SELECT SECTOR COMMAND: 
+                if (cmd[1] == 0xFF) {
+                    snprintf(exp, size, "SELECT SECTOR(%d)", cmd[1]);                    
+                } else {
+                    snprintf(exp, size, "RESTORE(%d)", cmd[1]);
+                }
             else
+
                 return 0;
             break;
         case MIFARE_CMD_TRANSFER:
diff --git a/include/protocols.h b/include/protocols.h
index 168606498..a7481bbc0 100644
--- a/include/protocols.h
+++ b/include/protocols.h
@@ -31,6 +31,8 @@ Ultralight C
     1A = Step1 Authenticate
     AF = Step2 Authenticate
 
+NTAG i2c 2K
+    C2 = SECTOR_SELECT
 
 ISO14443B
     05 = REQB
@@ -190,6 +192,10 @@ ISO 7816-4 Basic interindustry commands. For command APDU's.
 #define MIFARE_ULNANO_WRITESIG      0xA9
 #define MIFARE_ULNANO_LOCKSIG       0xAC
 
+// NTAG i2k 2K  uses sector 0, and sector 1 to have access to
+// block 0x00-0xFF.  
+#define NTAG_I2C_SELECT_SECTOR      0xC2
+
 // mifare 4bit card answers
 #define CARD_ACK      0x0A  // 1010 - ACK
 #define CARD_NACK_IV  0x00  // 0000 - NACK, invalid argument (invalid page address)
