commit 6bb301f4dac7a47e44b6396b4747bd4da8333bb4
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Jan 6 23:07:47 2022 +0100

    cppcheck fixes for const

diff --git a/armsrc/Standalone/hf_msdsal.c b/armsrc/Standalone/hf_msdsal.c
index 4a47a8fbb..f253b595b 100644
--- a/armsrc/Standalone/hf_msdsal.c
+++ b/armsrc/Standalone/hf_msdsal.c
@@ -348,7 +348,7 @@ void RunMod(void) {
                 state = STATE_READ;
                 DbpString(_YELLOW_("[ ") "Initialized reading mode" _YELLOW_(" ]"));
                 DbpString("\n" _YELLOW_("!!") "Waiting for a Visa card...");
-                break;
+                continue;
             }
 
             // We need to listen to the high-frequency, peak-detected path.
diff --git a/armsrc/Standalone/hf_reblay.c b/armsrc/Standalone/hf_reblay.c
index c058e83f2..6c7f16ede 100644
--- a/armsrc/Standalone/hf_reblay.c
+++ b/armsrc/Standalone/hf_reblay.c
@@ -99,7 +99,7 @@ void RunMod() {
 
     // For received Bluetooth package
     uint8_t rpacket[MAX_FRAME_SIZE] = { 0x00 };
-    uint16_t lenpacket = 0;
+    uint16_t lenpacket;
 
     // For answering the commands
     uint8_t apdubuffer[MAX_FRAME_SIZE] = { 0x00 };
@@ -268,7 +268,7 @@ void RunMod() {
                 SpinDelay(500);
                 state = STATE_READ;
                 DbpString(_YELLOW_("[ ") "Initialized reading mode" _YELLOW_(" ]"));
-                break;
+                continue;
             }
 
             // We need to listen to the high-frequency, peak-detected path.
diff --git a/armsrc/Standalone/hf_tcprst.c b/armsrc/Standalone/hf_tcprst.c
index 3f6a77687..8527ba2f1 100644
--- a/armsrc/Standalone/hf_tcprst.c
+++ b/armsrc/Standalone/hf_tcprst.c
@@ -200,7 +200,7 @@ void RunMod(void) {
                 state = STATE_READ;
                 DbpString(_YELLOW_("[ ") "Initialized reading mode" _YELLOW_(" ]"));
                 DbpString("\n" _YELLOW_("!!") "Waiting for an ST25TA card...");
-                break;
+                continue;
             }
 
             // We need to listen to the high-frequency, peak-detected path.
@@ -378,7 +378,7 @@ void RunMod(void) {
                 state = STATE_READ;
                 DbpString(_YELLOW_("[ ") "Initialized reading mode" _YELLOW_(" ]"));
                 DbpString("\n" _YELLOW_("!!") "Waiting for an ST25TA card...");
-                break;
+                continue;
             }
 
             // We need to listen to the high-frequency, peak-detected path.
diff --git a/armsrc/hitag2.c b/armsrc/hitag2.c
index 20518674c..f5730f14b 100644
--- a/armsrc/hitag2.c
+++ b/armsrc/hitag2.c
@@ -1146,7 +1146,7 @@ void SniffHitag2(bool ledcontrol) {
     AT91C_BASE_TCB->TCB_BCR = 1;
 
     int frame_count = 0, response = 0, overflow = 0, lastbit = 1, tag_sof = 4;
-    bool rising_edge = false, reader_frame = false, bSkip = true;
+    bool rising_edge, reader_frame = false, bSkip = true;
     uint8_t rx[HITAG_FRAME_LEN];
     size_t rxlen = 0;
 
@@ -1915,6 +1915,11 @@ out:
 
     // release allocated memory from BigBuff.
     BigBuf_free();
+    //
+    if (checked == -1) {
+        // user interupted
+        reply_mix(CMD_ACK, false, 0, 0, 0, 0);
+    }
 
     if (bSuccessful)
         reply_mix(CMD_ACK, bSuccessful, 0, 0, (uint8_t *)tag.sectors, tag_size);
@@ -2233,5 +2238,9 @@ out:
     // release allocated memory from BigBuff.
     BigBuf_free();
 
-    reply_mix(CMD_ACK, bSuccessful, 0, 0, (uint8_t *)tag.sectors, tag_size);
+    if (checked == -1) {
+        reply_mix(CMD_ACK, false, 0, 0, 0, 0);
+    } else {
+        reply_mix(CMD_ACK, bSuccessful, 0, 0, (uint8_t *)tag.sectors, tag_size);
+    }
 }
diff --git a/armsrc/hitagS.c b/armsrc/hitagS.c
index 71e4264ad..a4a3630c7 100644
--- a/armsrc/hitagS.c
+++ b/armsrc/hitagS.c
@@ -1268,7 +1268,7 @@ void ReadHitagS(hitag_function htf, hitag_data *htd, bool ledcontrol) {
     uint8_t rx[HITAG_FRAME_LEN];
     size_t rxlen = 0;
     uint8_t tx[HITAG_FRAME_LEN];
-    size_t txlen = 0;
+    size_t txlen;
     int t_wait = HITAG_T_WAIT_MAX;
 
 
diff --git a/armsrc/pcf7931.c b/armsrc/pcf7931.c
index f0da7be87..2d56943af 100644
--- a/armsrc/pcf7931.c
+++ b/armsrc/pcf7931.c
@@ -210,7 +210,7 @@ void ReadPCF7931(bool ledcontrol) {
     uint8_t single_blocks[8][17]; // PFC blocks with unknown position
     int single_blocks_cnt = 0;
 
-    size_t n = 0; // transmitted blocks
+    size_t n; // transmitted blocks
     uint8_t tmp_blocks[4][16]; // temporary read buffer
 
     uint8_t found_0_1 = 0; // flag: blocks 0 and 1 were found
diff --git a/client/src/cmdhfmfu.c b/client/src/cmdhfmfu.c
index 875e9a95c..311b02b17 100644
--- a/client/src/cmdhfmfu.c
+++ b/client/src/cmdhfmfu.c
@@ -1114,7 +1114,7 @@ typedef struct {
     uint8_t mlen;
     const char *match;
     uint32_t (*Pwd)(const uint8_t *uid);
-    uint16_t (*Pack)(uint8_t *uid);
+    uint16_t (*Pack)(const uint8_t *uid);
     const char *hint;
 } mfu_identify_t;
 
diff --git a/common/generator.c b/common/generator.c
index a5538c973..8b259a79c 100644
--- a/common/generator.c
+++ b/common/generator.c
@@ -157,10 +157,10 @@ uint16_t ul_ev1_packgenA(const uint8_t *uid) {
     uint16_t pack = (uid[0] ^ uid[1] ^ uid[2]) << 8 | (uid[2] ^ 8);
     return pack;
 }
-uint16_t ul_ev1_packgenB(uint8_t *uid) {
+uint16_t ul_ev1_packgenB(const uint8_t *uid) {
     return 0x8080;
 }
-uint16_t ul_ev1_packgenC(uint8_t *uid) {
+uint16_t ul_ev1_packgenC(const uint8_t *uid) {
     return 0xaa55;
 }
 uint16_t ul_ev1_packgenD(const uint8_t *uid) {
@@ -182,10 +182,11 @@ uint16_t ul_ev1_packgenD(const uint8_t *uid) {
     return BSWAP_16(p & 0xFFFF);
 }
 
+// default shims
 uint32_t ul_ev1_pwdgen_def(const uint8_t *uid) {
     return 0xFFFFFFFF;
 }
-uint16_t ul_ev1_packgen_def(uint8_t *uid) {
+uint16_t ul_ev1_packgen_def(const uint8_t *uid) {
     return 0x0000;
 }
 
diff --git a/common/generator.h b/common/generator.h
index ac13c9e3c..c25655a76 100644
--- a/common/generator.h
+++ b/common/generator.h
@@ -19,10 +19,10 @@ uint32_t ul_ev1_pwdgenB(const uint8_t *uid);
 uint32_t ul_ev1_pwdgenC(const uint8_t *uid);
 uint32_t ul_ev1_pwdgenD(const uint8_t *uid);
 
-uint16_t ul_ev1_packgen_def(uint8_t *uid);
+uint16_t ul_ev1_packgen_def(const uint8_t *uid);
 uint16_t ul_ev1_packgenA(const uint8_t *uid);
-uint16_t ul_ev1_packgenB(uint8_t *uid);
-uint16_t ul_ev1_packgenC(uint8_t *uid);
+uint16_t ul_ev1_packgenB(const uint8_t *uid);
+uint16_t ul_ev1_packgenC(const uint8_t *uid);
 uint16_t ul_ev1_packgenD(const uint8_t *uid);
 
 int mfc_algo_ving_one(uint8_t *uid, uint8_t sector, uint8_t keytype, uint64_t *key);
