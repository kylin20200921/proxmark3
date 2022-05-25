commit 4493114f4ffe65aed53054bc69c059009ff9f150
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Dec 27 17:43:28 2021 +0100

    renamed function and exported it

diff --git a/armsrc/lfops.c b/armsrc/lfops.c
index 772a01e9c..065577106 100644
--- a/armsrc/lfops.c
+++ b/armsrc/lfops.c
@@ -422,7 +422,7 @@ void ModThenAcquireRawAdcSamples125k(uint32_t delay_off, uint16_t period_0, uint
     // Some tags need to be interrogated very soon after activation else they enter their emulation mode
     // Therefore it's up to the caller to add an initial symbol of adequate duration, except for bitbang mode.
     if (bitbang) {
-        TurnReadLFOn(20000);
+        turn_read_lf_on(20000);
         // HACK it appears the loop and if statements take up about 7us so adjust waits accordingly...
         uint8_t hack_cnt = 7;
         if (period_0 < hack_cnt || period_1 < hack_cnt) {
@@ -472,13 +472,13 @@ void ModThenAcquireRawAdcSamples125k(uint32_t delay_off, uint16_t period_0, uint
         while (*command != '\0' && *command != ' ') {
             if (ledcontrol) LED_D_ON();
             if (*command == '0') {
-                TurnReadLFOn(period_0);
+                turn_read_lf_on(period_0);
             } else if (*command == '1') {
-                TurnReadLFOn(period_1);
+                turn_read_lf_on(period_1);
             } else {
                 for (uint8_t i = 0; i < LF_CMDREAD_MAX_EXTRA_SYMBOLS; i++) {
                     if (*command == symbol_extra[i]) {
-                        TurnReadLFOn(period_extra[i]);
+                        turn_read_lf_on(period_extra[i]);
                         break;
                     }
                 }
@@ -1635,14 +1635,15 @@ int lf_io_watch(int findone, uint32_t *high, uint32_t *low, bool ledcontrol) {
  * Q5 tags seems to have issues when these values changes.
  */
 
-void TurnReadLFOn(uint32_t delay) {
+void turn_read_lf_on(uint32_t delay) {
     FpgaWriteConfWord(FPGA_MAJOR_MODE_LF_READER | FPGA_LF_ADC_READER_FIELD);
 
     // measure antenna strength.
     //int adcval = ((MAX_ADC_LF_VOLTAGE * (SumAdc(ADC_CHAN_LF, 32) >> 1)) >> 14);
     WaitUS(delay);
 }
-static void TurnReadLF_off(uint32_t delay) {
+
+void turn_read_lf_off(uint32_t delay) {
     FpgaWriteConfWord(FPGA_MAJOR_MODE_OFF);
     WaitUS(delay);
 }
@@ -1659,23 +1660,23 @@ static void T55xxWriteBit(uint8_t bit, uint8_t downlink_idx) {
     switch (bit) {
         case 0 :
             // send bit 0/00
-            TurnReadLFOn(T55xx_Timing.m[downlink_idx].write_0);
+            turn_read_lf_on(T55xx_Timing.m[downlink_idx].write_0);
             break;
         case 1 :
             // send bit 1/01
-            TurnReadLFOn(T55xx_Timing.m[downlink_idx].write_1);
+            turn_read_lf_on(T55xx_Timing.m[downlink_idx].write_1);
             break;
         case 2 :
             // send bits 10 (1 of 4)
-            TurnReadLFOn(T55xx_Timing.m[downlink_idx].write_2);
+            turn_read_lf_on(T55xx_Timing.m[downlink_idx].write_2);
             break;
         case 3 :
             // send bits 11 (1 of 4)
-            TurnReadLFOn(T55xx_Timing.m[downlink_idx].write_3);
+            turn_read_lf_on(T55xx_Timing.m[downlink_idx].write_3);
             break;
         case 4 :
             // send Long Leading Reference
-            TurnReadLFOn(T55xx_Timing.m[downlink_idx].write_0 + T55_LLR_REF);
+            turn_read_lf_on(T55xx_Timing.m[downlink_idx].write_0 + T55_LLR_REF);
             break;
     }
 
@@ -1838,7 +1839,7 @@ void T55xxResetRead(uint8_t flags, bool ledcontrol) {
 
     T55xx_SendCMD(0, 0, arg);
 
-    TurnReadLFOn(T55xx_Timing.m[downlink_mode].read_gap);
+    turn_read_lf_on(T55xx_Timing.m[downlink_mode].read_gap);
 
     // Acquisition
     DoPartialAcquisition(0, false, BigBuf_max_traceLen(), 0, ledcontrol);
@@ -1881,7 +1882,7 @@ void T55xxDangerousRawTest(uint8_t *data, bool ledcontrol) {
         uint8_t sendbits = (bs[BITSTREAM_BYTE(i)] >> BITSTREAM_BIT(i));
         T55xxWriteBit(sendbits & 1, 0);
     }
-    TurnReadLFOn(c->time);
+    turn_read_lf_on(c->time);
     FpgaWriteConfWord(FPGA_MAJOR_MODE_OFF);
     reply_ng(CMD_LF_T55XX_DANGERRAW, PM3_SUCCESS, NULL, 0);
     if (ledcontrol) LED_A_OFF();
@@ -1925,10 +1926,10 @@ void T55xxWriteBlock(uint8_t *data, bool ledcontrol) {
         // 566-568 switches between wiping to 0s and doing nothing
         // 5184 wipes and allows 1 block to be programmed.
         // indefinite power on wipes and then programs all blocks with bitshifted data sent.
-        TurnReadLFOn(5184);
+        turn_read_lf_on(5184);
 
     } else {
-        TurnReadLFOn(20 * 1000);
+        turn_read_lf_on(20 * 1000);
         //could attempt to do a read to confirm write took
         // as the tag should repeat back the new block
         // until it is reset, but to confirm it we would
@@ -1993,8 +1994,8 @@ bool brute_mem = (flags & 0x0100) >> 8;
     // 137*8 seems to get to the start of data pretty well...
     //  but we want to go past the start and let the repeating data settle in...
 
-    // TurnReadLFOn(210*8); // issues with block 1 reads so dropping down seemed to help
-    TurnReadLFOn(137 * 8);
+    // turn_read_lf_on(210*8); // issues with block 1 reads so dropping down seemed to help
+    turn_read_lf_on(137 * 8);
 
     // Acquisition
     // Now do the acquisition
@@ -2058,8 +2059,8 @@ void T55xxReadBlock(uint8_t page, bool pwd_mode, bool brute_mem, uint8_t block,
     // 137*8 seems to get to the start of data pretty well...
     //  but we want to go past the start and let the repeating data settle in...
 
-    // TurnReadLFOn(210*8); // issues with block 1 reads so dropping down seemed to help
-    TurnReadLFOn(137 * 8);
+    // turn_read_lf_on(210*8); // issues with block 1 reads so dropping down seemed to help
+    turn_read_lf_on(137 * 8);
 
     // Acquisition
     // Now do the acquisition
@@ -2202,7 +2203,7 @@ void T55xxWakeUp(uint32_t pwd, uint8_t flags, bool ledcontrol) {
     T55xx_SendCMD(0, pwd, flags);
 
     //-- Turn and leave field on to let the begin repeating transmission
-    TurnReadLFOn(20 * 1000);
+    turn_read_lf_on(20 * 1000);
     reply_ng(CMD_LF_T55XX_WAKEUP, PM3_SUCCESS, NULL, 0);
 }
 
@@ -2537,16 +2538,16 @@ static void SendForward(uint8_t fwd_bit_count, bool fast) {
     fwd_bit_sz--; //prepare next bit modulation
     fwd_write_ptr++;
 
-    TurnReadLF_off(EM_START_GAP);
-    TurnReadLFOn(18 * 8);
+    turn_read_lf_off(EM_START_GAP);
+    turn_read_lf_on(18 * 8);
 
     // now start writing with bitbanging the antenna. (each bit should be 32*8 total length)
     while (fwd_bit_sz-- > 0) { //prepare next bit modulation
         if (((*fwd_write_ptr++) & 1) == 1) {
             WaitUS(32 * 8);
         } else {
-            TurnReadLF_off(23 * 8);
-            TurnReadLFOn(18 * 8);
+            turn_read_lf_off(23 * 8);
+            turn_read_lf_on(18 * 8);
         }
     }
 }
diff --git a/armsrc/lfops.h b/armsrc/lfops.h
index efd35a897..5c5a0fc74 100644
--- a/armsrc/lfops.h
+++ b/armsrc/lfops.h
@@ -53,7 +53,8 @@ void T55xxWakeUp(uint32_t pwd, uint8_t flags, bool ledcontrol);
 void T55xx_ChkPwds(uint8_t flags, bool ledcontrol);
 void T55xxDangerousRawTest(uint8_t *data, bool ledcontrol);
 
-void TurnReadLFOn(uint32_t delay);
+void turn_read_lf_on(uint32_t delay);
+void turn_read_lf_off(uint32_t delay);
 
 void EM4xLogin(uint32_t pwd, bool ledcontrol);
 void EM4xBruteforce(uint32_t start_pwd, uint32_t n, bool ledcontrol);
