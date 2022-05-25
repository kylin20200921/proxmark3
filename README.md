commit 3491157345763053a6879729ccbe2e9ce26982c8
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Jan 3 22:17:40 2022 +0100

    cppcheck fixes and other minor stuff

diff --git a/armsrc/Standalone/hf_colin.c b/armsrc/Standalone/hf_colin.c
index 93c92f92a..a48d839db 100644
--- a/armsrc/Standalone/hf_colin.c
+++ b/armsrc/Standalone/hf_colin.c
@@ -564,7 +564,7 @@ failtag:
                 err = 1;
                 allKeysFound = false;
                 // used in portable imlementation on microcontroller: it reports back the fail and open the
-                // standalone lock reply_old(CMD_CJB_FSMSTATE_MENU, 0, 0, 0, 0, 0);
+                // standalone lock reply_ng(CMD_CJB_FSMSTATE_MENU, NULL, 0);
                 break;
             } else if (key == -2) {
                 err = 1; // Can't select card.
diff --git a/armsrc/hitag2.c b/armsrc/hitag2.c
index 70f048bab..376971a97 100644
--- a/armsrc/hitag2.c
+++ b/armsrc/hitag2.c
@@ -1369,10 +1369,12 @@ void SimulateHitag2(bool ledcontrol) {
         // use malloc
         initSampleBufferEx(&signal_size, true);
 
-        if (ledcontrol) LED_D_ON();
+        if (ledcontrol) {
+            LED_D_ON();
+            LED_A_OFF();
+        }
 
 //        lf_reset_counter();
-        if (ledcontrol) LED_A_OFF();
         WDT_HIT();
 
         /*
diff --git a/armsrc/lfzx.c b/armsrc/lfzx.c
index 364af4abc..b392eba0e 100644
--- a/armsrc/lfzx.c
+++ b/armsrc/lfzx.c
@@ -87,7 +87,6 @@ static void zx8211_setup_read(void) {
     sample_config *sc = getSamplingConfig();
     LFSetupFPGAForADC(sc->divisor, true);
 
-
     FpgaWriteConfWord(FPGA_MAJOR_MODE_LF_READER | FPGA_LF_ADC_READER_FIELD);
 
     // 50ms for the resonant antenna to settle.
@@ -132,6 +131,26 @@ static void zx_send(uint8_t *cmd, uint8_t clen) {
     turn_read_lf_on(ZX_TEOF * 8);
 }
 
+static void zx_get(bool ledcontrol) {
+
+    while (BUTTON_PRESS() == false) {
+
+        WDT_HIT();
+
+        if (ledcontrol && (AT91C_BASE_SSC->SSC_SR & AT91C_SSC_TXRDY)) {
+            LED_D_ON();
+        }
+
+        if (AT91C_BASE_SSC->SSC_SR & AT91C_SSC_RXRDY) {
+            volatile uint8_t sample = (uint8_t)AT91C_BASE_SSC->SSC_RHR;
+            (void)sample;
+
+            // Test point 8 (TP8) can be used to trigger oscilloscope
+            if (ledcontrol) LED_D_OFF();
+
+        }
+    }
+}
 
 int zx8211_read(zx8211_data_t *zxd, bool ledcontrol) {
     zx8211_setup_read();
@@ -144,13 +163,18 @@ int zx8211_read(zx8211_data_t *zxd, bool ledcontrol) {
     // send GET_UID
     zx_send(NULL, 0);
 
+    FpgaWriteConfWord(FPGA_MAJOR_MODE_LF_READER | FPGA_LF_ADC_READER_FIELD);
+
+    zx_get(ledcontrol);
+
     //uint32_t cs = CRC8Hitag1(uint8_t *buff, size_t size);
 
     if (ledcontrol) LEDsoff();
 
     StopTicks();
     lf_finalize(ledcontrol);
-    //reply_ng(CMD_LF_ZX_READ, status, tag.data, sizeof(tag.data));
+
+    reply_ng(CMD_LF_ZX_READ, PM3_SUCCESS, NULL, 0);
     return PM3_SUCCESS;
 }
 
diff --git a/client/src/cmdhfemrtd.c b/client/src/cmdhfemrtd.c
index 613c0a055..2afefff1d 100644
--- a/client/src/cmdhfemrtd.c
+++ b/client/src/cmdhfemrtd.c
@@ -201,7 +201,7 @@ static int emrtd_exchange_commands_noout(sAPDU_t apdu, bool activate_field, bool
 }
 
 static char emrtd_calculate_check_digit(char *data) {
-    int mrz_weight[] = {7, 3, 1};
+    const int mrz_weight[] = {7, 3, 1};
     int value, cd = 0;
 
     for (int i = 0; i < strlen(data); i++) {
@@ -295,7 +295,7 @@ static void des3_decrypt_cbc(uint8_t *iv, uint8_t *key, uint8_t *input, int inpu
 }
 
 static int pad_block(uint8_t *input, int inputlen, uint8_t *output) {
-    uint8_t padding[8] = {0x80, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00};
+    const uint8_t padding[8] = {0x80, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00};
 
     memcpy(output, input, inputlen);
 
diff --git a/client/src/cmdhfepa.c b/client/src/cmdhfepa.c
index acc89d4c4..9864cc55e 100644
--- a/client/src/cmdhfepa.c
+++ b/client/src/cmdhfepa.c
@@ -132,7 +132,7 @@ static int CmdHFEPAPACEReplay(const char *Cmd) {
 
     uint8_t apdu_lengths[5] = {msesa_len, gn_len, map_len, pka_len, ma_len};
     // pointers to the arrays to be able to iterate
-    uint8_t *apdus[] = {msesa_apdu, gn_apdu, map_apdu, pka_apdu, ma_apdu};
+    const uint8_t *apdus[] = {msesa_apdu, gn_apdu, map_apdu, pka_apdu, ma_apdu};
 
     // Proxmark response
     PacketResponseNG resp;
diff --git a/client/src/cmdhfjooki.c b/client/src/cmdhfjooki.c
index a789fb762..0c89406ba 100644
--- a/client/src/cmdhfjooki.c
+++ b/client/src/cmdhfjooki.c
@@ -135,7 +135,7 @@ static int jooki_encode(uint8_t *iv, uint8_t tid, uint8_t fid, uint8_t *uid, uin
         return PM3_EINVARG;
     }
 
-    uint8_t d[JOOKI_PLAIN_LEN] = {iv[0], iv[1], iv[2], tid, fid, uid[0], uid[1], uid[2], uid[3], uid[4], uid[5], uid[6]};
+    const uint8_t d[JOOKI_PLAIN_LEN] = {iv[0], iv[1], iv[2], tid, fid, uid[0], uid[1], uid[2], uid[3], uid[4], uid[5], uid[6]};
     uint8_t enc[JOOKI_PLAIN_LEN] = {0};
     for (uint8_t i = 0; i < JOOKI_PLAIN_LEN; i++) {
 
diff --git a/client/src/cmdlfzx8211.c b/client/src/cmdlfzx8211.c
index c6c8d7337..893875e50 100644
--- a/client/src/cmdlfzx8211.c
+++ b/client/src/cmdlfzx8211.c
@@ -74,6 +74,17 @@ int demodzx(bool verbose) {
 }
 
 static int lf_Zx_read(void) {
+
+    PacketResponseNG resp;
+    clearCommandBuffer();
+
+    SendCommandNG(CMD_LF_ZX_READ, NULL, 0);
+
+    if (WaitForResponseTimeout(CMD_LF_ZX_READ, &resp, 1000) == false) {
+        PrintAndLogEx(ERR, "Error occurred, device did not respond during read operation.");
+        return PM3_ETIMEOUT;
+    }
+
     return PM3_SUCCESS;
 }
 
