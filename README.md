commit 982e669a777484ff995adda67bcf0972b02127c9
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Oct 23 20:45:46 2021 +0200

    magsafe iterates 7a/7b/7c/7d for wupa

diff --git a/armsrc/iso14443a.c b/armsrc/iso14443a.c
index 5b317ee71..1e83baff9 100644
--- a/armsrc/iso14443a.c
+++ b/armsrc/iso14443a.c
@@ -2437,8 +2437,9 @@ static int GetATQA(uint8_t *resp, uint8_t *resp_par, bool use_ecp, bool use_mags
     // 0x7A - MAGESAFE WAKE UP
     uint8_t wupa[] = { ISO14443A_CMD_WUPA };
 
+    // if magsafe, set it outofbounds
     if (use_magsafe) {
-        wupa[0] = MAGSAFE_CMD_WUPA;
+        wupa[0] = MAGSAFE_CMD_WUPA_4;
     }
 
     uint32_t save_iso14a_timeout = iso14a_get_timeout();
@@ -2455,6 +2456,15 @@ static int GetATQA(uint8_t *resp, uint8_t *resp_par, bool use_ecp, bool use_mags
             SpinDelay(ECP_DELAY);
         }
 
+        if (use_magsafe) {
+            if (wupa[0] == MAGSAFE_CMD_WUPA_4) {
+                wupa[0] = MAGSAFE_CMD_WUPA_1;
+            } else {
+                wupa[0]++;
+            }
+        }
+
+
         // Broadcast for a card, WUPA (0x52) will force response from all cards in the field
         ReaderTransmitBitsPar(wupa, 7, NULL, NULL);
         // Receive the ATQA
diff --git a/client/src/cmdhflist.c b/client/src/cmdhflist.c
index cc07666d8..362a665bc 100644
--- a/client/src/cmdhflist.c
+++ b/client/src/cmdhflist.c
@@ -181,7 +181,10 @@ int applyIso14443a(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize, bool i
 
         gs_ntag_i2c_state = 0;
         switch (cmd[0]) {
-            case MAGSAFE_CMD_WUPA:
+            case MAGSAFE_CMD_WUPA_1:
+            case MAGSAFE_CMD_WUPA_2:
+            case MAGSAFE_CMD_WUPA_3:
+            case MAGSAFE_CMD_WUPA_4:
                 snprintf(exp, size, "MAGSAFE WUPA");
                 break;
             case ISO14443A_CMD_WUPA:
diff --git a/include/protocols.h b/include/protocols.h
index f70a84850..96c00dc5f 100644
--- a/include/protocols.h
+++ b/include/protocols.h
@@ -144,7 +144,10 @@ ISO 7816-4 Basic interindustry commands. For command APDU's.
 
 
 // 7bit Apple Magsafe wake up command
-#define MAGSAFE_CMD_WUPA            0x7A
+#define MAGSAFE_CMD_WUPA_1          0x7A
+#define MAGSAFE_CMD_WUPA_2          0x7B
+#define MAGSAFE_CMD_WUPA_3          0x7C
+#define MAGSAFE_CMD_WUPA_4          0x7D
 
 #define ISO14443A_CMD_REQA          0x26
 #define ISO14443A_CMD_READBLOCK     0x30
