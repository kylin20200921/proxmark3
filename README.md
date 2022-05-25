commit e550f8ccc85b745e3961a096b5e3a602adaa5034
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Jan 6 15:40:11 2022 +0100

    cppcheck fixes for const

diff --git a/armsrc/iso14443a.c b/armsrc/iso14443a.c
index 7fb0b4b0c..e2f2cf585 100644
--- a/armsrc/iso14443a.c
+++ b/armsrc/iso14443a.c
@@ -801,7 +801,7 @@ void RAMFUNC SniffIso14443a(uint8_t param) {
 //-----------------------------------------------------------------------------
 // Prepare tag messages
 //-----------------------------------------------------------------------------
-static void CodeIso14443aAsTagPar(const uint8_t *cmd, uint16_t len, uint8_t *par, bool collision) {
+static void CodeIso14443aAsTagPar(const uint8_t *cmd, uint16_t len, const uint8_t *par, bool collision) {
 
     tosend_reset();
 
@@ -2047,7 +2047,7 @@ int EmGetCmd(uint8_t *received, uint16_t *len, uint8_t *par) {
     }
 }
 
-int EmSendCmd14443aRaw(uint8_t *resp, uint16_t respLen) {
+int EmSendCmd14443aRaw(const uint8_t *resp, uint16_t respLen) {
     volatile uint8_t b;
     uint16_t i = 0;
     uint32_t ThisTransferTime;
@@ -2412,7 +2412,7 @@ void iso14443a_antifuzz(uint32_t flags) {
     BigBuf_free_keep_EM();
 }
 
-static void iso14a_set_ATS_times(uint8_t *ats) {
+static void iso14a_set_ATS_times(const uint8_t *ats) {
 
     if (ats[0] > 1) {                           // there is a format byte T0
         if ((ats[1] & 0x20) == 0x20) {          // there is an interface byte TB(1)
diff --git a/armsrc/iso14443a.h b/armsrc/iso14443a.h
index 58664201a..a90e88dcc 100644
--- a/armsrc/iso14443a.h
+++ b/armsrc/iso14443a.h
@@ -154,7 +154,7 @@ int iso14443a_select_cardEx(uint8_t *uid_ptr, iso14a_card_select_t *p_card, uint
 int iso14443a_fast_select_card(uint8_t *uid_ptr, uint8_t num_cascades);
 void iso14a_set_trigger(bool enable);
 
-int EmSendCmd14443aRaw(uint8_t *resp, uint16_t respLen);
+int EmSendCmd14443aRaw(const uint8_t *resp, uint16_t respLen);
 int EmSend4bit(uint8_t resp);
 int EmSendCmd(uint8_t *resp, uint16_t respLen);
 int EmSendCmdEx(uint8_t *resp, uint16_t respLen, bool collision);
diff --git a/armsrc/iso14443b.c b/armsrc/iso14443b.c
index 8ad7b87d0..f6304fcb5 100644
--- a/armsrc/iso14443b.c
+++ b/armsrc/iso14443b.c
@@ -678,7 +678,7 @@ static int GetIso14443bCommandFromReader(uint8_t *received, uint16_t *len) {
     return false;
 }
 
-static void TransmitFor14443b_AsTag(uint8_t *response, uint16_t len) {
+static void TransmitFor14443b_AsTag(const uint8_t *response, uint16_t len) {
 
     // Signal field is off with the appropriate LED
     LED_D_OFF();
diff --git a/armsrc/iso15693.c b/armsrc/iso15693.c
index a09415f76..77c67c3cf 100644
--- a/armsrc/iso15693.c
+++ b/armsrc/iso15693.c
@@ -140,7 +140,7 @@ static uint8_t encode15_lut[] = {
     0x01  // 00000001
 };
 
-void CodeIso15693AsReader(uint8_t *cmd, int n) {
+void CodeIso15693AsReader(const uint8_t *cmd, int n) {
 
     tosend_reset();
     tosend_t *ts = get_tosend();
@@ -181,7 +181,7 @@ static void CodeIso15693AsReaderEOF(void) {
 // encode data using "1 out of 256" scheme
 // data rate is 1,66 kbit/s (fc/8192)
 // is designed for more robust communication over longer distances
-static void CodeIso15693AsReader256(uint8_t *cmd, int n) {
+static void CodeIso15693AsReader256(const uint8_t *cmd, int n) {
 
     tosend_reset();
     tosend_t *ts = get_tosend();
@@ -218,7 +218,7 @@ static const uint8_t encode_4bits[16] = {
     0xa5, 0x65, 0x95, 0x55
 };
 
-void CodeIso15693AsTag(uint8_t *cmd, size_t len) {
+void CodeIso15693AsTag(const uint8_t *cmd, size_t len) {
     /*
      * SOF comprises 3 parts;
      * * An unmodulated time of 56.64 us
@@ -2094,7 +2094,7 @@ void LockPassSlixIso15693(uint32_t pass_id, uint32_t password) {
 //-----------------------------------------------------------------------------
 
 // Set the UID on Magic ISO15693 tag (based on Iceman's LUA-script).
-void SetTag15693Uid(uint8_t *uid) {
+void SetTag15693Uid(const uint8_t *uid) {
 
     LED_A_ON();
 
@@ -2135,7 +2135,7 @@ void SetTag15693Uid(uint8_t *uid) {
     switch_off();
 }
 
-static void init_password_15693_slixl(uint8_t *buffer, uint8_t *pwd, uint8_t *rnd) {
+static void init_password_15693_slixl(uint8_t *buffer, uint8_t *pwd, const uint8_t *rnd) {
     memcpy(buffer, pwd, 4);
     if (rnd) {
         buffer[0] ^= rnd[0];
diff --git a/armsrc/iso15693.h b/armsrc/iso15693.h
index 1b6c96977..b4f634fff 100644
--- a/armsrc/iso15693.h
+++ b/armsrc/iso15693.h
@@ -35,8 +35,8 @@
 
 void Iso15693InitReader(void);
 void Iso15693InitTag(void);
-void CodeIso15693AsReader(uint8_t *cmd, int n);
-void CodeIso15693AsTag(uint8_t *cmd, size_t len);
+void CodeIso15693AsReader(const uint8_t *cmd, int n);
+void CodeIso15693AsTag(const uint8_t *cmd, size_t len);
 
 void TransmitTo15693Reader(const uint8_t *cmd, size_t len, uint32_t *start_time, uint32_t slot_time, bool slow);
 int GetIso15693CommandFromReader(uint8_t *received, size_t max_len, uint32_t *eof_time);
@@ -57,7 +57,7 @@ int SendDataTag(uint8_t *send, int sendlen, bool init, bool speed_fast, uint8_t
 
 int SendDataTagEOF(uint8_t *recv, uint16_t max_recv_len, uint32_t start_time, uint16_t timeout, uint32_t *eof_time);
 
-void SetTag15693Uid(uint8_t *uid);
+void SetTag15693Uid(const uint8_t *uid);
 
 void DisablePrivacySlixLIso15693(uint8_t *password);
 #endif
diff --git a/armsrc/lfops.c b/armsrc/lfops.c
index d19e61d65..52e3f8db6 100644
--- a/armsrc/lfops.c
+++ b/armsrc/lfops.c
@@ -403,7 +403,9 @@ static bool prev_keep = false;
  * @param period_1
  * @param command (in binary char array)
  */
-void ModThenAcquireRawAdcSamples125k(uint32_t delay_off, uint16_t period_0, uint16_t period_1, uint8_t *symbol_extra, uint16_t *period_extra, uint8_t *command, bool verbose, bool keep_field_on, uint32_t samples, bool ledcontrol) {
+void ModThenAcquireRawAdcSamples125k(uint32_t delay_off, uint16_t period_0, uint16_t period_1, 
+                   const uint8_t *symbol_extra, uint16_t *period_extra, uint8_t *command,
+                   bool verbose, bool keep_field_on, uint32_t samples, bool ledcontrol) {
 
     if (!prev_keep) {
         FpgaDownloadAndGo(FPGA_BITSTREAM_LF);
@@ -1104,7 +1106,8 @@ static void leadingZeroBiphaseSimBits(int *n, uint8_t clock, uint8_t *phase) {
 
 
 // args clock, ask/man or askraw, invert, transmission separator
-void CmdASKsimTAG(uint8_t encoding, uint8_t invert, uint8_t separator, uint8_t clk, uint16_t size, uint8_t *bits, bool ledcontrol) {
+void CmdASKsimTAG(uint8_t encoding, uint8_t invert, uint8_t separator, uint8_t clk,
+                  uint16_t size, const uint8_t *bits, bool ledcontrol) {
     FpgaDownloadAndGo(FPGA_BITSTREAM_LF);
     set_tracing(false);
 
@@ -1184,7 +1187,8 @@ static void pskSimBit(uint8_t waveLen, int *n, uint8_t clk, uint8_t *curPhase, b
 }
 
 // args clock, carrier, invert,
-void CmdPSKsimTAG(uint8_t carrier, uint8_t invert, uint8_t clk, uint16_t size, uint8_t *bits, bool ledcontrol) {
+void CmdPSKsimTAG(uint8_t carrier, uint8_t invert, uint8_t clk, uint16_t size,
+                  const uint8_t *bits, bool ledcontrol) {
     FpgaDownloadAndGo(FPGA_BITSTREAM_LF);
     set_tracing(false);
 
@@ -1218,7 +1222,8 @@ static void nrzSimBit(uint8_t c, int *n, uint8_t clock) {
 }
 
 // args clock,
-void CmdNRZsimTAG(uint8_t invert, uint8_t separator, uint8_t clk, uint16_t size, uint8_t *bits, bool ledcontrol) {
+void CmdNRZsimTAG(uint8_t invert, uint8_t separator, uint8_t clk, uint16_t size,
+                  const uint8_t *bits, bool ledcontrol) {
 
     FpgaDownloadAndGo(FPGA_BITSTREAM_LF);
     set_tracing(false);
@@ -2219,7 +2224,7 @@ void T55xxWakeUp(uint32_t pwd, uint8_t flags, bool ledcontrol) {
 }
 
 /*-------------- Cloning routines -----------*/
-static void WriteT55xx(uint32_t *blockdata, uint8_t startblock, uint8_t numblocks, bool ledcontrol) {
+static void WriteT55xx(const uint32_t *blockdata, uint8_t startblock, uint8_t numblocks, bool ledcontrol) {
     t55xx_write_block_t cmd;
     cmd.pwd     = 0;
     cmd.flags   = 0;
diff --git a/armsrc/lfops.h b/armsrc/lfops.h
index 0d6b7e342..494b59623 100644
--- a/armsrc/lfops.h
+++ b/armsrc/lfops.h
@@ -21,7 +21,10 @@
 
 #include "pm3_cmd.h" // struct
 
-void ModThenAcquireRawAdcSamples125k(uint32_t delay_off, uint16_t period_0, uint16_t period_1, uint8_t *symbol_extra, uint16_t *period_extra, uint8_t *command, bool verbose, bool keep_field_on, uint32_t samples, bool ledcontrol);
+void ModThenAcquireRawAdcSamples125k(uint32_t delay_off, uint16_t period_0, uint16_t period_1, 
+                   const uint8_t *symbol_extra, uint16_t *period_extra, uint8_t *command, bool verbose, 
+                   bool keep_field_on, uint32_t samples, bool ledcontrol);
+
 void ReadTItag(bool ledcontrol);
 void WriteTItag(uint32_t idhi, uint32_t idlo, uint16_t crc, bool ledcontrol);
 
@@ -34,11 +37,16 @@ void SimulateTagLowFrequencyBidir(int divisor, int max_bitlen);
 void CmdHIDsimTAGEx(uint32_t hi2, uint32_t hi, uint32_t lo, uint8_t longFMT, bool ledcontrol, int numcycles);
 void CmdHIDsimTAG(uint32_t hi2, uint32_t hi, uint32_t lo, uint8_t longFMT, bool ledcontrol);
 
-void CmdFSKsimTAGEx(uint8_t fchigh, uint8_t fclow, uint8_t separator, uint8_t clk, uint16_t bitslen, uint8_t *bits, bool ledcontrol, int numcycles);
-void CmdFSKsimTAG(uint8_t fchigh, uint8_t fclow, uint8_t separator, uint8_t clk, uint16_t bitslen, uint8_t *bits, bool ledcontrol);
-void CmdASKsimTAG(uint8_t encoding, uint8_t invert, uint8_t separator, uint8_t clk, uint16_t size, uint8_t *bits, bool ledcontrol);
-void CmdPSKsimTAG(uint8_t carrier, uint8_t invert, uint8_t clk, uint16_t size, uint8_t *bits, bool ledcontrol);
-void CmdNRZsimTAG(uint8_t invert, uint8_t separator, uint8_t clk, uint16_t size, uint8_t *bits, bool ledcontrol);
+void CmdFSKsimTAGEx(uint8_t fchigh, uint8_t fclow, uint8_t separator, uint8_t clk, uint16_t bitslen,
+                  uint8_t *bits, bool ledcontrol, int numcycles);
+void CmdFSKsimTAG(uint8_t fchigh, uint8_t fclow, uint8_t separator, uint8_t clk, uint16_t bitslen, 
+                  uint8_t *bits, bool ledcontrol);
+void CmdASKsimTAG(uint8_t encoding, uint8_t invert, uint8_t separator, uint8_t clk, uint16_t size,
+                  const uint8_t *bits, bool ledcontrol);
+void CmdPSKsimTAG(uint8_t carrier, uint8_t invert, uint8_t clk, uint16_t size,
+                  const uint8_t *bits, bool ledcontrol);
+void CmdNRZsimTAG(uint8_t invert, uint8_t separator, uint8_t clk, uint16_t size,
+                  const uint8_t *bits, bool ledcontrol);
 
 int lf_hid_watch(int findone, uint32_t *high, uint32_t *low, bool ledcontrol);
 int lf_awid_watch(int findone, uint32_t *high, uint32_t *low, bool ledcontrol); // Realtime demodulation mode for AWID26
@@ -54,7 +62,8 @@ void T55xxResetRead(uint8_t flags, bool ledcontrol);
 //id T55xxWriteBlock(uint32_t data, uint8_t blockno, uint32_t pwd, uint8_t flags, bool ledcontrol);
 void T55xxWriteBlock(uint8_t *data, bool ledcontrol);
 // void T55xxWriteBlockExt(uint32_t data, uint8_t blockno, uint32_t pwd, uint8_t flags);
-void T55xxReadBlock(uint8_t page, bool pwd_mode, bool brute_mem, uint8_t block, uint32_t pwd, uint8_t downlink_mode, bool ledcontrol);
+void T55xxReadBlock(uint8_t page, bool pwd_mode, bool brute_mem, uint8_t block, uint32_t pwd, 
+                    uint8_t downlink_mode, bool ledcontrol);
 void T55xxWakeUp(uint32_t pwd, uint8_t flags, bool ledcontrol);
 void T55xx_ChkPwds(uint8_t flags, bool ledcontrol);
 void T55xxDangerousRawTest(uint8_t *data, bool ledcontrol);
diff --git a/armsrc/mifarecmd.c b/armsrc/mifarecmd.c
index 76312a702..9d3a5f759 100644
--- a/armsrc/mifarecmd.c
+++ b/armsrc/mifarecmd.c
@@ -663,7 +663,7 @@ void MifareUSetPwd(uint8_t arg0, uint8_t *datain) {
 }
 
 // Return 1 if the nonce is invalid else return 0
-static int valid_nonce(uint32_t Nt, uint32_t NtEnc, uint32_t Ks1, uint8_t *parity) {
+static int valid_nonce(uint32_t Nt, uint32_t NtEnc, uint32_t Ks1, const uint8_t *parity) {
     return (
                (oddparity8((Nt >> 24) & 0xFF) == ((parity[0]) ^ oddparity8((NtEnc >> 24) & 0xFF) ^ BIT(Ks1, 16))) && \
                (oddparity8((Nt >> 16) & 0xFF) == ((parity[1]) ^ oddparity8((NtEnc >> 16) & 0xFF) ^ BIT(Ks1, 8))) && \
@@ -1300,7 +1300,7 @@ static uint8_t chkKey_readb(struct chk_t *c, uint8_t *keyb) {
     return res;
 }
 
-static void chkKey_scanA(struct chk_t *c, struct sector_t *k_sector, uint8_t *found, uint8_t *sectorcnt, uint8_t *foundkeys) {
+static void chkKey_scanA(struct chk_t *c, struct sector_t *k_sector, uint8_t *found, const uint8_t *sectorcnt, uint8_t *foundkeys) {
     for (uint8_t s = 0; s < *sectorcnt; s++) {
 
         // skip already found A keys
@@ -1318,7 +1318,7 @@ static void chkKey_scanA(struct chk_t *c, struct sector_t *k_sector, uint8_t *fo
     }
 }
 
-static void chkKey_scanB(struct chk_t *c, struct sector_t *k_sector, uint8_t *found, uint8_t *sectorcnt, uint8_t *foundkeys) {
+static void chkKey_scanB(struct chk_t *c, struct sector_t *k_sector, uint8_t *found, const uint8_t *sectorcnt, uint8_t *foundkeys) {
     for (uint8_t s = 0; s < *sectorcnt; s++) {
 
         // skip already found B keys
diff --git a/armsrc/mifareutil.c b/armsrc/mifareutil.c
index d0da3191e..1a2fed1b4 100644
--- a/armsrc/mifareutil.c
+++ b/armsrc/mifareutil.c
@@ -30,7 +30,7 @@
 #include "desfire_crypto.h"
 
 // crypto1 helpers
-void mf_crypto1_decryptEx(struct Crypto1State *pcs, uint8_t *data_in, int len, uint8_t *data_out) {
+void mf_crypto1_decryptEx(struct Crypto1State *pcs, const uint8_t *data_in, int len, uint8_t *data_out) {
     if (len != 1) {
         for (int i = 0; i < len; i++)
             data_out[i] = crypto1_byte(pcs, 0x00, 0) ^ data_in[i];
@@ -53,7 +53,7 @@ void mf_crypto1_encrypt(struct Crypto1State *pcs, uint8_t *data, uint16_t len, u
     mf_crypto1_encryptEx(pcs, data, NULL, data, len, par);
 }
 
-void mf_crypto1_encryptEx(struct Crypto1State *pcs, uint8_t *data_in, uint8_t *keystream, uint8_t *data_out, uint16_t len, uint8_t *par) {
+void mf_crypto1_encryptEx(struct Crypto1State *pcs, const uint8_t *data_in, uint8_t *keystream, uint8_t *data_out, uint16_t len, uint8_t *par) {
     int i;
     par[0] = 0;
 
diff --git a/armsrc/mifareutil.h b/armsrc/mifareutil.h
index dddb76c98..bb1360b0e 100644
--- a/armsrc/mifareutil.h
+++ b/armsrc/mifareutil.h
@@ -93,9 +93,10 @@ int mifare_desfire_des_auth2(uint32_t uid, uint8_t *key, uint8_t *blockData);
 
 // crypto functions
 void mf_crypto1_decrypt(struct Crypto1State *pcs, uint8_t *data, int len);
-void mf_crypto1_decryptEx(struct Crypto1State *pcs, uint8_t *data_in, int len, uint8_t *data_out);
+void mf_crypto1_decryptEx(struct Crypto1State *pcs, const uint8_t *data_in, int len, uint8_t *data_out);
 void mf_crypto1_encrypt(struct Crypto1State *pcs, uint8_t *data, uint16_t len, uint8_t *par);
-void mf_crypto1_encryptEx(struct Crypto1State *pcs, uint8_t *data_in, uint8_t *keystream, uint8_t *data_out, uint16_t len, uint8_t *par);
+void mf_crypto1_encryptEx(struct Crypto1State *pcs, const uint8_t *data_in, uint8_t *keystream,
+                          uint8_t *data_out, uint16_t len, uint8_t *par);
 uint8_t mf_crypto1_encrypt4bit(struct Crypto1State *pcs, uint8_t data);
 
 // Mifare memory structure
diff --git a/armsrc/optimized_cipher.c b/armsrc/optimized_cipher.c
index bbe9f62ea..68b36af85 100644
--- a/armsrc/optimized_cipher.c
+++ b/armsrc/optimized_cipher.c
@@ -172,7 +172,7 @@ static void opt_successor(const uint8_t *k, State_t *s, uint8_t y) {
     s->l = s->r + r;
 }
 
-static void opt_suc(const uint8_t *k, State_t *s, uint8_t *in, uint8_t length, bool add32Zeroes) {
+static void opt_suc(const uint8_t *k, State_t *s, const uint8_t *in, uint8_t length, bool add32Zeroes) {
     for (int i = 0; i < length; i++) {
         uint8_t head;
         head = in[i];
diff --git a/armsrc/optimized_elite.c b/armsrc/optimized_elite.c
index 7d9e752c9..3e9e1608a 100644
--- a/armsrc/optimized_elite.c
+++ b/armsrc/optimized_elite.c
@@ -60,7 +60,7 @@
  * @param key
  * @param dest
  */
-void permutekey(uint8_t key[8], uint8_t dest[8]) {
+void permutekey(const uint8_t key[8], uint8_t dest[8]) {
     int i;
     for (i = 0 ; i < 8 ; i++) {
         dest[i] = (((key[7] & (0x80 >> i)) >> (7 - i)) << 7) |
@@ -79,7 +79,7 @@ void permutekey(uint8_t key[8], uint8_t dest[8]) {
  * @param key
  * @param dest
  */
-void permutekey_rev(uint8_t key[8], uint8_t dest[8]) {
+void permutekey_rev(const uint8_t key[8], uint8_t dest[8]) {
     int i;
     for (i = 0 ; i < 8 ; i++) {
         dest[7 - i] = (((key[0] & (0x80 >> i)) >> (7 - i)) << 7) |
@@ -129,7 +129,7 @@ static uint8_t swap(uint8_t val) {
  * @param csn the CSN used
  * @param k output
  */
-void hash1(uint8_t csn[], uint8_t k[]) {
+void hash1(const uint8_t csn[], uint8_t k[]) {
     k[0] = csn[0] ^ csn[1] ^ csn[2] ^ csn[3] ^ csn[4] ^ csn[5] ^ csn[6] ^ csn[7];
     k[1] = csn[0] + csn[1] + csn[2] + csn[3] + csn[4] + csn[5] + csn[6] + csn[7];
     k[2] = rr(swap(csn[2] + k[1]));
diff --git a/armsrc/optimized_elite.h b/armsrc/optimized_elite.h
index f11cb8f2d..f921e832a 100644
--- a/armsrc/optimized_elite.h
+++ b/armsrc/optimized_elite.h
@@ -37,21 +37,21 @@
 #include <stdint.h>
 #include <stdlib.h>
 
-void permutekey(uint8_t key[8], uint8_t dest[8]);
+void permutekey(const uint8_t key[8], uint8_t dest[8]);
 /**
  * Permutes  a key from iclass specific format to NIST format
  * @brief permutekey_rev
  * @param key
  * @param dest
  */
-void permutekey_rev(uint8_t key[8], uint8_t dest[8]);
+void permutekey_rev(const uint8_t key[8], uint8_t dest[8]);
 /**
  * Hash1 takes CSN as input, and determines what bytes in the keytable will be used
  * when constructing the K_sel.
  * @param csn the CSN used
  * @param k output
  */
-void hash1(uint8_t *csn, uint8_t *k);
+void hash1(const uint8_t *csn, uint8_t *k);
 void hash2(uint8_t *key64, uint8_t *outp_keytable);
 
 #endif
diff --git a/armsrc/pcf7931.c b/armsrc/pcf7931.c
index 68d660377..f0da7be87 100644
--- a/armsrc/pcf7931.c
+++ b/armsrc/pcf7931.c
@@ -180,7 +180,7 @@ bool IsBlock0PCF7931(uint8_t *block) {
     return false;
 }
 
-bool IsBlock1PCF7931(uint8_t *block) {
+bool IsBlock1PCF7931(const uint8_t *block) {
     // assuming all RFU bits are set to 0
 
     uint8_t rb1 = block[14] & 0x80;
@@ -472,7 +472,7 @@ void WritePCF7931(uint8_t pass1, uint8_t pass2, uint8_t pass3, uint8_t pass4, ui
  * @param tab : array of the data frame
  */
 
-void SendCmdPCF7931(uint32_t *tab, bool ledcontrol) {
+void SendCmdPCF7931(const uint32_t *tab, bool ledcontrol) {
     uint16_t u = 0, tempo = 0;
 
     if (g_dbglevel >= DBG_INFO) {
diff --git a/armsrc/pcf7931.h b/armsrc/pcf7931.h
index 18bd928b6..3be9ea5be 100644
--- a/armsrc/pcf7931.h
+++ b/armsrc/pcf7931.h
@@ -20,9 +20,9 @@
 
 size_t DemodPCF7931(uint8_t **outBlocks, bool ledcontrol);
 bool IsBlock0PCF7931(uint8_t *block);
-bool IsBlock1PCF7931(uint8_t *block);
+bool IsBlock1PCF7931(const uint8_t *block);
 void ReadPCF7931(bool ledcontrol);
-void SendCmdPCF7931(uint32_t *tab, bool ledcontrol);
+void SendCmdPCF7931(const uint32_t *tab, bool ledcontrol);
 bool AddBytePCF7931(uint8_t byte, uint32_t *tab, int32_t l, int32_t p);
 bool AddBitPCF7931(bool b, uint32_t *tab, int32_t l, int32_t p);
 bool AddPatternPCF7931(uint32_t a, uint32_t b, uint32_t c, uint32_t *tab);
diff --git a/armsrc/thinfilm.c b/armsrc/thinfilm.c
index 96cbeafe5..82db7c7cb 100644
--- a/armsrc/thinfilm.c
+++ b/armsrc/thinfilm.c
@@ -78,7 +78,7 @@ static void CodeThinfilmAsTag(const uint8_t *cmd, uint16_t len) {
     ts->max++;
 }
 
-static int EmSendCmdThinfilmRaw(uint8_t *resp, uint16_t respLen) {
+static int EmSendCmdThinfilmRaw(const uint8_t *resp, uint16_t respLen) {
     volatile uint8_t b;
     uint16_t i = 0;
     uint32_t ThisTransferTime;
diff --git a/armsrc/wiegand.c b/armsrc/wiegand.c
index 506d43ded..4de79b8ac 100644
--- a/armsrc/wiegand.c
+++ b/armsrc/wiegand.c
@@ -25,7 +25,7 @@
 * @param type     use the defined values  EVEN|ODD
 * @return parity bit required to match type
 */
-uint8_t getParity(uint8_t *bits, uint8_t len, uint8_t type) {
+uint8_t getParity(const uint8_t *bits, uint8_t len, uint8_t type) {
     uint8_t x = 0;
     for (; len > 0; --len)
         x += bits[len - 1];
@@ -89,7 +89,7 @@ size_t removeParity(uint8_t *bits, size_t startIdx, uint8_t pLen, uint8_t pType,
 * @param pType      EVEN|ODD|2 (always 1's)|3 (always 0's)
 * @return
 */
-size_t addParity(uint8_t *src, uint8_t *dest, uint8_t sourceLen, uint8_t pLen, uint8_t pType) {
+size_t addParity(const uint8_t *src, uint8_t *dest, uint8_t sourceLen, uint8_t pLen, uint8_t pType) {
     uint32_t parityWd = 0;
     size_t j = 0, bitCnt = 0;
     for (int word = 0; word < sourceLen; word += pLen - 1) {
diff --git a/armsrc/wiegand.h b/armsrc/wiegand.h
index 5001bf715..1c92b2ea6 100644
--- a/armsrc/wiegand.h
+++ b/armsrc/wiegand.h
@@ -21,7 +21,7 @@
 
 #include "common.h"
 
-uint8_t getParity(uint8_t *bits, uint8_t len, uint8_t type);
+uint8_t getParity(const uint8_t *bits, uint8_t len, uint8_t type);
 uint8_t checkParity(uint32_t bits, uint8_t len, uint8_t type);
 
 void num_to_wiegand_bytes(uint64_t oem, uint64_t fc, uint64_t cn, uint8_t *dest, uint8_t formatlen);
