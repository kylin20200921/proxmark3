commit e8d5d811782b66a8d9eb40b2d94e0a398abc70b3
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jun 20 10:41:47 2021 +0200

    code style,  and make less output for hf search

diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index fd8d9db9a..b221869d7 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -181,7 +181,7 @@ static const hintAIDListT hintAIDList[] = {
 };
 
 // iso14a apdu input frame length
-static uint16_t frameLength = 0;
+static uint16_t g_frame_len = 0;
 uint16_t atsFSC[] = {16, 24, 32, 40, 48, 64, 96, 128, 256};
 
 static int CmdHF14AList(const char *Cmd) {
@@ -857,31 +857,35 @@ int ExchangeRAW14a(uint8_t *datain, int datainlen, bool activateField, bool leav
     return 0;
 }
 
-int SelectCard14443A_4(bool disconnect, iso14a_card_select_t *card) {
-    PacketResponseNG resp;
+int SelectCard14443A_4(bool disconnect, bool verbose, iso14a_card_select_t *card) {
 
-    frameLength = 0;
+    // global vars should be prefixed with g_
+    g_frame_len = 0;
 
-    if (card)
+    if (card) {
         memset(card, 0, sizeof(iso14a_card_select_t));
+    }
 
     DropField();
 
     // Anticollision + SELECT card
+    PacketResponseNG resp;
     SendCommandMIX(CMD_HF_ISO14443A_READER, ISO14A_CONNECT | ISO14A_NO_DISCONNECT, 0, 0, NULL, 0);
     if (WaitForResponseTimeout(CMD_ACK, &resp, 1500) == false) {
-        PrintAndLogEx(ERR, "Proxmark3 connection timeout");
+        PrintAndLogEx(WARNING, "Command execute timeout");
         return PM3_ETIMEOUT;
     }
 
     // check result
     if (resp.oldarg[0] == 0) {
-        PrintAndLogEx(ERR, "No card in field");
+        if (verbose) {
+            PrintAndLogEx(FAILED, "No card in field");
+        }
         return PM3_ECARDEXCHANGE;
     }
 
     if (resp.oldarg[0] != 1 && resp.oldarg[0] != 2) {
-        PrintAndLogEx(ERR, "Card not in iso14443-4, res=%" PRId64 ".", resp.oldarg[0]);
+        PrintAndLogEx(WARNING, "Card not in iso14443-4, res=%" PRId64 ".", resp.oldarg[0]);
         return PM3_ECARDEXCHANGE;
     }
 
@@ -890,36 +894,44 @@ int SelectCard14443A_4(bool disconnect, iso14a_card_select_t *card) {
         uint8_t rats[] = { 0xE0, 0x80 }; // FSDI=8 (FSD=256), CID=0
         SendCommandMIX(CMD_HF_ISO14443A_READER, ISO14A_RAW | ISO14A_APPEND_CRC | ISO14A_NO_DISCONNECT, sizeof(rats), 0, rats, sizeof(rats));
         if (WaitForResponseTimeout(CMD_ACK, &resp, 1500) == false) {
-            PrintAndLogEx(ERR, "Proxmark3 connection timeout");
+            PrintAndLogEx(WARNING, "Command execute timeout");
             return PM3_ETIMEOUT;
         }
 
         if (resp.oldarg[0] == 0) { // ats_len
-            PrintAndLogEx(ERR, "Can't get ATS");
+            if (verbose) {
+                PrintAndLogEx(FAILED, "Can't get ATS");
+            }
             return PM3_ECARDEXCHANGE;
         }
 
         // get frame length from ATS in data field
         if (resp.oldarg[0] > 1) {
             uint8_t fsci = resp.data.asBytes[1] & 0x0f;
-            if (fsci < ARRAYLEN(atsFSC))
-                frameLength = atsFSC[fsci];
+            if (fsci < ARRAYLEN(atsFSC)) {
+                g_frame_len = atsFSC[fsci];
+            }
         }
     } else {
         // get frame length from ATS in card data structure
         iso14a_card_select_t *vcard = (iso14a_card_select_t *) resp.data.asBytes;
         if (vcard->ats_len > 1) {
             uint8_t fsci = vcard->ats[1] & 0x0f;
-            if (fsci < ARRAYLEN(atsFSC))
-                frameLength = atsFSC[fsci];
+            if (fsci < ARRAYLEN(atsFSC)) {
+                g_frame_len = atsFSC[fsci];
+            }
         }
 
-        if (card)
+        if (card) {
             memcpy(card, vcard, sizeof(iso14a_card_select_t));
+        }
     }
+
     SetISODEPState(ISODEP_NFCA);
-    if (disconnect)
+
+    if (disconnect) {
         DropField();
+    }
 
     return PM3_SUCCESS;
 }
@@ -928,8 +940,8 @@ static int CmdExchangeAPDU(bool chainingin, uint8_t *datain, int datainlen, bool
     *chainingout = false;
 
     if (activateField) {
-        // select with no disconnect and set frameLength
-        int selres = SelectCard14443A_4(false, NULL);
+        // select with no disconnect and set g_frame_len
+        int selres = SelectCard14443A_4(false, true, NULL);
         if (selres != PM3_SUCCESS)
             return selres;
     }
@@ -965,7 +977,7 @@ static int CmdExchangeAPDU(bool chainingin, uint8_t *datain, int datainlen, bool
         }
 
         // I-block ACK
-        if ((res & 0xf2) == 0xa2) {
+        if ((res & 0xF2) == 0xA2) {
             *dataoutlen = 0;
             *chainingout = true;
             return PM3_SUCCESS;
@@ -1015,13 +1027,14 @@ int ExchangeAPDU14a(uint8_t *datain, int datainlen, bool activateField, bool lea
 
     // 3 byte here - 1b framing header, 2b crc16
     if (APDUInFramingEnable &&
-            ((frameLength && (datainlen > frameLength - 3)) || (datainlen > PM3_CMD_DATA_SIZE - 3))) {
+        ((g_frame_len && (datainlen > g_frame_len - 3)) || (datainlen > PM3_CMD_DATA_SIZE - 3))) {
+
         int clen = 0;
 
         bool vActivateField = activateField;
 
         do {
-            int vlen = MIN(frameLength - 3, datainlen - clen);
+            int vlen = MIN(g_frame_len - 3, datainlen - clen);
             bool chainBlockNotLast = ((clen + vlen) < datainlen);
 
             *dataoutlen = 0;
@@ -1045,17 +1058,19 @@ int ExchangeAPDU14a(uint8_t *datain, int datainlen, bool activateField, bool lea
             clen += vlen;
             vActivateField = false;
             if (*dataoutlen) {
-                if (clen != datainlen)
+                if (clen != datainlen) {
                     PrintAndLogEx(ERR, "APDU: I-block/R-block sequence error. Data len=%d, Sent=%d, Last packet len=%d", datainlen, clen, *dataoutlen);
+                }
                 break;
             }
         } while (clen < datainlen);
+
     } else {
         res = CmdExchangeAPDU(false, datain, datainlen, activateField, dataout, maxdataoutlen, dataoutlen, &chaining);
         if (res != PM3_SUCCESS) {
-            if (leaveSignalON == false)
+            if (leaveSignalON == false) {
                 DropField();
-
+            }
             return res;
         }
     }
@@ -1064,15 +1079,16 @@ int ExchangeAPDU14a(uint8_t *datain, int datainlen, bool activateField, bool lea
         // I-block with chaining
         res = CmdExchangeAPDU(false, NULL, 0, false, &dataout[*dataoutlen], maxdataoutlen, dataoutlen, &chaining);
         if (res != PM3_SUCCESS) {
-            if (leaveSignalON == false)
+            if (leaveSignalON == false) {
                 DropField();
-
+            }
             return 100;
         }
     }
 
-    if (!leaveSignalON)
+    if (leaveSignalON == false) {
         DropField();
+    }
 
     return PM3_SUCCESS;
 }
diff --git a/client/src/cmdhf14a.h b/client/src/cmdhf14a.h
index 484b43cfd..18e9fffd7 100644
--- a/client/src/cmdhf14a.h
+++ b/client/src/cmdhf14a.h
@@ -43,5 +43,5 @@ int Hf14443_4aGetCardData(iso14a_card_select_t *card);
 int ExchangeAPDU14a(uint8_t *datain, int datainlen, bool activateField, bool leaveSignalON, uint8_t *dataout, int maxdataoutlen, int *dataoutlen);
 int ExchangeRAW14a(uint8_t *datain, int datainlen, bool activateField, bool leaveSignalON, uint8_t *dataout, int maxdataoutlen, int *dataoutlen, bool silentMode);
 
-int SelectCard14443A_4(bool disconnect, iso14a_card_select_t *card);
+int SelectCard14443A_4(bool disconnect, bool verbose, iso14a_card_select_t *card);
 #endif
diff --git a/client/src/cmdhfmfdes.c b/client/src/cmdhfmfdes.c
index bec41af0b..52d26bb7f 100644
--- a/client/src/cmdhfmfdes.c
+++ b/client/src/cmdhfmfdes.c
@@ -3685,7 +3685,7 @@ static int CmdHF14ADesInfo(const char *Cmd) {
 
 
     iso14a_card_select_t card;
-    res = SelectCard14443A_4(true, &card);
+    res = SelectCard14443A_4(true, false, &card);
     if (res == PM3_SUCCESS) {
         static const char STANDALONE_DESFIRE[] = { 0x75, 0x77, 0x81, 0x02};
         static const char JCOP_DESFIRE[] = { 0x75, 0xf7, 0xb1, 0x02 };
diff --git a/client/src/iso7816/iso7816core.c b/client/src/iso7816/iso7816core.c
index 9c336c0bc..3907d7744 100644
--- a/client/src/iso7816/iso7816core.c
+++ b/client/src/iso7816/iso7816core.c
@@ -55,7 +55,7 @@ int Iso7816Connect(Iso7816CommandChannel channel) {
     }
     // Try to 14a
     // select with no disconnect and set frameLength
-    int res = SelectCard14443A_4(false, NULL);
+    int res = SelectCard14443A_4(false, false, NULL);
     if (res == PM3_SUCCESS) {
         SetISODEPState(ISODEP_NFCA);
         return PM3_SUCCESS;
