commit 8076e8ec0a590cbdc9c65bff7b2800e177c35959
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Feb 4 21:25:18 2022 +0100

    fix #1583

diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index da732dd42..527fbd41a 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -189,6 +189,7 @@ static const hintAIDList_t hintAIDList[] = {
 
 // iso14a apdu input frame length
 static uint16_t gs_frame_len = 0;
+static uint8_t gs_frames_num = 0;
 static uint16_t atsFSC[] = {16, 24, 32, 40, 48, 64, 96, 128, 256};
 
 static int CmdHF14AList(const char *Cmd) {
@@ -796,14 +797,14 @@ int CmdHF14ASniff(const char *Cmd) {
 }
 
 int ExchangeRAW14a(uint8_t *datain, int datainlen, bool activateField, bool leaveSignalON, uint8_t *dataout, int maxdataoutlen, int *dataoutlen, bool silentMode) {
-    static uint8_t responseNum = 0;
+    
     uint16_t cmdc = 0;
     *dataoutlen = 0;
 
     if (activateField) {
         // select with no disconnect and set gs_frame_len
         int selres = SelectCard14443A_4(false, !silentMode, NULL);
-        responseNum = 0;
+        gs_frames_num = 0;
         if (selres != PM3_SUCCESS)
             return selres;
     }
@@ -811,8 +812,8 @@ int ExchangeRAW14a(uint8_t *datain, int datainlen, bool activateField, bool leav
     if (leaveSignalON)
         cmdc |= ISO14A_NO_DISCONNECT;
 
-    uint8_t data[PM3_CMD_DATA_SIZE] = { 0x0a | responseNum, 0x00};
-    responseNum ^= 1;
+    uint8_t data[PM3_CMD_DATA_SIZE] = { 0x0a | gs_frames_num, 0x00};
+    gs_frames_num ^= 1;
     memcpy(&data[2], datain, datainlen & 0xFFFF);
     SendCommandOLD(CMD_HF_ISO14443A_READER, ISO14A_RAW | ISO14A_APPEND_CRC | cmdc, (datainlen & 0xFFFF) + 2, 0, data, (datainlen & 0xFFFF) + 2);
 
@@ -862,6 +863,7 @@ int SelectCard14443A_4(bool disconnect, bool verbose, iso14a_card_select_t *card
 
     // global vars should be prefixed with g_
     gs_frame_len = 0;
+    gs_frames_num = 0;
 
     if (card) {
         memset(card, 0, sizeof(iso14a_card_select_t));
