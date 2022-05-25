commit b55a33b1d7b2950fbe7e42d3b308ef21a06183c3
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Jan 6 15:22:36 2022 +0100

    cppcheck fixes for const

diff --git a/armsrc/Standalone/hf_msdsal.c b/armsrc/Standalone/hf_msdsal.c
index 8940909d1..4a47a8fbb 100644
--- a/armsrc/Standalone/hf_msdsal.c
+++ b/armsrc/Standalone/hf_msdsal.c
@@ -65,7 +65,7 @@ void ModInfo(void) {
 static uint8_t ppdol [255] = {0x80, 0xA8, 0x00, 0x00, 0x02, 0x83, 0x00};
 
 // Generate GET PROCESSING
-static uint8_t treatPDOL(uint8_t *apdu) {
+static uint8_t treatPDOL(const uint8_t *apdu) {
 
     uint8_t plen = 7;
 
diff --git a/armsrc/Standalone/lf_nexid.c b/armsrc/Standalone/lf_nexid.c
index 2904bab46..5789226a1 100644
--- a/armsrc/Standalone/lf_nexid.c
+++ b/armsrc/Standalone/lf_nexid.c
@@ -90,7 +90,7 @@ static uint8_t nexwatch_parity_swap(uint8_t parity) {
 }
 // parity check
 // from 32b hex id, 4b mode,
-static uint8_t nexwatch_parity(uint8_t hexid[5]) {
+static uint8_t nexwatch_parity(const uint8_t hexid[5]) {
     uint8_t p = 0;
     for (uint8_t i = 0; i < 5; i++) {
         p ^= NIBBLE_HIGH(hexid[i]);
diff --git a/armsrc/cmd.c b/armsrc/cmd.c
index fd0ce9a93..8857fc66f 100644
--- a/armsrc/cmd.c
+++ b/armsrc/cmd.c
@@ -71,7 +71,7 @@ int reply_old(uint64_t cmd, uint64_t arg0, uint64_t arg1, uint64_t arg2, void *d
     return PM3_SUCCESS;
 }
 
-static int reply_ng_internal(uint16_t cmd, int16_t status, uint8_t *data, size_t len, bool ng) {
+static int reply_ng_internal(uint16_t cmd, int16_t status, const uint8_t *data, size_t len, bool ng) {
     PacketResponseNGRaw txBufferNG;
     size_t txBufferNGLen;
 
diff --git a/armsrc/felica.c b/armsrc/felica.c
index 21c71ff92..6e5f836f1 100644
--- a/armsrc/felica.c
+++ b/armsrc/felica.c
@@ -297,7 +297,7 @@ static uint8_t felica_select_card(felica_card_select_t *card) {
 // Felica standard has a different file system, AFAIK,
 // 8-byte IDm, number of blocks, blocks numbers
 // number of blocks limited to 4 for FelicaLite(S)
-static void BuildFliteRdblk(uint8_t *idm, int blocknum, uint16_t *blocks) {
+static void BuildFliteRdblk(const uint8_t *idm, int blocknum, const uint16_t *blocks) {
     if (blocknum > 4 || blocknum <= 0)
         Dbprintf("Invalid number of blocks, %d != 4", blocknum);
 
diff --git a/armsrc/hitag2crack.c b/armsrc/hitag2crack.c
index bf859c48e..f4943ec57 100644
--- a/armsrc/hitag2crack.c
+++ b/armsrc/hitag2crack.c
@@ -286,7 +286,7 @@ bool hitag2crack_test_e_p0cmd(uint8_t *keybits, uint8_t *nrar, uint8_t *e_cmd, u
 
 // hitag2crack_xor XORs the source with the pad to produce the target.
 // source, target and pad are binarrays of length len.
-void hitag2crack_xor(uint8_t *target, uint8_t *source, uint8_t *pad, unsigned int len) {
+void hitag2crack_xor(uint8_t *target, const uint8_t *source, const uint8_t *pad, unsigned int len) {
 
     for (int i = 0; i < len; i++) {
         target[i] = source[i] ^ pad[i];
diff --git a/armsrc/hitag2crack.h b/armsrc/hitag2crack.h
index a546f3f33..570635f9b 100644
--- a/armsrc/hitag2crack.h
+++ b/armsrc/hitag2crack.h
@@ -22,7 +22,7 @@ bool hitag2_crack(uint8_t *response, uint8_t *nrarhex);
 bool hitag2crack_find_valid_e_cmd(uint8_t e_cmd[], uint8_t nrar[]);
 bool hitag2crack_find_e_page0_cmd(uint8_t keybits[], uint8_t e_firstcmd[], uint8_t nrar[], uint8_t uid[]);
 bool hitag2crack_test_e_p0cmd(uint8_t *keybits, uint8_t *nrar, uint8_t *e_cmd, uint8_t *uid, uint8_t *e_uid);
-void hitag2crack_xor(uint8_t *target, uint8_t *source, uint8_t *pad, unsigned int len);
+void hitag2crack_xor(uint8_t *target, const uint8_t *source, const uint8_t *pad, unsigned int len);
 bool hitag2crack_read_page(uint8_t *responsestr, uint8_t pagenum, uint8_t *nrar, uint8_t *keybits);
 bool hitag2crack_send_e_cmd(uint8_t *responsestr, uint8_t *nrar, uint8_t *cmd, int len);
 bool hitag2crack_tx_rx(uint8_t *responsestr, uint8_t *msg, int len, int state, bool reset);
diff --git a/armsrc/hitagS.c b/armsrc/hitagS.c
index 35ea870ea..7545c4d96 100644
--- a/armsrc/hitagS.c
+++ b/armsrc/hitagS.c
@@ -320,7 +320,7 @@ static void hitag_reader_send_frame(const uint8_t *frame, size_t frame_len, bool
 /*
  * to check if the right uid was selected
  */
-static int check_select(uint8_t *rx, uint32_t uid) {
+static int check_select(const uint8_t *rx, uint32_t uid) {
     unsigned char resp[48];
     uint32_t ans = 0x0;
     for (int i = 0; i < 48; i++)
@@ -1039,7 +1039,7 @@ static void sendReceiveHitagS(uint8_t *tx, size_t txlen, uint8_t *rx, size_t siz
     *prxbits = k;
 }
 
-static size_t concatbits(uint8_t *dstbuf, size_t dstbufskip, uint8_t *srcbuf, size_t srcbufstart, size_t srcbuflen) {
+static size_t concatbits(uint8_t *dstbuf, size_t dstbufskip, const uint8_t *srcbuf, size_t srcbufstart, size_t srcbuflen) {
     // erase dstbuf bits that will be overriden
     dstbuf[dstbufskip / 8] &= 0xFF - ((1 << (7 - (dstbufskip % 8) + 1)) - 1);
     for (size_t i = (dstbufskip / 8) + 1; i <= (dstbufskip + srcbuflen) / 8; i++) {
@@ -1357,11 +1357,11 @@ void ReadHitagS(hitag_function htf, hitag_data *htd, bool ledcontrol) {
  * Authenticates to the Tag with the given Key or Challenge.
  * Writes the given 32Bit data into page_
  */
-void WritePageHitagS(hitag_function htf, hitag_data *htd, int pageNum, bool ledcontrol) {
+void WritePageHitagS(hitag_function htf, hitag_data *htd, int page, bool ledcontrol) {
 
     bool bSuccessful = false;
     //check for valid input
-    if (pageNum == 0) {
+    if (page == 0) {
         Dbprintf("Error, invalid page");
         reply_mix(CMD_ACK, bSuccessful, 0, 0, 0, 0);
         return;
@@ -1378,7 +1378,7 @@ void WritePageHitagS(hitag_function htf, hitag_data *htd, int pageNum, bool ledc
     }
 
     //check if the given page exists
-    if (pageNum > tag.max_page) {
+    if (page > tag.max_page) {
         Dbprintf("page number too big for this tag");
         goto write_end;
     }
@@ -1387,7 +1387,7 @@ void WritePageHitagS(hitag_function htf, hitag_data *htd, int pageNum, bool ledc
     txlen = 0;
     uint8_t cmd = 0x08;
     txlen = concatbits(tx, txlen, &cmd, 8 - 4, 4);
-    uint8_t addr = pageNum;
+    uint8_t addr = page;
     txlen = concatbits(tx, txlen, &addr, 0, 8);
     uint8_t crc = CRC8Hitag1Bits(tx, txlen);
     txlen = concatbits(tx, txlen, &crc, 0, 8);
@@ -1395,7 +1395,7 @@ void WritePageHitagS(hitag_function htf, hitag_data *htd, int pageNum, bool ledc
     sendReceiveHitagS(tx, txlen, rx, ARRAYLEN(rx), &rxlen, t_wait, ledcontrol, false);
 
     if ((rxlen != 2) || (rx[0] >> (8 - 2) != 0x1)) {
-        Dbprintf("no write access on page %d", pageNum);
+        Dbprintf("no write access on page %d", page);
         goto write_end;
     }
 
@@ -1425,9 +1425,9 @@ void WritePageHitagS(hitag_function htf, hitag_data *htd, int pageNum, bool ledc
     sendReceiveHitagS(tx, txlen, rx, ARRAYLEN(rx), &rxlen, t_wait, ledcontrol, false);
 
     if ((rxlen != 2) || (rx[0] >> (8 - 2) != 0x1)) {
-        Dbprintf("write on page %d failed", pageNum);
+        Dbprintf("write on page %d failed", page);
     } else {
-        Dbprintf("write on page %d successful", pageNum);
+        Dbprintf("write on page %d successful", page);
         bSuccessful = true;
     }
 
@@ -1457,25 +1457,31 @@ void Hitag_check_challenges(uint8_t *data, uint32_t datalen, bool ledcontrol) {
     uint8_t tx[HITAG_FRAME_LEN];
     int t_wait = HITAG_T_WAIT_MAX;
 
-    while (!BUTTON_PRESS() && !data_available()) {
+    while (BUTTON_PRESS() == false && data_available() == false) {
         // Watchdog hit
         WDT_HIT();
 
         hitag_data htd;
+        memset(&htd, 0, sizeof(htd));
+
         memcpy(htd.auth.NrAr, data + dataoffset, 8);
 
         int res = selectHitagS(RHTSF_CHALLENGE, &htd, tx, ARRAYLEN(tx), rx, ARRAYLEN(rx), t_wait, ledcontrol);
-        Dbprintf("Challenge %s: %02X %02X %02X %02X %02X %02X %02X %02X", res == -1 ? "failed " : "success",
+        Dbprintf("Challenge %s: %02X %02X %02X %02X %02X %02X %02X %02X",
+                 res == -1 ? "failed " : "success",
                  htd.auth.NrAr[0], htd.auth.NrAr[1],
                  htd.auth.NrAr[2], htd.auth.NrAr[3],
                  htd.auth.NrAr[4], htd.auth.NrAr[5],
-                 htd.auth.NrAr[6], htd.auth.NrAr[7]);
+                 htd.auth.NrAr[6], htd.auth.NrAr[7]
+                );
+
         if (res == -1) {
             // Need to do a dummy UID select that will fail
             FpgaWriteConfWord(FPGA_MAJOR_MODE_OFF);
             SpinDelay(2);
             selectHitagS(RHTSF_CHALLENGE, &htd, tx, ARRAYLEN(tx), rx, ARRAYLEN(rx), t_wait, ledcontrol);
         }
+
         dataoffset += 8;
         if (dataoffset >= datalen - 8)
             break;
@@ -1484,6 +1490,7 @@ void Hitag_check_challenges(uint8_t *data, uint32_t datalen, bool ledcontrol) {
         // min t_reset = 2ms
         SpinDelay(2);
     }
+
     set_tracing(false);
     lf_finalize(ledcontrol);
     reply_mix(CMD_ACK, 1, 0, 0, 0, 0);
diff --git a/armsrc/iclass.c b/armsrc/iclass.c
index e8029dd69..134b633a8 100644
--- a/armsrc/iclass.c
+++ b/armsrc/iclass.c
@@ -94,7 +94,7 @@ void SniffIClass(uint8_t jam_search_len, uint8_t *jam_search_string) {
     SniffIso15693(jam_search_len, jam_search_string);
 }
 
-static void rotateCSN(uint8_t *original_csn, uint8_t *rotated_csn) {
+static void rotateCSN(const uint8_t *original_csn, uint8_t *rotated_csn) {
     for (uint8_t i = 0; i < 8; i++) {
         rotated_csn[i] = (original_csn[i] >> 3) | (original_csn[(i + 1) % 8] << 5);
     }
