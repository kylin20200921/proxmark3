commit 0e5e9163a147201050798e87499df0ec9542e9ad
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Oct 23 18:54:44 2021 +0200

    added apple magsafe polling, thanks to @tcprst

diff --git a/CHANGELOG.md b/CHANGELOG.md
index e26298b8c..818d858ad 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,8 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Changed `trace list -t 14a` - now annotate magsafe wupa (@iceman1001)
+ - Added magsafe polling (thanks to @tcprst !) (@iceman1001)
  - Changed `trace list -t 14a` - now annotate ECP (@iceman1001)
  - Added enhanced contactless polling (thanks to @relays user at gitlab) (@iceman1001)
  - Added iCS key (@FlUxIuS)
diff --git a/armsrc/iso14443a.c b/armsrc/iso14443a.c
index bccfa3109..5b317ee71 100644
--- a/armsrc/iso14443a.c
+++ b/armsrc/iso14443a.c
@@ -2427,12 +2427,20 @@ static void iso14a_set_ATS_times(uint8_t *ats) {
     }
 }
 
-static int GetATQA(uint8_t *resp, uint8_t *resp_par, bool use_ecp) {
+static int GetATQA(uint8_t *resp, uint8_t *resp_par, bool use_ecp, bool use_magsafe) {
 
 #define ECP_DELAY 15
 #define WUPA_RETRY_TIMEOUT 10    // 10ms
 
-    uint8_t wupa[] = { ISO14443A_CMD_WUPA };  // 0x26 - REQA  0x52 - WAKE-UP
+    // 0x26 - REQA
+    // 0x52 - WAKE-UP
+    // 0x7A - MAGESAFE WAKE UP
+    uint8_t wupa[] = { ISO14443A_CMD_WUPA };
+
+    if (use_magsafe) {
+        wupa[0] = MAGSAFE_CMD_WUPA;
+    }
+
     uint32_t save_iso14a_timeout = iso14a_get_timeout();
     iso14a_set_timeout(1236 / 128 + 1);  // response to WUPA is expected at exactly 1236/fc. No need to wait longer.
 
@@ -2458,7 +2466,7 @@ static int GetATQA(uint8_t *resp, uint8_t *resp_par, bool use_ecp) {
 }
 
 int iso14443a_select_card(uint8_t *uid_ptr, iso14a_card_select_t *p_card, uint32_t *cuid_ptr, bool anticollision, uint8_t num_cascades, bool no_rats) {
-    return iso14443a_select_cardEx(uid_ptr, p_card, cuid_ptr, anticollision, num_cascades, no_rats, false);
+    return iso14443a_select_cardEx(uid_ptr, p_card, cuid_ptr, anticollision, num_cascades, no_rats, false, false);
 }
 
 // performs iso14443a anticollision (optional) and card select procedure
@@ -2467,7 +2475,7 @@ int iso14443a_select_card(uint8_t *uid_ptr, iso14a_card_select_t *p_card, uint32
 // if anticollision is false, then the UID must be provided in uid_ptr[]
 // and num_cascades must be set (1: 4 Byte UID, 2: 7 Byte UID, 3: 10 Byte UID)
 // requests ATS unless no_rats is true
-int iso14443a_select_cardEx(uint8_t *uid_ptr, iso14a_card_select_t *p_card, uint32_t *cuid_ptr, bool anticollision, uint8_t num_cascades, bool no_rats, bool use_ecp) {
+int iso14443a_select_cardEx(uint8_t *uid_ptr, iso14a_card_select_t *p_card, uint32_t *cuid_ptr, bool anticollision, uint8_t num_cascades, bool no_rats, bool use_ecp, bool use_magsafe) {
 
     uint8_t resp[MAX_FRAME_SIZE] = {0}; // theoretically. A usual RATS will be much smaller
     uint8_t resp_par[MAX_PARITY_SIZE] = {0};
@@ -2482,7 +2490,7 @@ int iso14443a_select_cardEx(uint8_t *uid_ptr, iso14a_card_select_t *p_card, uint
         p_card->ats_len = 0;
     }
 
-    if (!GetATQA(resp, resp_par, use_ecp)) {
+    if (!GetATQA(resp, resp_par, use_ecp, use_magsafe)) {
         return 0;
     }
 
@@ -2680,7 +2688,7 @@ int iso14443a_fast_select_card(uint8_t *uid_ptr, uint8_t num_cascades) {
     uint8_t sak = 0x04; // cascade uid
     int cascade_level = 0;
 
-    if (!GetATQA(resp, resp_par, false)) {
+    if (!GetATQA(resp, resp_par, false, false)) {
         return 0;
     }
 
@@ -2889,7 +2897,7 @@ void ReaderIso14443a(PacketCommandNG *c) {
         // if failed selecting, turn off antenna and quite.
         if (!(param & ISO14A_NO_SELECT)) {
             iso14a_card_select_t *card = (iso14a_card_select_t *)buf;
-            arg0 = iso14443a_select_cardEx(NULL, card, NULL, true, 0, (param & ISO14A_NO_RATS), (param & ISO14A_USE_ECP));
+            arg0 = iso14443a_select_cardEx(NULL, card, NULL, true, 0, (param & ISO14A_NO_RATS), (param & ISO14A_USE_ECP), (param & ISO14A_USE_MAGSAFE));
             FpgaDisableTracing();
 
             reply_mix(CMD_ACK, arg0, card->uidlen, 0, buf, sizeof(iso14a_card_select_t));
diff --git a/armsrc/iso14443a.h b/armsrc/iso14443a.h
index 8acbc8716..2eebf0f8c 100644
--- a/armsrc/iso14443a.h
+++ b/armsrc/iso14443a.h
@@ -142,7 +142,7 @@ int ReaderReceive(uint8_t *receivedAnswer, uint8_t *par);
 void iso14443a_setup(uint8_t fpga_minor_mode);
 int iso14_apdu(uint8_t *cmd, uint16_t cmd_len, bool send_chaining, void *data, uint8_t *res);
 int iso14443a_select_card(uint8_t *uid_ptr, iso14a_card_select_t *p_card, uint32_t *cuid_ptr, bool anticollision, uint8_t num_cascades, bool no_rats);
-int iso14443a_select_cardEx(uint8_t *uid_ptr, iso14a_card_select_t *p_card, uint32_t *cuid_ptr, bool anticollision, uint8_t num_cascades, bool no_rats, bool use_ecp);
+int iso14443a_select_cardEx(uint8_t *uid_ptr, iso14a_card_select_t *p_card, uint32_t *cuid_ptr, bool anticollision, uint8_t num_cascades, bool no_rats, bool use_ecp, bool use_magsafe);
 int iso14443a_fast_select_card(uint8_t *uid_ptr, uint8_t num_cascades);
 void iso14a_set_trigger(bool enable);
 
diff --git a/client/lualibs/read14a.lua b/client/lualibs/read14a.lua
index beaea0fe5..c4ef3caa7 100644
--- a/client/lualibs/read14a.lua
+++ b/client/lualibs/read14a.lua
@@ -29,7 +29,8 @@ local ISO14A_COMMAND = {
     ISO14A_TOPAZMODE = 0x100,
     ISO14A_NO_RATS = 0x200,
     ISO14A_SEND_CHAINING = 0x400,
-    ISO14A_USE_ECP = 0x800
+    ISO14A_USE_ECP = 0x800,
+    ISO14A_USE_MAGSAFE = 0x1000,
 }
 
 local ISO14443a_TYPES = {}
diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index 213ac6753..9417d7337 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -417,7 +417,11 @@ static int CmdHF14AReader(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf 14a reader",
                   "Act as a ISO-14443a reader to identify tag. Look for ISO-14443a tags until Enter or the pm3 button is pressed",
-                  "hf 14a reader -@   -> Continuous mode");
+                  "hf 14a reader\n"
+                  "hf 14a reader -@     -> Continuous mode\n"
+                  "hf 14a reader --ecp  -> trigger apple enhanced contactless polling\n"
+                  "hf 14a reader --mag  -> trigger apple magsafe polling\n"
+                  );
 
     void *argtable[] = {
         arg_param_begin,
@@ -426,6 +430,7 @@ static int CmdHF14AReader(const char *Cmd) {
         arg_lit0(NULL, "drop", "just drop the signal field"),
         arg_lit0(NULL, "skip", "ISO14443-3 select only (skip RATS)"),
         arg_lit0(NULL, "ecp", "Use enhanced contactless polling"),
+        arg_lit0(NULL, "mag", "Use Apple magsafe polling"),
         arg_lit0("@", NULL, "continuous reader mode"),
         arg_param_end
     };
@@ -451,7 +456,11 @@ static int CmdHF14AReader(const char *Cmd) {
         cm |= ISO14A_USE_ECP;
     }
 
-    bool continuous = arg_get_lit(ctx, 6);
+    if (arg_get_lit(ctx, 6)) {
+        cm |= ISO14A_USE_MAGSAFE;
+    }
+
+    bool continuous = arg_get_lit(ctx, 7);
 
     CLIParserFree(ctx);
 
@@ -1219,7 +1228,8 @@ static int CmdHF14ACmdRaw(const char *Cmd) {
         arg_int0("t",  "timeout", "<ms>", "timeout in milliseconds"),
         arg_lit0("v",  "verbose", "Verbose output"),
         arg_lit0(NULL, "topaz", "use Topaz protocol to send command"),
-        arg_lit0(NULL, "ecp", "Use enhanced contactless polling"),        
+        arg_lit0(NULL, "ecp", "Use enhanced contactless polling"),
+        arg_lit0(NULL, "mag", "Use Apple magsafe polling"),
         arg_strx1(NULL, NULL, "<hex>", "raw bytes to send"),
         arg_param_end
     };
@@ -1236,10 +1246,11 @@ static int CmdHF14ACmdRaw(const char *Cmd) {
     bool verbose = arg_get_lit(ctx, 9);
     bool topazmode = arg_get_lit(ctx, 10);
     bool use_ecp = arg_get_lit(ctx, 11);
+    bool use_magsafe = arg_get_lit(ctx, 12);
 
     int datalen = 0;
     uint8_t data[PM3_CMD_DATA_SIZE];
-    CLIGetHexWithReturn(ctx, 12, data, &datalen);
+    CLIGetHexWithReturn(ctx, 13, data, &datalen);
     CLIParserFree(ctx);
 
     bool bTimeout = (timeout) ? true : false;
@@ -1292,13 +1303,19 @@ static int CmdHF14ACmdRaw(const char *Cmd) {
     if (topazmode) {
         flags |= ISO14A_TOPAZMODE;
     }
+
     if (no_rats) {
         flags |= ISO14A_NO_RATS;
     }
+
     if (use_ecp){
         flags |= ISO14A_USE_ECP;
     }
 
+    if (use_magsafe){
+        flags |= ISO14A_USE_MAGSAFE;
+    }    
+
     // Max buffer is PM3_CMD_DATA_SIZE
     datalen = (datalen > PM3_CMD_DATA_SIZE) ? PM3_CMD_DATA_SIZE : datalen;
 
diff --git a/client/src/cmdhflist.c b/client/src/cmdhflist.c
index 4380b5500..cc07666d8 100644
--- a/client/src/cmdhflist.c
+++ b/client/src/cmdhflist.c
@@ -181,6 +181,9 @@ int applyIso14443a(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize, bool i
 
         gs_ntag_i2c_state = 0;
         switch (cmd[0]) {
+            case MAGSAFE_CMD_WUPA:
+                snprintf(exp, size, "MAGSAFE WUPA");
+                break;
             case ISO14443A_CMD_WUPA:
                 snprintf(exp, size, "WUPA");
                 break;
diff --git a/include/mifare.h b/include/mifare.h
index 0a0156ef7..3500c8b8f 100644
--- a/include/mifare.h
+++ b/include/mifare.h
@@ -66,7 +66,8 @@ typedef enum ISO14A_COMMAND {
     ISO14A_TOPAZMODE = (1 << 8),
     ISO14A_NO_RATS = (1 << 9),
     ISO14A_SEND_CHAINING = (1 << 10),
-    ISO14A_USE_ECP = (1 << 11)
+    ISO14A_USE_ECP = (1 << 11),
+    ISO14A_USE_MAGSAFE = (1 << 12)
 } iso14a_command_t;
 
 typedef struct {
diff --git a/include/protocols.h b/include/protocols.h
index 41cbd989d..55d0874d7 100644
--- a/include/protocols.h
+++ b/include/protocols.h
@@ -143,6 +143,9 @@ ISO 7816-4 Basic interindustry commands. For command APDU's.
 #define ICLASS_DEBIT(x)             (((x) & 0x80) == 0x80)
 
 
+// U7bit 
+#define MAGSAFE_CMD_WUPA            0x7A
+
 #define ISO14443A_CMD_REQA          0x26
 #define ISO14443A_CMD_READBLOCK     0x30
 #define ISO14443A_CMD_WUPA          0x52
