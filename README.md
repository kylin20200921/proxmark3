commit dd20083126907dc4144ae4136c056fd7d68cff2e
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Apr 30 18:39:14 2022 +0200

    improved FUDAN identification and anti-collision select based on @lsylx issue #1599

diff --git a/armsrc/iso14443a.c b/armsrc/iso14443a.c
index 9ac4146ca..040bb3904 100644
--- a/armsrc/iso14443a.c
+++ b/armsrc/iso14443a.c
@@ -2512,7 +2512,7 @@ int iso14443a_select_cardEx(uint8_t *uid_ptr, iso14a_card_select_t *p_card, uint
         p_card->ats_len = 0;
     }
 
-    if (!GetATQA(resp, resp_par, use_ecp, use_magsafe)) {
+    if (GetATQA(resp, resp_par, use_ecp, use_magsafe) == false) {
         return 0;
     }
 
@@ -2521,6 +2521,29 @@ int iso14443a_select_cardEx(uint8_t *uid_ptr, iso14a_card_select_t *p_card, uint
         p_card->atqa[1] = resp[1];
     }
 
+    // 11RF005SH or 11RF005M, Read UID again
+    if (p_card && p_card->atqa[1] == 0x00 ) {
+
+        if ((p_card->atqa[0]==0x03) || (p_card->atqa[0]==0x05)) {
+
+            // Read real UID
+            uint8_t fudan_read[] = { 0x30, 0x01, 0x02, 0xA8};
+            ReaderTransmit(fudan_read, sizeof(fudan_read), NULL); 
+            ReaderReceive(resp, resp_par);
+
+            memcpy(p_card->uid, resp, 4);
+
+            // select again?
+            if (GetATQA(resp, resp_par, false, false) == false) {
+                return 0;
+            }
+
+            p_card->sak = 0x0A;
+            p_card->uidlen = 4;
+            return 1;
+        }
+    }
+
     if (anticollision) {
         // clear uid
         if (uid_ptr)
diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index 028ded92a..9a78fc589 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -1470,7 +1470,9 @@ typedef enum {
     MTPLUS = 8,
     MTULTRALIGHT = 16,
     HID_SEOS = 32,
-    MTOTHER = 64
+    MTOTHER = 64,
+    MTEMV = 128,
+    MTFUDAN = 256,
 } nxp_mifare_type_t;
 
 // Based on NXP AN10833 Rev 3.6 and NXP AN10834 Rev 4.1
@@ -1577,6 +1579,9 @@ static int detect_nxp_card(uint8_t sak, uint16_t atqa, uint64_t select_status) {
                     if ((atqa & 0x0001) == 0x0001) {
                         printTag("HID SEOS (smartmx / javacard)");
                         type |= HID_SEOS;
+                    } else if ((atqa & 0x0004) == 0x0004) {
+                        printTag("EMV");
+                        type |= MTEMV;
                     } else {
                         printTag("MIFARE Plus EV1 2K/4K in SL3");
                         printTag("MIFARE Plus S 2K/4K in SL3");
@@ -1592,6 +1597,20 @@ static int detect_nxp_card(uint8_t sak, uint16_t atqa, uint64_t select_status) {
         } else if ((sak & 0x04) == 0x04) {
             printTag("Any MIFARE CL1");
             type |= MTDESFIRE;
+        } else if ((sak & 0x0A) == 0x0A) {
+
+            if ((atqa & 0x0003) == 0x0003) {
+                // Uses Shanghai algo
+                printTag("FM11RF005SH (FUDAN Shanghai Metro)");
+                type |= MTFUDAN;
+            } else if ((atqa & 0x0005) == 0x0005) {
+                printTag("FM11RF005M (FUDAN MIFARE Classic clone)");
+                type |= MTFUDAN;
+            }
+        } 
+        else if ((sak & 0x53) == 0x53) {
+            printTag("FM11RF08SH (FUDAN)");
+            type |= MTFUDAN;
         } else {
             printTag("MIFARE Ultralight");
             printTag("MIFARE Ultralight C");
@@ -1760,6 +1779,7 @@ int infoHF14A(bool verbose, bool do_nack_test, bool do_aid_search) {
     bool isMifareUltralight = false;
     bool isST = false;
     bool isEMV = false;
+    bool isFUDAN = false;
     int nxptype = MTNONE;
 
     if (card.uidlen <= 4) {
@@ -1773,6 +1793,12 @@ int infoHF14A(bool verbose, bool do_nack_test, bool do_aid_search) {
         if ((nxptype & MTOTHER) == MTOTHER)
             isMifareClassic = true;
 
+        if ((nxptype & MTFUDAN) == MTFUDAN)
+            isFUDAN = true;
+
+        if ((nxptype & MTEMV) == MTEMV)
+            isEMV = true;
+
     } else {
 
         // Double & triple sized UID, can be mapped to a manufacturer.
@@ -1793,6 +1819,12 @@ int infoHF14A(bool verbose, bool do_nack_test, bool do_aid_search) {
                 if ((nxptype & MTOTHER) == MTOTHER)
                     isMifareClassic = true;
 
+                if ((nxptype & MTFUDAN) == MTFUDAN)
+                    isFUDAN = true;
+
+                if ((nxptype & MTEMV) == MTEMV)
+                    isEMV = true;
+
                 break;
             case 0x05: // Infineon
                 if ((card.uid[1] & 0xF0) == 0x10) {
@@ -1860,12 +1892,14 @@ int infoHF14A(bool verbose, bool do_nack_test, bool do_aid_search) {
                         break;
                     }
                     case 0x0A: {
-                        if (card.atqa[0] == 0x03)
-                            // Uses MIFARE Crypto-1 algo
-                            printTag("FM11RF005M (FUDAN MIFARE Classic clone)");
-                        else if (card.atqa[0] == 0x05)
+                        if (card.atqa[0] == 0x03) {
                             // Uses Shanghai algo
                             printTag("FM11RF005SH (FUDAN Shanghai Metro)");
+
+                        } else if (card.atqa[0] == 0x05) {
+                            // Uses MIFARE Crypto-1 algo
+                            printTag("FM11RF005M (FUDAN MIFARE Classic clone)");
+                        }
                         break;
                     }
                     case 0x20: {
@@ -1880,6 +1914,10 @@ int infoHF14A(bool verbose, bool do_nack_test, bool do_aid_search) {
                         printTag("Nokia 6212 or 6131");
                         break;
                     }
+                    case 0x53: {
+                        printTag("FM11RF08SH (FUDAN)");
+                        break;
+                    }
                     case 0x98: {
                         printTag("Gemplus MPCOS");
                         break;
@@ -2259,6 +2297,19 @@ int infoHF14A(bool verbose, bool do_nack_test, bool do_aid_search) {
     if (isEMV)
         PrintAndLogEx(HINT, "Hint: try " _YELLOW_("`emv search -s`"));
 
+    if (isFUDAN) {
+        PrintAndLogEx(HINT, "Hint: try " _YELLOW_("`hf 14a raw`") " - since FUDAN is different");
+        PrintAndLogEx(HINT, "  hf 14a raw -a -b 7 -p 26");
+        PrintAndLogEx(HINT, "  hf 14a raw -p -c 3000");
+        PrintAndLogEx(HINT, "  hf 14a raw -p -c 3001");
+        PrintAndLogEx(HINT, "  hf 14a raw -p -c 3002");
+        PrintAndLogEx(HINT, "  hf 14a raw -p -c 3003");
+        PrintAndLogEx(HINT, "  hf 14a raw -p -c 3004");
+        PrintAndLogEx(HINT, "  hf 14a raw -p -c 3005");
+        PrintAndLogEx(HINT, "  hf 14a raw -p -c 3006");
+        PrintAndLogEx(HINT, "  hf 14a raw -c 3007");
+    }
+
     PrintAndLogEx(NORMAL, "");
     DropField();
     return select_status;
