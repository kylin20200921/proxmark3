commit 37b89c7b119c4f66aa4bef8f9ed7f8bc338bc080
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Oct 17 11:38:37 2021 +0200

    add ehanced contactless polling to 14a reader/raw commands

diff --git a/armsrc/iso14443a.c b/armsrc/iso14443a.c
index 60f510269..8df4729ab 100644
--- a/armsrc/iso14443a.c
+++ b/armsrc/iso14443a.c
@@ -2427,11 +2427,12 @@ static void iso14a_set_ATS_times(uint8_t *ats) {
     }
 }
 
-static int GetATQA(uint8_t *resp, uint8_t *resp_par) {
+static int GetATQA(uint8_t *resp, uint8_t *resp_par, bool use_ecp) {
 
+#define ECP_DELAY 1000
 #define WUPA_RETRY_TIMEOUT 10    // 10ms
-    uint8_t wupa[] = { ISO14443A_CMD_WUPA };  // 0x26 - REQA  0x52 - WAKE-UP
 
+    uint8_t wupa[] = { ISO14443A_CMD_WUPA };  // 0x26 - REQA  0x52 - WAKE-UP
     uint32_t save_iso14a_timeout = iso14a_get_timeout();
     iso14a_set_timeout(1236 / 128 + 1);  // response to WUPA is expected at exactly 1236/fc. No need to wait longer.
 
@@ -2440,6 +2441,12 @@ static int GetATQA(uint8_t *resp, uint8_t *resp_par) {
 
     // we may need several tries if we did send an unknown command or a wrong authentication before...
     do {
+        if (use_ecp) {
+            uint8_t ecp[] = { 0x6a, 0x02, 0xC8, 0x01, 0x00, 0x03, 0x00, 0x02, 0x79, 0x00, 0x00, 0x00, 0x00, 0xC2, 0xD8};
+            ReaderTransmit(ecp, sizeof(ecp), NULL);
+            SpinDelay(ECP_DELAY);
+        }
+
         // Broadcast for a card, WUPA (0x52) will force response from all cards in the field
         ReaderTransmitBitsPar(wupa, 7, NULL, NULL);
         // Receive the ATQA
@@ -2450,13 +2457,17 @@ static int GetATQA(uint8_t *resp, uint8_t *resp_par) {
     return len;
 }
 
+int iso14443a_select_card(uint8_t *uid_ptr, iso14a_card_select_t *p_card, uint32_t *cuid_ptr, bool anticollision, uint8_t num_cascades, bool no_rats) {
+    return iso14443a_select_cardEx(uid_ptr, p_card, cuid_ptr, anticollision, num_cascades, no_rats, false);
+}
+
 // performs iso14443a anticollision (optional) and card select procedure
 // fills the uid and cuid pointer unless NULL
 // fills the card info record unless NULL
 // if anticollision is false, then the UID must be provided in uid_ptr[]
 // and num_cascades must be set (1: 4 Byte UID, 2: 7 Byte UID, 3: 10 Byte UID)
 // requests ATS unless no_rats is true
-int iso14443a_select_card(uint8_t *uid_ptr, iso14a_card_select_t *p_card, uint32_t *cuid_ptr, bool anticollision, uint8_t num_cascades, bool no_rats) {
+int iso14443a_select_cardEx(uint8_t *uid_ptr, iso14a_card_select_t *p_card, uint32_t *cuid_ptr, bool anticollision, uint8_t num_cascades, bool no_rats, bool use_ecp) {
 
     uint8_t resp[MAX_FRAME_SIZE] = {0}; // theoretically. A usual RATS will be much smaller
     uint8_t resp_par[MAX_PARITY_SIZE] = {0};
@@ -2471,7 +2482,7 @@ int iso14443a_select_card(uint8_t *uid_ptr, iso14a_card_select_t *p_card, uint32
         p_card->ats_len = 0;
     }
 
-    if (!GetATQA(resp, resp_par)) {
+    if (!GetATQA(resp, resp_par, use_ecp)) {
         return 0;
     }
 
@@ -2669,7 +2680,7 @@ int iso14443a_fast_select_card(uint8_t *uid_ptr, uint8_t num_cascades) {
     uint8_t sak = 0x04; // cascade uid
     int cascade_level = 0;
 
-    if (!GetATQA(resp, resp_par)) {
+    if (!GetATQA(resp, resp_par, false)) {
         return 0;
     }
 
@@ -2878,7 +2889,7 @@ void ReaderIso14443a(PacketCommandNG *c) {
         // if failed selecting, turn off antenna and quite.
         if (!(param & ISO14A_NO_SELECT)) {
             iso14a_card_select_t *card = (iso14a_card_select_t *)buf;
-            arg0 = iso14443a_select_card(NULL, card, NULL, true, 0, param & ISO14A_NO_RATS);
+            arg0 = iso14443a_select_cardEx(NULL, card, NULL, true, 0, (param & ISO14A_NO_RATS), (param & ISO14A_USE_ECP));
             FpgaDisableTracing();
 
             reply_mix(CMD_ACK, arg0, card->uidlen, 0, buf, sizeof(iso14a_card_select_t));
diff --git a/armsrc/iso14443a.h b/armsrc/iso14443a.h
index 54764d223..8acbc8716 100644
--- a/armsrc/iso14443a.h
+++ b/armsrc/iso14443a.h
@@ -142,6 +142,7 @@ int ReaderReceive(uint8_t *receivedAnswer, uint8_t *par);
 void iso14443a_setup(uint8_t fpga_minor_mode);
 int iso14_apdu(uint8_t *cmd, uint16_t cmd_len, bool send_chaining, void *data, uint8_t *res);
 int iso14443a_select_card(uint8_t *uid_ptr, iso14a_card_select_t *p_card, uint32_t *cuid_ptr, bool anticollision, uint8_t num_cascades, bool no_rats);
+int iso14443a_select_cardEx(uint8_t *uid_ptr, iso14a_card_select_t *p_card, uint32_t *cuid_ptr, bool anticollision, uint8_t num_cascades, bool no_rats, bool use_ecp);
 int iso14443a_fast_select_card(uint8_t *uid_ptr, uint8_t num_cascades);
 void iso14a_set_trigger(bool enable);
 
diff --git a/client/lualibs/read14a.lua b/client/lualibs/read14a.lua
index 59de3c11f..beaea0fe5 100644
--- a/client/lualibs/read14a.lua
+++ b/client/lualibs/read14a.lua
@@ -27,7 +27,9 @@ local ISO14A_COMMAND = {
     ISO14A_SET_TIMEOUT = 0x40,
     ISO14A_NO_SELECT = 0x80,
     ISO14A_TOPAZMODE = 0x100,
-    ISO14A_NO_RATS = 0x200
+    ISO14A_NO_RATS = 0x200,
+    ISO14A_SEND_CHAINING = 0x400,
+    ISO14A_USE_ECP = 0x800
 }
 
 local ISO14443a_TYPES = {}
diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index 053b1f60a..213ac6753 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -425,6 +425,7 @@ static int CmdHF14AReader(const char *Cmd) {
         arg_lit0("s", "silent", "silent (no messages)"),
         arg_lit0(NULL, "drop", "just drop the signal field"),
         arg_lit0(NULL, "skip", "ISO14443-3 select only (skip RATS)"),
+        arg_lit0(NULL, "ecp", "Use enhanced contactless polling"),
         arg_lit0("@", NULL, "continuous reader mode"),
         arg_param_end
     };
@@ -446,7 +447,11 @@ static int CmdHF14AReader(const char *Cmd) {
         cm |= ISO14A_NO_RATS;
     }
 
-    bool continuous = arg_get_lit(ctx, 5);
+    if (arg_get_lit(ctx, 5)) {
+        cm |= ISO14A_USE_ECP;
+    }
+
+    bool continuous = arg_get_lit(ctx, 6);
 
     CLIParserFree(ctx);
 
@@ -1214,6 +1219,7 @@ static int CmdHF14ACmdRaw(const char *Cmd) {
         arg_int0("t",  "timeout", "<ms>", "timeout in milliseconds"),
         arg_lit0("v",  "verbose", "Verbose output"),
         arg_lit0(NULL, "topaz", "use Topaz protocol to send command"),
+        arg_lit0(NULL, "ecp", "Use enhanced contactless polling"),        
         arg_strx1(NULL, NULL, "<hex>", "raw bytes to send"),
         arg_param_end
     };
@@ -1229,10 +1235,11 @@ static int CmdHF14ACmdRaw(const char *Cmd) {
     uint32_t timeout = (uint32_t)arg_get_int_def(ctx, 8, 0);
     bool verbose = arg_get_lit(ctx, 9);
     bool topazmode = arg_get_lit(ctx, 10);
+    bool use_ecp = arg_get_lit(ctx, 11);
 
     int datalen = 0;
     uint8_t data[PM3_CMD_DATA_SIZE];
-    CLIGetHexWithReturn(ctx, 11, data, &datalen);
+    CLIGetHexWithReturn(ctx, 12, data, &datalen);
     CLIParserFree(ctx);
 
     bool bTimeout = (timeout) ? true : false;
@@ -1288,6 +1295,9 @@ static int CmdHF14ACmdRaw(const char *Cmd) {
     if (no_rats) {
         flags |= ISO14A_NO_RATS;
     }
+    if (use_ecp){
+        flags |= ISO14A_USE_ECP;
+    }
 
     // Max buffer is PM3_CMD_DATA_SIZE
     datalen = (datalen > PM3_CMD_DATA_SIZE) ? PM3_CMD_DATA_SIZE : datalen;
diff --git a/include/mifare.h b/include/mifare.h
index 1c8fc7f42..0a0156ef7 100644
--- a/include/mifare.h
+++ b/include/mifare.h
@@ -65,7 +65,8 @@ typedef enum ISO14A_COMMAND {
     ISO14A_NO_SELECT = (1 << 7),
     ISO14A_TOPAZMODE = (1 << 8),
     ISO14A_NO_RATS = (1 << 9),
-    ISO14A_SEND_CHAINING = (1 << 10)
+    ISO14A_SEND_CHAINING = (1 << 10),
+    ISO14A_USE_ECP = (1 << 11)
 } iso14a_command_t;
 
 typedef struct {
