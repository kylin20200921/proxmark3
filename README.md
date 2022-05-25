commit 9c2b5118dfaa45a7135b129d1ee402420213dfb3
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Dec 31 09:24:10 2021 +0100

    hf mf gview now support user supplied password

diff --git a/armsrc/appmain.c b/armsrc/appmain.c
index c379951e6..ef95ba3e2 100644
--- a/armsrc/appmain.c
+++ b/armsrc/appmain.c
@@ -1574,12 +1574,13 @@ static void PacketReceived(PacketCommandNG *packet) {
             MifareGen3Freez();
             break;
         }
-        case CMD_HF_MIFARE_G3_RDBL: {
+        case CMD_HF_MIFARE_G4_RDBL: {
             struct p {
                 uint8_t blockno;
+                uint8_t pwd[4];
             } PACKED;
             struct p *payload = (struct p *) packet->data.asBytes;
-            MifareG3ReadBlk(payload->blockno);
+            MifareG4ReadBlk(payload->blockno, payload->pwd);
             break;
         }
         case CMD_HF_MIFARE_PERSONALIZE_UID: {
diff --git a/armsrc/mifarecmd.c b/armsrc/mifarecmd.c
index 42eef11a0..a9247055a 100644
--- a/armsrc/mifarecmd.c
+++ b/armsrc/mifarecmd.c
@@ -2594,7 +2594,7 @@ OUT:
     BigBuf_free();
 }
 
-void MifareG3ReadBlk(uint8_t blockno) {
+void MifareG4ReadBlk(uint8_t blockno, uint8_t *pwd) {
     iso14443a_setup(FPGA_HF_ISO14443A_READER_LISTEN);
     clear_trace();
     set_tracing(true);
@@ -2611,8 +2611,11 @@ void MifareG3ReadBlk(uint8_t blockno) {
     LED_B_ON();
     uint32_t save_iso14a_timeout = iso14a_get_timeout();
     iso14a_set_timeout(13560000 / 1000 / (8 * 16) * 1000); // 2 seconds timeout
-
+    
     uint8_t cmd[] = { 0xCF, 0x00, 0x00, 0x00, 0x00, 0xCE, blockno, 0x00, 0x00};
+
+    memcpy(cmd + 1, pwd, 4);
+
     AddCrc14A(cmd, sizeof(cmd) - 2);
 
     ReaderTransmit(cmd, sizeof(cmd), NULL);
@@ -2624,7 +2627,7 @@ void MifareG3ReadBlk(uint8_t blockno) {
     LED_B_OFF();
 
 OUT:
-    reply_ng(CMD_HF_MIFARE_G3_RDBL, retval, buf, 18);
+    reply_ng(CMD_HF_MIFARE_G4_RDBL, retval, buf, 18);
     // turns off
     OnSuccessMagic();
     BigBuf_free();
diff --git a/armsrc/mifarecmd.h b/armsrc/mifarecmd.h
index bacb8405e..49f1832e7 100644
--- a/armsrc/mifarecmd.h
+++ b/armsrc/mifarecmd.h
@@ -49,8 +49,8 @@ void MifareGen3UID(uint8_t uidlen, uint8_t *uid); // Gen 3 magic card set UID wi
 void MifareGen3Blk(uint8_t block_len, uint8_t *block); // Gen 3 magic card overwrite manufacturer block
 void MifareGen3Freez(void); // Gen 3 magic card lock further UID changes
 
-// MFC GEN3 GTU
-void MifareG3ReadBlk(uint8_t blockno);
+// MFC GEN4 GTU
+void MifareG4ReadBlk(uint8_t blockno, uint8_t *pwd);
 
 void MifareSetMod(uint8_t *datain);
 void MifarePersonalizeUID(uint8_t keyType, uint8_t perso_option, uint64_t key);
diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index 011db0ac8..ea61c460f 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -6133,7 +6133,7 @@ static int CmdHF14AMfView(const char *Cmd) {
     return PM3_SUCCESS;
 }
 
-static int CmdHF14AGen3View(const char *Cmd) {
+static int CmdHF14AGen4View(const char *Cmd) {
 
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mf gview",
@@ -6147,6 +6147,7 @@ static int CmdHF14AGen3View(const char *Cmd) {
         arg_lit0(NULL, "1k", "MIFARE Classic 1k / S50 (def)"),
         arg_lit0(NULL, "2k", "MIFARE Classic/Plus 2k"),
         arg_lit0(NULL, "4k", "MIFARE Classic 4k / S70"),
+        arg_strx0("p", "pwd", "<hex>", "password 4bytes"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -6154,9 +6155,18 @@ static int CmdHF14AGen3View(const char *Cmd) {
     bool m1 = arg_get_lit(ctx, 2);
     bool m2 = arg_get_lit(ctx, 3);
     bool m4 = arg_get_lit(ctx, 4);
+
+    int pwd_len = 0;
+    uint8_t pwd[4] = {0};
+    CLIGetHexWithReturn(ctx, 5, pwd, &pwd_len);
     CLIParserFree(ctx);
 
     // validations
+    if (pwd_len != 4 && pwd_len != 0) {
+        PrintAndLogEx(FAILED, "Must specify 4 bytes, got " _YELLOW_("%u"), pwd_len);
+        return PM3_EINVARG;
+    }
+
     if ((m0 + m1 + m2 + m4) > 1) {
         PrintAndLogEx(WARNING, "Only specify one MIFARE Type");
         return PM3_EINVARG;
@@ -6183,7 +6193,7 @@ static int CmdHF14AGen3View(const char *Cmd) {
         PrintAndLogEx(WARNING, "Please specify a MIFARE Type");
         return PM3_EINVARG;
     }
-    PrintAndLogEx(SUCCESS, "View magic gen3 GTU MIFARE Classic " _GREEN_("%s"), s);
+    PrintAndLogEx(SUCCESS, "View magic gen4 GTU MIFARE Classic " _GREEN_("%s"), s);
     PrintAndLogEx(INFO, "." NOLF);
 
     // Select card to get UID/UIDLEN information
@@ -6221,7 +6231,7 @@ static int CmdHF14AGen3View(const char *Cmd) {
 
     for (uint16_t i = 0; i < block_cnt; i++) {
 
-        if (mfG3GetBlock(i, dump + (i * MFBLOCK_SIZE)) !=  PM3_SUCCESS) {
+        if (mfG4GetBlock(pwd, i, dump + (i * MFBLOCK_SIZE)) !=  PM3_SUCCESS) {
             PrintAndLogEx(WARNING, "Can't get magic card block: %u", i);
             PrintAndLogEx(HINT, "Verify your card size, and try again or try another tag position");
             free(dump);
@@ -6290,8 +6300,8 @@ static command_t CommandTable[] = {
     {"gen3uid",     CmdHf14AGen3UID,        IfPm3Iso14443a,  "Set UID without changing manufacturer block"},
     {"gen3blk",     CmdHf14AGen3Block,      IfPm3Iso14443a,  "Overwrite manufacturer block"},
     {"gen3freeze",  CmdHf14AGen3Freeze,     IfPm3Iso14443a,  "Perma lock UID changes. irreversible"},
-    {"-----------", CmdHelp,                IfPm3Iso14443a,  "----------------------- " _CYAN_("magic gen3 GTU") " -----------------------"},
-    {"gview",       CmdHF14AGen3View,       IfPm3Iso14443a,  "View card"},
+    {"-----------", CmdHelp,                IfPm3Iso14443a,  "----------------------- " _CYAN_("magic gen4 GTU") " -----------------------"},
+    {"gview",       CmdHF14AGen4View,       IfPm3Iso14443a,  "View card"},
 //    {"-----------", CmdHelp,                IfPm3Iso14443a,  "----------------------- " _CYAN_("i") " -----------------------"},
 //    {"ice",         CmdHF14AMfice,          IfPm3Iso14443a,  "collect MIFARE Classic nonces to file"},
     {NULL, NULL, NULL, NULL}
diff --git a/client/src/mifare/mifarehost.c b/client/src/mifare/mifarehost.c
index 4d36098f3..4b5e4e5a1 100644
--- a/client/src/mifare/mifarehost.c
+++ b/client/src/mifare/mifarehost.c
@@ -1069,18 +1069,21 @@ int mfGen3Freeze(void) {
     }
 }
 
-int mfG3GetBlock(uint8_t blockno, uint8_t *data) {
+int mfG4GetBlock(uint8_t *pwd, uint8_t blockno, uint8_t *data) {
     struct p {
         uint8_t blockno;
+        uint8_t pwd[4];
     } PACKED payload;
     payload.blockno = blockno;
+    memcpy(payload.pwd, pwd, sizeof(payload.pwd));
 
     clearCommandBuffer();
-    SendCommandNG(CMD_HF_MIFARE_G3_RDBL, (uint8_t *)&payload, sizeof(payload));
+    SendCommandNG(CMD_HF_MIFARE_G4_RDBL, (uint8_t *)&payload, sizeof(payload));
     PacketResponseNG resp;
-    if (WaitForResponseTimeout(CMD_HF_MIFARE_G3_RDBL, &resp, 1500)) {
-        if (resp.status != PM3_SUCCESS)
+    if (WaitForResponseTimeout(CMD_HF_MIFARE_G4_RDBL, &resp, 1500)) {
+        if (resp.status != PM3_SUCCESS) {
             return PM3_EUNDEF;
+        }
         memcpy(data, resp.data.asBytes, 16);
     } else {
         PrintAndLogEx(WARNING, "command execute timeout");
diff --git a/client/src/mifare/mifarehost.h b/client/src/mifare/mifarehost.h
index 8f5f215fd..56cc5d841 100644
--- a/client/src/mifare/mifarehost.h
+++ b/client/src/mifare/mifarehost.h
@@ -85,7 +85,7 @@ int mfGen3UID(uint8_t *uid, uint8_t uidlen, uint8_t *oldUid);
 int mfGen3Block(uint8_t *block, int blockLen, uint8_t *newBlock);
 int mfGen3Freeze(void);
 
-int mfG3GetBlock(uint8_t blockno, uint8_t *data);
+int mfG4GetBlock(uint8_t *pwd, uint8_t blockno, uint8_t *data);
 
 int tryDecryptWord(uint32_t nt, uint32_t ar_enc, uint32_t at_enc, uint8_t *data, int len);
 
diff --git a/include/pm3_cmd.h b/include/pm3_cmd.h
index 88b7cb730..c8f4ab4d7 100644
--- a/include/pm3_cmd.h
+++ b/include/pm3_cmd.h
@@ -746,8 +746,8 @@ typedef struct {
 #define CMD_HF_MIFARE_GEN3BLK                                             0x0851
 #define CMD_HF_MIFARE_GEN3FREEZ                                           0x0852
 
-// Gen 3 GTU magic cards
-#define CMD_HF_MIFARE_G3_RDBL                                             0x0860
+// Gen 4 GTU magic cards
+#define CMD_HF_MIFARE_G4_RDBL                                             0x0860
 
 #define CMD_UNKNOWN                                                       0xFFFF
 
