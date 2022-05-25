commit 98d291f5b163041334e3ca9abe9e8c2a41f68044
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 22 18:29:11 2022 +0100

    fix hf 14a sim,   on device side a sneaky detection if data started with zero,  it would treat it as uid is in emulator memory. Also fixed a size issue on flag

diff --git a/CHANGELOG.md b/CHANGELOG.md
index 4d6e91a2e..101cce452 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,7 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Fix `hf 14a sim` - sneaky detection of user supplied UID might be empty (@iceman1001)
  - Adds support for PCSC's proprietary HID 37bit format P10004 (@bthedorff)
  - Add `nfc decode` - now NDEF vCard messages with a PHOTO in base64 format is shown (@iceman1001)
  - Remove AID limitations when using Gallagher key diversification (@DarkMatterMatt)
diff --git a/armsrc/Standalone/hf_aveful.c b/armsrc/Standalone/hf_aveful.c
index fd9b7ff36..7363a952e 100644
--- a/armsrc/Standalone/hf_aveful.c
+++ b/armsrc/Standalone/hf_aveful.c
@@ -248,7 +248,7 @@ void RunMod(void) {
                     state = STATE_SEARCH;
                 }
             } else if (state == STATE_EMUL) {
-                uint8_t flags = FLAG_7B_UID_IN_DATA;
+                uint16_t flags = FLAG_7B_UID_IN_DATA;
 
                 Dbprintf("Starting simulation, press pm3-button to stop and go back to search state.");
                 SimulateIso14443aTag(7, flags, card.uid, 0);
diff --git a/armsrc/Standalone/hf_craftbyte.c b/armsrc/Standalone/hf_craftbyte.c
index 4e38284cc..80733a3cc 100644
--- a/armsrc/Standalone/hf_craftbyte.c
+++ b/armsrc/Standalone/hf_craftbyte.c
@@ -78,7 +78,7 @@ void RunMod(void) {
                     state = STATE_EMUL;
                 }
             } else if (state == STATE_EMUL) {
-                uint8_t flags = 0;
+                uint16_t flags = 0;
                 if (card.uidlen == 4) {
                     flags |= FLAG_4B_UID_IN_DATA;
                 } else if (card.uidlen == 7) {
diff --git a/armsrc/Standalone/hf_young.c b/armsrc/Standalone/hf_young.c
index 725b6bc28..71376de8c 100644
--- a/armsrc/Standalone/hf_young.c
+++ b/armsrc/Standalone/hf_young.c
@@ -236,7 +236,7 @@ void RunMod(void) {
                 int button_pressed = BUTTON_HELD(1000);
                 if (button_pressed == BUTTON_NO_CLICK) {  // No button action, proceed with sim
 
-                    uint8_t flags = FLAG_4B_UID_IN_DATA;
+                    uint16_t flags = FLAG_4B_UID_IN_DATA;
                     uint8_t data[PM3_CMD_DATA_SIZE] = {0}; // in case there is a read command received we shouldn't break
 
                     memcpy(data, uids[selected].uid, uids[selected].uidlen);
diff --git a/armsrc/appmain.c b/armsrc/appmain.c
index a37b3aa78..22871a00d 100644
--- a/armsrc/appmain.c
+++ b/armsrc/appmain.c
@@ -1395,7 +1395,7 @@ static void PacketReceived(PacketCommandNG *packet) {
         case CMD_HF_ISO14443A_SIMULATE: {
             struct p {
                 uint8_t tagtype;
-                uint8_t flags;
+                uint16_t flags;
                 uint8_t uid[10];
                 uint8_t exitAfter;
             } PACKED;
diff --git a/armsrc/iso14443a.c b/armsrc/iso14443a.c
index ab69c41fc..1e867e60e 100644
--- a/armsrc/iso14443a.c
+++ b/armsrc/iso14443a.c
@@ -1007,7 +1007,7 @@ bool prepare_allocated_tag_modulation(tag_response_info_t *response_info, uint8_
     }
 }
 
-bool SimulateIso14443aInit(int tagType, int flags, uint8_t *data, tag_response_info_t **responses, uint32_t *cuid, uint32_t counters[3], uint8_t tearings[3], uint8_t *pages) {
+bool SimulateIso14443aInit(uint8_t tagType, uint16_t flags, uint8_t *data, tag_response_info_t **responses, uint32_t *cuid, uint32_t counters[3], uint8_t tearings[3], uint8_t *pages) {
     uint8_t sak = 0;
     // The first response contains the ATQA (note: bytes are transmitted in reverse order).
     static uint8_t rATQA[2] = { 0x00 };
@@ -1128,7 +1128,7 @@ bool SimulateIso14443aInit(int tagType, int flags, uint8_t *data, tag_response_i
     }
 
     // if uid not supplied then get from emulator memory
-    if (data[0] == 0 || (flags & FLAG_UID_IN_EMUL) == FLAG_UID_IN_EMUL) {
+    if ( (memcmp(data, "\x00\x00\x00\x00", 4) == 0) || ((flags & FLAG_UID_IN_EMUL) == FLAG_UID_IN_EMUL)) {
         if (tagType == 2 || tagType == 7) {
             uint16_t start = MFU_DUMP_PREFIX_LENGTH;
             uint8_t emdata[8];
@@ -1178,6 +1178,7 @@ bool SimulateIso14443aInit(int tagType, int flags, uint8_t *data, tag_response_i
 
         *cuid = bytes_to_num(data + 3, 4);
     } else if ((flags & FLAG_10B_UID_IN_DATA) == FLAG_10B_UID_IN_DATA) {
+
         rUIDc1[0] = 0x88;  // Cascade Tag marker
         rUIDc1[1] = data[0];
         rUIDc1[2] = data[1];
@@ -1265,7 +1266,7 @@ bool SimulateIso14443aInit(int tagType, int flags, uint8_t *data, tag_response_i
 // response to send, and send it.
 // 'hf 14a sim'
 //-----------------------------------------------------------------------------
-void SimulateIso14443aTag(uint8_t tagType, uint8_t flags, uint8_t *data, uint8_t exitAfterNReads) {
+void SimulateIso14443aTag(uint8_t tagType, uint16_t flags, uint8_t *data, uint8_t exitAfterNReads) {
 
 #define ATTACK_KEY_COUNT 8 // keep same as define in cmdhfmf.c -> readerAttack()
 
diff --git a/armsrc/iso14443a.h b/armsrc/iso14443a.h
index a90e88dcc..08b73aad2 100644
--- a/armsrc/iso14443a.h
+++ b/armsrc/iso14443a.h
@@ -137,8 +137,8 @@ RAMFUNC bool MillerDecoding(uint8_t bit, uint32_t non_real_time);
 RAMFUNC int ManchesterDecoding(uint8_t bit, uint16_t offset, uint32_t non_real_time);
 
 void RAMFUNC SniffIso14443a(uint8_t param);
-void SimulateIso14443aTag(uint8_t tagType, uint8_t flags, uint8_t *data, uint8_t exitAfterNReads);
-bool SimulateIso14443aInit(int tagType, int flags, uint8_t *data, tag_response_info_t **responses, uint32_t *cuid, uint32_t counters[3], uint8_t tearings[3], uint8_t *pages);
+void SimulateIso14443aTag(uint8_t tagType, uint16_t flags, uint8_t *data, uint8_t exitAfterNReads);
+bool SimulateIso14443aInit(uint8_t tagType, uint16_t flags, uint8_t *data, tag_response_info_t **responses, uint32_t *cuid, uint32_t counters[3], uint8_t tearings[3], uint8_t *pages);
 bool GetIso14443aCommandFromReader(uint8_t *received, uint8_t *par, int *len);
 void iso14443a_antifuzz(uint32_t flags);
 void ReaderIso14443a(PacketCommandNG *c);
diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index 9317147ea..f8c959072 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -676,7 +676,7 @@ int CmdHF14ASim(const char *Cmd) {
     uint8_t uid[10] = {0};
     CLIGetHexWithReturn(ctx, 2, uid, &uid_len);
 
-    uint8_t flags = 0;
+    uint16_t flags = 0;
     bool useUIDfromEML = true;
 
     if (uid_len > 0) {
@@ -719,7 +719,7 @@ int CmdHF14ASim(const char *Cmd) {
 
     struct {
         uint8_t tagtype;
-        uint8_t flags;
+        uint16_t flags;
         uint8_t uid[10];
         uint8_t exitAfter;
     } PACKED payload;
