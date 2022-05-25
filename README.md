commit 27dcbb49ec8b3a8b0937aa8044ec6e26130597e4
Author: iceman1001 <iceman@iuse.se>
Date:   Wed May 19 12:18:04 2021 +0200

    hf 14a raw - now has a verbose parameter and less output normally

diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index f79396d96..22022d3c6 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -33,7 +33,7 @@
 bool APDUInFramingEnable = true;
 
 static int CmdHelp(const char *Cmd);
-static int waitCmd(bool i_select, uint32_t timeout);
+static int waitCmd(bool i_select, uint32_t timeout, bool verbose);
 
 static const manufactureName manufactureMapping[] = {
     // ID,  "Vendor Country"
@@ -1187,7 +1187,6 @@ static int CmdHF14AAPDU(const char *Cmd) {
 }
 
 static int CmdHF14ACmdRaw(const char *Cmd) {
-
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf 14a raw",
                   "Sends raw bytes over ISO14443a. With option to use TOPAZ 14a mode.",
@@ -1205,13 +1204,13 @@ static int CmdHF14ACmdRaw(const char *Cmd) {
         arg_lit0("r",  NULL, "do not read response"),
         arg_lit0("s",  NULL, "active signal field ON with select"),
         arg_int0("t",  "timeout", "<ms>", "timeout in milliseconds"),
+        arg_lit0("v",  "verbose", "Verbose output"),
         arg_lit0(NULL, "topaz", "use Topaz protocol to send command"),
         arg_strx1(NULL, NULL, "<hex>", "raw bytes to send"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
 
-
     bool active = arg_get_lit(ctx, 1);
     uint16_t numbits = (uint16_t)arg_get_int_def(ctx, 2, 0);
     bool crc = arg_get_lit(ctx, 3);
@@ -1220,11 +1219,12 @@ static int CmdHF14ACmdRaw(const char *Cmd) {
     bool reply = (arg_get_lit(ctx, 6) == false);
     bool active_select = arg_get_lit(ctx, 7);
     uint32_t timeout = (uint32_t)arg_get_int_def(ctx, 8, 0);
-    bool topazmode = arg_get_lit(ctx, 9);
+    bool verbose = arg_get_lit(ctx, 9);
+    bool topazmode = arg_get_lit(ctx, 10);
 
     int datalen = 0;
     uint8_t data[PM3_CMD_DATA_SIZE];
-    CLIGetHexWithReturn(ctx, 10, data, &datalen);
+    CLIGetHexWithReturn(ctx, 11, data, &datalen);
     CLIParserFree(ctx);
 
     bool bTimeout = (timeout) ? true : false;
@@ -1290,14 +1290,14 @@ static int CmdHF14ACmdRaw(const char *Cmd) {
     if (reply) {
         int res = 0;
         if (active_select)
-            res = waitCmd(true, timeout);
+            res = waitCmd(true, timeout, verbose);
         if (res == PM3_SUCCESS && datalen > 0)
-            waitCmd(false, timeout);
+            waitCmd(false, timeout, verbose);
     }
     return PM3_SUCCESS;
 }
 
-static int waitCmd(bool i_select, uint32_t timeout) {
+static int waitCmd(bool i_select, uint32_t timeout, bool verbose) {
     PacketResponseNG resp;
 
     if (WaitForResponseTimeout(CMD_ACK, &resp, timeout + 1500)) {
@@ -1305,16 +1305,24 @@ static int waitCmd(bool i_select, uint32_t timeout) {
         if (i_select) {
             len = (resp.oldarg[1] & 0xFFFF);
             if (len) {
-                PrintAndLogEx(SUCCESS, "Card selected. UID[%u]:", len);
+                if (verbose) {
+                    PrintAndLogEx(SUCCESS, "Card selected. UID[%u]:", len);
+                } else {
+                    return PM3_SUCCESS;
+                }
+
             } else {
                 PrintAndLogEx(WARNING, "Can't select card.");
             }
         } else {
-            PrintAndLogEx(SUCCESS, "received " _YELLOW_("%u") " bytes", len);
+            if (verbose) {
+                PrintAndLogEx(SUCCESS, "received " _YELLOW_("%u") " bytes", len);
+            }
         }
 
-        if (!len)
+        if (len == 0) {
             return PM3_ESOFT;
+        }
 
         uint8_t *data = resp.data.asBytes;
 
