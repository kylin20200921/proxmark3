commit 63bc9b5eb25dea6d1c2add122c2587ff68d78d0c
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Feb 14 09:19:35 2022 +0100

    added legic view command,  and converted OLD -> NG comms

diff --git a/CHANGELOG.md b/CHANGELOG.md
index 2f76c3c16..a339b528e 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,8 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Changed `hf legic *` - now uses NG instead (@iceman1001)
+ - Added `hf legic view` - view contents of LEGIC Prime dump files (@iceman1001)
  - Changed `hf mfu restore` - now takes bin/json as dump files (@iceman1001)
  - Added `hf mfu view` - view contents of MFU dump files (@iceman1001)
  - Changed `hf_mf_uidbruteforce` - added support for S70, enhance UID length management (@cactuschibre)
diff --git a/armsrc/appmain.c b/armsrc/appmain.c
index 22871a00d..c6e188ec0 100644
--- a/armsrc/appmain.c
+++ b/armsrc/appmain.c
@@ -1285,11 +1285,13 @@ static void PacketReceived(PacketCommandNG *packet) {
             break;
         }
         case CMD_HF_LEGIC_WRITER: {
-            LegicRfWriter(packet->oldarg[0], packet->oldarg[1], packet->oldarg[2], packet->data.asBytes);
+            legic_packet_t *payload = (legic_packet_t*) packet->data.asBytes;
+            LegicRfWriter(payload->offset, payload->len, payload->iv, payload->data);
             break;
         }
         case CMD_HF_LEGIC_READER: {
-            LegicRfReader(packet->oldarg[0], packet->oldarg[1], packet->oldarg[2]);
+            legic_packet_t *payload = (legic_packet_t*) packet->data.asBytes;
+            LegicRfReader(payload->offset, payload->len, payload->iv);
             break;
         }
         case CMD_HF_LEGIC_INFO: {
@@ -1302,10 +1304,9 @@ static void PacketReceived(PacketCommandNG *packet) {
             // involved in dealing with emulator memory. But if it is called later, it might
             // destroy the Emulator Memory.
             //-----------------------------------------------------------------------------
-            // arg0 = offset
-            // arg1 = num of bytes
             FpgaDownloadAndGo(FPGA_BITSTREAM_HF);
-            emlSet(packet->data.asBytes, packet->oldarg[0], packet->oldarg[1]);
+            legic_packet_t *payload = (legic_packet_t*) packet->data.asBytes;
+            emlSet(payload->data, payload->offset, payload->len);
             break;
         }
 #endif
diff --git a/armsrc/legicrf.c b/armsrc/legicrf.c
index 840fd74f9..31f9691fc 100644
--- a/armsrc/legicrf.c
+++ b/armsrc/legicrf.c
@@ -422,7 +422,7 @@ void LegicRfInfo(void) {
     // establish shared secret and detect card type
     uint8_t card_type = setup_phase(0x01);
     if (init_card(card_type, &card) != PM3_SUCCESS) {
-        reply_mix(CMD_ACK, 0, 0, 0, 0, 0);
+        reply_ng(CMD_HF_LEGIC_INFO, PM3_EINIT, NULL, 0);
         goto OUT;
     }
 
@@ -430,7 +430,7 @@ void LegicRfInfo(void) {
     for (uint8_t i = 0; i < sizeof(card.uid); ++i) {
         int16_t byte = read_byte(i, card.cmdsize);
         if (byte == -1) {
-            reply_mix(CMD_ACK, 0, 0, 0, 0, 0);
+            reply_ng(CMD_HF_LEGIC_INFO, PM3_EFAILED, NULL, 0);
             goto OUT;
         }
         card.uid[i] = byte & 0xFF;
@@ -440,12 +440,12 @@ void LegicRfInfo(void) {
     int16_t mcc = read_byte(4, card.cmdsize);
     int16_t calc_mcc = CRC8Legic(card.uid, 4);
     if (mcc != calc_mcc) {
-        reply_mix(CMD_ACK, 0, 0, 0, 0, 0);
+        reply_ng(CMD_HF_LEGIC_INFO, PM3_ESOFT, NULL, 0);
         goto OUT;
     }
 
     // OK
-    reply_mix(CMD_ACK, 1, 0, 0, (uint8_t *)&card, sizeof(legic_card_select_t));
+    reply_ng(CMD_HF_LEGIC_INFO, PM3_SUCCESS, (uint8_t *)&card, sizeof(legic_card_select_t));
 
 OUT:
     switch_off();
@@ -497,7 +497,7 @@ void LegicRfReader(uint16_t offset, uint16_t len, uint8_t iv) {
     // establish shared secret and detect card type
     uint8_t card_type = setup_phase(iv);
     if (init_card(card_type, &card) != PM3_SUCCESS) {
-        reply_mix(CMD_ACK, 0, 0, 0, 0, 0);
+        reply_ng(CMD_HF_LEGIC_READER, PM3_EINIT, NULL, 0);
         goto OUT;
     }
 
@@ -509,7 +509,7 @@ void LegicRfReader(uint16_t offset, uint16_t len, uint8_t iv) {
     for (uint16_t i = 0; i < len; ++i) {
         int16_t byte = read_byte(offset + i, card.cmdsize);
         if (byte == -1) {
-            reply_mix(CMD_ACK, 0, 0, 0, 0, 0);
+            reply_ng(CMD_HF_LEGIC_READER, PM3_EFAILED, NULL, 0);
             goto OUT;
         }
         legic_mem[i] = byte;
@@ -520,7 +520,7 @@ void LegicRfReader(uint16_t offset, uint16_t len, uint8_t iv) {
     }
 
     // OK
-    reply_mix(CMD_ACK, 1, len, 0, 0, 0);
+    reply_ng(CMD_HF_LEGIC_READER, PM3_SUCCESS, (uint8_t*)&len, sizeof(len));
 
 OUT:
     switch_off();
@@ -533,14 +533,14 @@ void LegicRfWriter(uint16_t offset, uint16_t len, uint8_t iv, uint8_t *data) {
 
     // uid is not writeable
     if (offset <= WRITE_LOWERLIMIT) {
-        reply_mix(CMD_ACK, 0, 0, 0, 0, 0);
+        reply_ng(CMD_HF_LEGIC_WRITER, PM3_EINVARG, NULL, 0);
         goto OUT;
     }
 
     // establish shared secret and detect card type
     uint8_t card_type = setup_phase(iv);
     if (init_card(card_type, &card) != PM3_SUCCESS) {
-        reply_mix(CMD_ACK, 0, 0, 0, 0, 0);
+        reply_ng(CMD_HF_LEGIC_WRITER, PM3_EINIT, NULL, 0);
         goto OUT;
     }
 
@@ -553,13 +553,13 @@ void LegicRfWriter(uint16_t offset, uint16_t len, uint8_t iv, uint8_t *data) {
     while (len-- > 0 && BUTTON_PRESS() == false) {
         if (write_byte(len + offset, data[len], card.addrsize) == false) {
             Dbprintf("operation failed | %02X | %02X | %02X", len + offset, len, data[len]);
-            reply_mix(CMD_ACK, 0, 0, 0, 0, 0);
+            reply_ng(CMD_HF_LEGIC_WRITER, PM3_EFAILED, NULL, 0);
             goto OUT;
         }
     }
 
     // OK
-    reply_mix(CMD_ACK, 1, len, 0, 0, 0);
+    reply_ng(CMD_HF_LEGIC_WRITER, PM3_SUCCESS, (uint8_t*)&len, sizeof(len));
 
 OUT:
     switch_off();
diff --git a/client/luascripts/hf_legic_clone.lua b/client/luascripts/hf_legic_clone.lua
index ae777dd7e..d41046d65 100644
--- a/client/luascripts/hf_legic_clone.lua
+++ b/client/luascripts/hf_legic_clone.lua
@@ -170,14 +170,9 @@ end
 -- read LEGIC data
 local function readlegicdata(offset, length, iv)
     -- Read data
-    local command = Command:newMIX{
-                    cmd = cmds.CMD_HF_LEGIC_READER
-                    , arg1 = offset
-                    , arg2 = length
-                    , arg3 = iv
-                    , data = nil
-                    }
-    local result, err = command:sendMIX()
+    local d0 = ('%04X%04X%02X'):format(offset, len, iv)
+    local c = Command:newNG{cmd = cmds.CMD_HF_LEGIC_READER, data = d0}
+    local result, err = c:sendNG()
     if not result then return oops(err) end
     -- result is a packed data structure, data starts at offset 33
     return result
diff --git a/client/src/cmdhflegic.c b/client/src/cmdhflegic.c
index f00e58407..e791c4ecc 100644
--- a/client/src/cmdhflegic.c
+++ b/client/src/cmdhflegic.c
@@ -30,7 +30,7 @@
 
 static int CmdHelp(const char *Cmd);
 
-#define MAX_LENGTH 1024
+#define LEGIC_PRIME_MAX_LENGTH 1024
 
 static bool legic_xor(uint8_t *data, uint16_t cardsize) {
 
@@ -393,33 +393,30 @@ static int CmdLegicRdbl(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf legic rdbl",
                   "Read data from a LEGIC Prime tag",
-                  "hf legic rdbl -o 0 -l 16           -> reads from byte[0] 16 bytes(system header)\n"
-                  "hf legic rdbl -o 0 -l 4 --iv 55    -> reads from byte[0] 4 bytes with IV 0x55\n"
-                  "hf legic rdbl -o 0 -l 256 --iv 55  -> reads from byte[0] 256 bytes with IV 0x55");
+                  "hf legic rdbl -o 0 -l 16           -> read 16 bytes from offset 0 (system header)\n"
+                  "hf legic rdbl -o 0 -l 4 --iv 55    -> read 4 bytes from offset 0\n"
+                  "hf legic rdbl -o 0 -l 256 --iv 55  -> read 256 bytes from offset 0");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_int1("o", "offset", "<dec>", "offset in data array to start download from"),
-        arg_int1("l", "length", "<dec>", "number of bytes to read"),
+        arg_int0("o", "offset", "<dec>", "offset in data array to start download from"),
+        arg_int0("l", "length", "<dec>", "number of bytes to read"),
         arg_str0(NULL, "iv", "<hex>", "Initialization vector to use. Must be odd and 7bits max"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
 
     int offset = arg_get_int_def(ctx, 1, 0);
-
-    int len = arg_get_int_def(ctx, 2, 0);
+    int len = arg_get_int_def(ctx, 2, 16);
 
     int iv_len = 0;
-    uint8_t iv[1] = {0x01};  // formerly uidcrc
-
+    uint8_t iv[1] = {0x01};
     CLIGetHexWithReturn(ctx, 3, iv, &iv_len);
-
     CLIParserFree(ctx);
 
     // sanity checks
-    if (len + offset >= MAX_LENGTH) {
-        PrintAndLogEx(WARNING, "Out-of-bounds, Cardsize = %d, [offset+len = %d ]", MAX_LENGTH, len + offset);
+    if (len + offset >= LEGIC_PRIME_MAX_LENGTH) {
+        PrintAndLogEx(WARNING, "Out-of-bounds, Cardsize = %d, [offset+len = %d ]", LEGIC_PRIME_MAX_LENGTH, len + offset);
         return PM3_EOUTOFBOUND;
     }
 
@@ -435,9 +432,9 @@ static int CmdLegicRdbl(const char *Cmd) {
     uint16_t datalen = 0;
     int status = legic_read_mem(offset, len, iv[0], data, &datalen);
     if (status == PM3_SUCCESS) {
-        PrintAndLogEx(NORMAL, " ##  |  0  1  2  3  4  5  6  7  8  9  A  B  C  D  E  F 10 11 12 13 14 15 16 17 18 19 1A 1B 1C 1D 1E 1F");
-        PrintAndLogEx(NORMAL, "-----+------------------------------------------------------------------------------------------------");
-        print_hex_break(data, datalen, 32);
+        PrintAndLogEx(INFO, "## |  0  1  2  3  4  5  6  7  8  9  A  B  C  D  E  F | ascii");
+        PrintAndLogEx(INFO, "---+-------------------------------------------------+-----------------");
+        print_hex_break(data, datalen, 16);
     }
     free(data);
     return status;
@@ -513,15 +510,13 @@ static int CmdLegicWrbl(const char *Cmd) {
     int offset = arg_get_int_def(ctx, 1, 0);
 
     int dlen = 0;
-    uint8_t data[MAX_LENGTH] = {0};
+    uint8_t data[LEGIC_PRIME_MAX_LENGTH] = {0};
     CLIGetHexWithReturn(ctx, 2, data, &dlen);
 
     bool autoconfirm = arg_get_lit(ctx, 3);
 
     CLIParserFree(ctx);
 
-    uint32_t IV = 0x55;
-
     // OUT-OF-BOUNDS checks
     // UID 4+1 bytes can't be written to.
     if (offset < 5) {
@@ -558,16 +553,24 @@ static int CmdLegicWrbl(const char *Cmd) {
         }
     }
 
+    uint32_t IV = 0x55;
     legic_chk_iv(&IV);
 
-    PrintAndLogEx(SUCCESS, "Writing to tag");
+    PrintAndLogEx(SUCCESS, "Writing to tag to offset %i", offset);
+
+    legic_packet_t *payload = calloc(1, sizeof(legic_packet_t) + dlen);
+    payload->offset = (offset & 0xFFFF);
+    payload->iv = (IV & 0x7F);
+    payload->len = dlen;
+    memcpy(payload->data, data, dlen);
 
     PacketResponseNG resp;
     clearCommandBuffer();
-    SendCommandOLD(CMD_HF_LEGIC_WRITER, offset, dlen, IV, data, dlen);
+    SendCommandNG(CMD_HF_LEGIC_WRITER, (uint8_t*)payload, sizeof(legic_packet_t) + dlen);
+    free(payload);
 
     uint8_t timeout = 0;
-    while (!WaitForResponseTimeout(CMD_ACK, &resp, 2000)) {
+    while (WaitForResponseTimeout(CMD_HF_LEGIC_WRITER, &resp, 2000) == false) {
         ++timeout;
         PrintAndLogEx(NORMAL, "." NOLF);
         if (timeout > 7) {
@@ -576,9 +579,8 @@ static int CmdLegicWrbl(const char *Cmd) {
         }
     }
     PrintAndLogEx(NORMAL, "");
-
-    uint8_t isOK = resp.oldarg[0] & 0xFF;
-    if (!isOK) {
+    
+    if (resp.status != PM3_SUCCESS) {
         PrintAndLogEx(WARNING, "Failed writing tag");
         return PM3_ERFTRANS;
     }
@@ -633,12 +635,17 @@ int legic_read_mem(uint32_t offset, uint32_t len, uint32_t iv, uint8_t *out, uin
 
     legic_chk_iv(&iv);
 
+    legic_packet_t *payload = calloc(1, sizeof(legic_packet_t));
+    payload->offset = (offset & 0xFFFF);
+    payload->iv = iv;
+    payload->len = len;
+
     clearCommandBuffer();
-    SendCommandMIX(CMD_HF_LEGIC_READER, offset, len, iv, NULL, 0);
+    SendCommandNG(CMD_HF_LEGIC_READER, (uint8_t*)payload, sizeof(legic_packet_t));
     PacketResponseNG resp;
 
     uint8_t timeout = 0;
-    while (!WaitForResponseTimeout(CMD_ACK, &resp, 1000)) {
+    while (WaitForResponseTimeout(CMD_HF_LEGIC_READER, &resp, 1000) == false) {
         ++timeout;
         PrintAndLogEx(NORMAL,  "." NOLF);
         if (timeout > 14) {
@@ -648,9 +655,9 @@ int legic_read_mem(uint32_t offset, uint32_t len, uint32_t iv, uint8_t *out, uin
     }
     PrintAndLogEx(NORMAL, "");
 
-    uint8_t isOK = resp.oldarg[0] & 0xFF;
-    *outlen = resp.oldarg[1];
-    if (!isOK) {
+
+    *outlen = resp.data.asDwords[0];
+    if (resp.status != PM3_SUCCESS) {
         PrintAndLogEx(WARNING, "Failed reading tag");
         return PM3_ESOFT;
     }
@@ -659,7 +666,7 @@ int legic_read_mem(uint32_t offset, uint32_t len, uint32_t iv, uint8_t *out, uin
         PrintAndLogEx(WARNING, "Fail, only managed to read %u bytes", *outlen);
 
     // copy data from device
-    if (!GetFromDevice(BIG_BUF_EML, out, *outlen, 0, NULL, 0, NULL, 2500, false)) {
+    if (GetFromDevice(BIG_BUF_EML, out, *outlen, 0, NULL, 0, NULL, 2500, false) == false) {
         PrintAndLogEx(WARNING, "Fail, transfer from device time-out");
         return PM3_ETIMEOUT;
     }
@@ -683,21 +690,22 @@ int legic_print_type(uint32_t tagtype, uint8_t spaces) {
 }
 int legic_get_type(legic_card_select_t *card) {
 
-    if (card == NULL) return PM3_EINVARG;
+    if (card == NULL)
+        return PM3_EINVARG;
 
     clearCommandBuffer();
     SendCommandNG(CMD_HF_LEGIC_INFO, NULL, 0);
     PacketResponseNG resp;
-    if (!WaitForResponseTimeout(CMD_ACK, &resp, 1500))
+    if (WaitForResponseTimeout(CMD_HF_LEGIC_INFO, &resp, 1500) == false)
         return PM3_ETIMEOUT;
 
-    uint8_t isOK = resp.oldarg[0] & 0xFF;
-    if (!isOK)
+    if (resp.status != PM3_SUCCESS)
         return PM3_ESOFT;
 
     memcpy(card, resp.data.asBytes, sizeof(legic_card_select_t));
     return PM3_SUCCESS;
 }
+
 void legic_chk_iv(uint32_t *iv) {
     if ((*iv & 0x7F) != *iv) {
         *iv &= 0x7F;
@@ -709,20 +717,30 @@ void legic_chk_iv(uint32_t *iv) {
         PrintAndLogEx(INFO, "LSB of IV must be SET %u", *iv);
     }
 }
+
 void legic_seteml(uint8_t *src, uint32_t offset, uint32_t numofbytes) {
+
     // fast push mode
     g_conn.block_after_ACK = true;
-    for (size_t i = offset; i < numofbytes; i += PM3_CMD_DATA_SIZE) {
+    for (size_t i = offset; i < numofbytes; i += (PM3_CMD_DATA_SIZE - sizeof(legic_packet_t))) {
 
-        size_t len = MIN((numofbytes - i), PM3_CMD_DATA_SIZE);
+        size_t len = MIN((numofbytes - i), (PM3_CMD_DATA_SIZE - sizeof(legic_packet_t)));
         if (len == numofbytes - i) {
             // Disable fast mode on last packet
             g_conn.block_after_ACK = false;
         }
+
+        legic_packet_t *payload = calloc(1, sizeof(legic_packet_t) + len);
+        payload->offset = i;
+        payload->len = len;
+        memcpy(payload->data, src + i, len);
+
         clearCommandBuffer();
-        SendCommandOLD(CMD_HF_LEGIC_ESET, i, len, 0, src + i, len);
+        SendCommandNG(CMD_HF_LEGIC_ESET, (uint8_t*)payload, sizeof(legic_packet_t) + len);
+        free(payload);
     }
 }
+
 static int CmdLegicReader(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf legic reader",
@@ -780,12 +798,17 @@ static int CmdLegicDump(const char *Cmd) {
     legic_print_type(dumplen, 0);
     PrintAndLogEx(SUCCESS, "Reading tag memory %d b...", dumplen);
 
+    legic_packet_t *payload = calloc(1, sizeof(legic_packet_t));
+    payload->offset = 0;
+    payload->iv = 0x55;
+    payload->len = dumplen;
+
     clearCommandBuffer();
-    SendCommandMIX(CMD_HF_LEGIC_READER, 0x00, dumplen, 0x55, NULL, 0);
+    SendCommandNG(CMD_HF_LEGIC_READER, (uint8_t*)payload, sizeof(legic_packet_t));
     PacketResponseNG resp;
 
     uint8_t timeout = 0;
-    while (!WaitForResponseTimeout(CMD_ACK, &resp, 2000)) {
+    while (WaitForResponseTimeout(CMD_HF_LEGIC_READER, &resp, 2000) == false) {
         ++timeout;
         PrintAndLogEx(NORMAL, "." NOLF);
         if (timeout > 7) {
@@ -795,13 +818,12 @@ static int CmdLegicDump(const char *Cmd) {
     }
     PrintAndLogEx(NORMAL, "");
 
-    uint8_t isOK = resp.oldarg[0] & 0xFF;
-    if (!isOK) {
+    if (resp.status != PM3_SUCCESS) {
         PrintAndLogEx(WARNING, "Failed dumping tag data");
         return PM3_ERFTRANS;
     }
 
-    uint16_t readlen = resp.oldarg[1];
+    uint16_t readlen = resp.data.asDwords[0];
     uint8_t *data = calloc(readlen, sizeof(uint8_t));
     if (!data) {
         PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
@@ -812,7 +834,7 @@ static int CmdLegicDump(const char *Cmd) {
         PrintAndLogEx(WARNING, "Fail, only managed to read 0x%02X bytes of 0x%02X", readlen, dumplen);
 
     // copy data from device
-    if (!GetFromDevice(BIG_BUF_EML, data, readlen, 0, NULL, 0, NULL, 2500, false)) {
+    if (GetFromDevice(BIG_BUF_EML, data, readlen, 0, NULL, 0, NULL, 2500, false) == false) {
         PrintAndLogEx(WARNING, "Fail, transfer from device time-out");
         free(data);
         return PM3_ETIMEOUT;
@@ -918,11 +940,19 @@ static int CmdLegicRestore(const char *Cmd) {
             // Disable fast mode on last packet
             g_conn.block_after_ACK = false;
         }
+
+        legic_packet_t *payload = calloc(1, sizeof(legic_packet_t) + len);
+        payload->offset = i;
+        payload->iv = 0x55;
+        payload->len = len;
+        memcpy(payload->data, data + i, len);
+
         clearCommandBuffer();
-        SendCommandOLD(CMD_HF_LEGIC_WRITER, i, len, 0x55, data + i, len);
+        SendCommandNG(CMD_HF_LEGIC_WRITER, (uint8_t*)payload, sizeof(legic_packet_t) + len);
+        free(payload);
 
         uint8_t timeout = 0;
-        while (!WaitForResponseTimeout(CMD_ACK, &resp, 2000)) {
+        while (WaitForResponseTimeout(CMD_HF_LEGIC_WRITER, &resp, 2000) == false) {
             ++timeout;
             PrintAndLogEx(NORMAL, "." NOLF);
             if (timeout > 7) {
@@ -933,8 +963,7 @@ static int CmdLegicRestore(const char *Cmd) {
         }
         PrintAndLogEx(NORMAL, "");
 
-        uint8_t isOK = resp.oldarg[0] & 0xFF;
-        if (!isOK) {
+        if (resp.status != PM3_SUCCESS) {
             PrintAndLogEx(WARNING, "Failed writing tag [msg = %u]", (uint8_t)(resp.oldarg[1] & 0xFF));
             free(data);
             return PM3_ERFTRANS;
@@ -952,7 +981,7 @@ static int CmdLegicELoad(const char *Cmd) {
     CLIParserInit(&ctx, "hf legic eload",
                   "Loads a LEGIC binary dump into emulator memory",
                   "hf legic eload -f myfile -t 0  -> Simulate Type MIM22\n"
-                  "hf legic eload -f myfile -t 1  -> Simulate Type MIM256 (default)\n"
+                  "hf legic eload -f myfile -t 1  -> Simulate Type MIM256 (def)\n"
                   "hf legic eload -f myfile -t 2  -> Simulate Type MIM1024");
 
     void *argtable[] = {
@@ -1021,7 +1050,7 @@ static int CmdLegicESave(const char *Cmd) {
                   "Saves bin/eml/json dump file of emulator memory",
                   "hf legic esave                  --> uses UID as filename\n"
                   "hf legic esave -f myfile -t 0   --> Type MIM22\n"
-                  "hf legic esave -f myfile -t 1   --> Type MIM256 (default)\n"
+                  "hf legic esave -f myfile -t 1   --> Type MIM256 (def)\n"
                   "hf legic esave -f myfile -t 2   --> Type MIM1024");
 
     void *argtable[] = {
@@ -1061,14 +1090,14 @@ static int CmdLegicESave(const char *Cmd) {
 
     // set up buffer
     uint8_t *data = calloc(numofbytes, sizeof(uint8_t));
-    if (!data) {
+    if (data == NULL) {
         PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
         return PM3_EMALLOC;
     }
 
     // download emulator memory
     PrintAndLogEx(SUCCESS, "Reading emulator memory...");
-    if (!GetFromDevice(BIG_BUF_EML, data, numofbytes, 0, NULL, 0, NULL, 2500, false)) {
+    if (GetFromDevice(BIG_BUF_EML, data, numofbytes, 0, NULL, 0, NULL, 2500, false) == false) {
         PrintAndLogEx(WARNING, "Fail, transfer from device time-out");
         free(data);
         return PM3_ETIMEOUT;
@@ -1091,6 +1120,66 @@ static int CmdLegicESave(const char *Cmd) {
     return PM3_SUCCESS;
 }
 
+static int CmdLegicEView(const char *Cmd) {
+
+    CLIParserContext *ctx;
+    CLIParserInit(&ctx, "hf legic eview",
+                  "It displays emulator memory",
+                  "hf legic eview\n"
+                  "hf legic eview -t 0  -> MIM22\n"
+                  "hf legic eview -t 1  -> MIM256 (def)\n"
+                  "hf legic eview -t 2  -> MIM1024"
+                 );
+    void *argtable[] = {
+        arg_param_begin,
+        arg_int0("t", "type", "<dec>", "Tag type to simulate."),        
+        arg_param_end
+    };
+    CLIExecWithReturn(ctx, Cmd, argtable, true);
+
+    size_t bytes = 0;
+
+    switch (arg_get_int_def(ctx, 1, 1)) {
+        case 0:
+            bytes = 22;
+            break;
+        case 1:
+            bytes = 256;
+            break;
+        case 2:
+            bytes = LEGIC_PRIME_MAX_LENGTH;
+            break;
+        default:
+            PrintAndLogEx(ERR, "Unknown tag type");
+            CLIParserFree(ctx);
+            return PM3_EINVARG;
+    }
+
+    CLIParserFree(ctx);
+
+    // validations
+
+    uint8_t *dump = calloc(bytes, sizeof(uint8_t));
+    if (dump == NULL) {
+        PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
+        return PM3_EMALLOC;
+    }
+
+    PrintAndLogEx(INFO, "downloading emulator memory");
+    if (GetFromDevice(BIG_BUF_EML, dump, bytes, 0, NULL, 0, NULL, 2500, false) == false) {
+        PrintAndLogEx(WARNING, "Fail, transfer from device time-out");
+        free(dump);
+        return PM3_ETIMEOUT;
+    }
+
+    PrintAndLogEx(NORMAL, "");
+    PrintAndLogEx(INFO, "## |  0  1  2  3  4  5  6  7  8  9  A  B  C  D  E  F | ascii");
+    PrintAndLogEx(INFO, "---+-------------------------------------------------+-----------------");
+    print_hex_break(dump, bytes, 16);
+    free(dump);
+    return PM3_SUCCESS;
+}
+
 static int CmdLegicWipe(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf legic wipe",
@@ -1136,11 +1225,19 @@ static int CmdLegicWipe(const char *Cmd) {
             // Disable fast mode on last packet
             g_conn.block_after_ACK = false;
         }
+
+        legic_packet_t *payload = calloc(1, sizeof(legic_packet_t) + len);
+        payload->offset = i;
+        payload->iv = 0x55;
+        payload->len = len;
+        memcpy(payload->data, data + i, len);
+
         clearCommandBuffer();
-        SendCommandOLD(CMD_HF_LEGIC_WRITER, i, len, 0x55, data + i, len);
+        SendCommandNG(CMD_HF_LEGIC_WRITER, (uint8_t*)payload, sizeof(legic_packet_t) + len);
+        free(payload);
 
         uint8_t timeout = 0;
-        while (!WaitForResponseTimeout(CMD_ACK, &resp, 2000)) {
+        while (WaitForResponseTimeout(CMD_HF_LEGIC_WRITER, &resp, 2000) == false) {
             ++timeout;
             PrintAndLogEx(NORMAL, "." NOLF);
             if (timeout > 7) {
@@ -1151,14 +1248,13 @@ static int CmdLegicWipe(const char *Cmd) {
         }
         PrintAndLogEx(NORMAL, "");
 
-        uint8_t isOK = resp.oldarg[0] & 0xFF;
-        if (!isOK) {
-            PrintAndLogEx(WARNING, "Failed writing tag [msg = %u]", (uint8_t)(resp.oldarg[1] & 0xFF));
+        if (resp.status != PM3_SUCCESS) {
+            PrintAndLogEx(WARNING, "failed writing tag");
             free(data);
             return PM3_ERFTRANS;
         }
     }
-    PrintAndLogEx(SUCCESS, "ok\n");
+    PrintAndLogEx(SUCCESS, "Done!\n");
     free(data);
     return PM3_SUCCESS;
 }
@@ -1167,20 +1263,85 @@ static int CmdLegicList(const char *Cmd) {
     return CmdTraceListAlias(Cmd, "hf legic", "legic");
 }
 
+static int CmdLegicView(const char *Cmd) {
+    CLIParserContext *ctx;
+    CLIParserInit(&ctx, "hf legic view",
+                  "Print a LEGIC Prime dump file (bin/eml/json)",
+                  "hf legic view -f hf-legic-01020304-dump.bin"
+                 );
+    void *argtable[] = {
+        arg_param_begin,
+        arg_str1("f", "file", "<fn>", "Filename of dump"),
+        arg_param_end
+    };
+    CLIExecWithReturn(ctx, Cmd, argtable, false);
+    int fnlen = 0;
+    char filename[FILE_PATH_SIZE];
+    CLIParamStrToBuf(arg_get_str(ctx, 1), (uint8_t *)filename, FILE_PATH_SIZE, &fnlen);
+    CLIParserFree(ctx);
+
+    // reserve memory
+    uint8_t *dump = NULL;
+    size_t bytes_read = 0;
+    int res = 0;
+    DumpFileType_t dftype = getfiletype(filename);
+    switch (dftype) {
+        case BIN: {
+            res = loadFile_safe(filename, ".bin", (void **)&dump, &bytes_read);
+            break;
+        }
+        case JSON: {
+            dump = calloc(LEGIC_PRIME_MAX_LENGTH, sizeof(uint8_t));
+            if (dump == NULL) {
+                PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
+                return PM3_EMALLOC;
+            }
+            res = loadFileJSON(filename, (void *)dump, LEGIC_PRIME_MAX_LENGTH, &bytes_read, NULL);
+            break;
+        }
+        case EML:
+            res = loadFileEML_safe(filename, (void **)&dump, &bytes_read);
+            break;
+        case DICTIONARY: {
+            PrintAndLogEx(ERR, "Error: Only BIN/EML/JSON formats allowed");
+            free(dump);
+            return PM3_EINVARG;
+        }
+    }
+
+    if (res != PM3_SUCCESS) {
+        PrintAndLogEx(FAILED, "File: " _YELLOW_("%s") ": not found or locked.", filename);
+        free(dump);
+        return PM3_EFILE;
+    }
+
+    PrintAndLogEx(NORMAL, "");
+    PrintAndLogEx(INFO, "## |  0  1  2  3  4  5  6  7  8  9  A  B  C  D  E  F | ascii");
+    PrintAndLogEx(INFO, "---+-------------------------------------------------+-----------------");
+    print_hex_break(dump, bytes_read, 16);
+    free(dump);
+    return PM3_SUCCESS;    
+}
+
 static command_t CommandTable[] =  {
+    {"-----------", CmdHelp,      AlwaysAvailable, "--------------------- " _CYAN_("operations") " ---------------------"},    
     {"help",    CmdHelp,          AlwaysAvailable, "This help"},
-    {"list",    CmdLegicList,     AlwaysAvailable,    "List LEGIC history"},
-    {"reader",  CmdLegicReader,   IfPm3Legicrf,    "LEGIC Prime Reader UID and tag info"},
-    {"info",    CmdLegicInfo,     IfPm3Legicrf,    "Display deobfuscated and decoded LEGIC Prime tag data"},
     {"dump",    CmdLegicDump,     IfPm3Legicrf,    "Dump LEGIC Prime tag to binary file"},
-    {"restore", CmdLegicRestore,  IfPm3Legicrf,    "Restore a dump file onto a LEGIC Prime tag"},
+    {"info",    CmdLegicInfo,     IfPm3Legicrf,    "Display deobfuscated and decoded LEGIC Prime tag data"},
+    {"list",    CmdLegicList,     AlwaysAvailable, "List LEGIC history"},
     {"rdbl",    CmdLegicRdbl,     IfPm3Legicrf,    "Read bytes from a LEGIC Prime tag"},
-    {"sim",     CmdLegicSim,      IfPm3Legicrf,    "Start tag simulator"},
+    {"reader",  CmdLegicReader,   IfPm3Legicrf,    "LEGIC Prime Reader UID and tag info"},
+    {"restore", CmdLegicRestore,  IfPm3Legicrf,    "Restore a dump file onto a LEGIC Prime tag"},
+    {"wipe",    CmdLegicWipe,     IfPm3Legicrf,    "Wipe a LEGIC Prime tag"},
     {"wrbl",    CmdLegicWrbl,     IfPm3Legicrf,    "Write data to a LEGIC Prime tag"},
+    {"-----------", CmdHelp,      AlwaysAvailable, "--------------------- " _CYAN_("simulation") " ---------------------"},
+    {"sim",     CmdLegicSim,      IfPm3Legicrf,    "Start tag simulator"},
+    {"eload",   CmdLegicELoad,    IfPm3Legicrf,    "Load binary dump to emulator memory"},
+    {"esave",   CmdLegicESave,    IfPm3Legicrf,    "Save emulator memory to binary file"},
+    {"eview",   CmdLegicEView,    IfPm3Legicrf,    "View emulator memory"},
+    {"-----------", CmdHelp,      AlwaysAvailable, "--------------------- " _CYAN_("utils") " ---------------------"},
     {"crc",     CmdLegicCalcCrc,  AlwaysAvailable, "Calculate Legic CRC over given bytes"},
-    {"eload",   CmdLegicELoad,    AlwaysAvailable,    "Load binary dump to emulator memory"},
-    {"esave",   CmdLegicESave,    AlwaysAvailable,    "Save emulator memory to binary file"},
-    {"wipe",    CmdLegicWipe,     IfPm3Legicrf,    "Wipe a LEGIC Prime tag"},
+    {"view",    CmdLegicView,     AlwaysAvailable, "Display content from tag dump file"},
     {NULL, NULL, NULL, NULL}
 };
 
diff --git a/client/src/fileutils.c b/client/src/fileutils.c
index f2249d1ee..2ac29b849 100644
--- a/client/src/fileutils.c
+++ b/client/src/fileutils.c
@@ -1239,6 +1239,10 @@ int loadFileJSONex(const char *preferredName, void *data, size_t maxdatalen, siz
         JsonLoadBufAsHex(root, "$.raw", udata, maxdatalen, datalen);
     }
 
+    if (!strcmp(ctype, "legic")) {
+        JsonLoadBufAsHex(root, "$.raw", udata, maxdatalen, datalen);
+    }
+
 out:
 
     if (callback != NULL) {
diff --git a/include/legic.h b/include/legic.h
index 7fb3ff19b..ce816cdf8 100644
--- a/include/legic.h
+++ b/include/legic.h
@@ -30,7 +30,14 @@ typedef struct {
     uint8_t cmdsize;
     uint8_t addrsize;
     uint16_t cardsize;
-} legic_card_select_t;
+} PACKED legic_card_select_t;
+
+typedef struct {
+    uint16_t offset;
+    uint16_t len;
+    uint8_t iv;
+    uint8_t data[];
+} PACKED legic_packet_t;
 
 // iceman: todo :  this should be packed
 
