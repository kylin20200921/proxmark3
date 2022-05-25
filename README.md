commit 05576ba06b8c595f1c44b8903caaef782aaccacc
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Jan 6 21:00:16 2022 +0100

    cppcheck fixes

diff --git a/client/src/cmdanalyse.c b/client/src/cmdanalyse.c
index 4d3352da8..f4624dbb7 100644
--- a/client/src/cmdanalyse.c
+++ b/client/src/cmdanalyse.c
@@ -56,7 +56,7 @@ static uint16_t shiftadd ( uint8_t* bytes, uint8_t len){
     return 0;
 }
 */
-static uint16_t calcSumCrumbAdd(uint8_t *bytes, uint8_t len, uint32_t mask) {
+static uint16_t calcSumCrumbAdd(const uint8_t *bytes, uint8_t len, uint32_t mask) {
     uint32_t sum = 0;
     for (uint8_t i = 0; i < len; i++) {
         sum += CRUMB(bytes[i], 0);
@@ -67,10 +67,10 @@ static uint16_t calcSumCrumbAdd(uint8_t *bytes, uint8_t len, uint32_t mask) {
     sum &= mask;
     return (sum & 0xFFFF);
 }
-static uint16_t calcSumCrumbAddOnes(uint8_t *bytes, uint8_t len, uint32_t mask) {
+static uint16_t calcSumCrumbAddOnes(const uint8_t *bytes, uint8_t len, uint32_t mask) {
     return (~calcSumCrumbAdd(bytes, len, mask) & mask);
 }
-static uint16_t calcSumNibbleAdd(uint8_t *bytes, uint8_t len, uint32_t mask) {
+static uint16_t calcSumNibbleAdd(const uint8_t *bytes, uint8_t len, uint32_t mask) {
     uint32_t sum = 0;
     for (uint8_t i = 0; i < len; i++) {
         sum += NIBBLE_LOW(bytes[i]);
@@ -82,7 +82,7 @@ static uint16_t calcSumNibbleAdd(uint8_t *bytes, uint8_t len, uint32_t mask) {
 static uint16_t calcSumNibbleAddOnes(uint8_t *bytes, uint8_t len, uint32_t mask) {
     return (~calcSumNibbleAdd(bytes, len, mask) & mask);
 }
-static uint16_t calcSumCrumbXor(uint8_t *bytes, uint8_t len, uint32_t mask) {
+static uint16_t calcSumCrumbXor(const uint8_t *bytes, uint8_t len, uint32_t mask) {
     uint32_t sum = 0;
     for (uint8_t i = 0; i < len; i++) {
         sum ^= CRUMB(bytes[i], 0);
@@ -93,7 +93,7 @@ static uint16_t calcSumCrumbXor(uint8_t *bytes, uint8_t len, uint32_t mask) {
     sum &= mask;
     return (sum & 0xFFFF);
 }
-static uint16_t calcSumNibbleXor(uint8_t *bytes, uint8_t len, uint32_t mask) {
+static uint16_t calcSumNibbleXor(const uint8_t *bytes, uint8_t len, uint32_t mask) {
     uint32_t sum = 0;
     for (uint8_t i = 0; i < len; i++) {
         sum ^= NIBBLE_LOW(bytes[i]);
@@ -102,7 +102,7 @@ static uint16_t calcSumNibbleXor(uint8_t *bytes, uint8_t len, uint32_t mask) {
     sum &= mask;
     return (sum & 0xFFFF);
 }
-static uint16_t calcSumByteXor(uint8_t *bytes, uint8_t len, uint32_t mask) {
+static uint16_t calcSumByteXor(const uint8_t *bytes, uint8_t len, uint32_t mask) {
     uint32_t sum = 0;
     for (uint8_t i = 0; i < len; i++) {
         sum ^= bytes[i];
@@ -110,7 +110,7 @@ static uint16_t calcSumByteXor(uint8_t *bytes, uint8_t len, uint32_t mask) {
     sum &= mask;
     return (sum & 0xFFFF);
 }
-static uint16_t calcSumByteAdd(uint8_t *bytes, uint8_t len, uint32_t mask) {
+static uint16_t calcSumByteAdd(const uint8_t *bytes, uint8_t len, uint32_t mask) {
     uint32_t sum = 0;
     for (uint8_t i = 0; i < len; i++) {
         sum += bytes[i];
@@ -123,7 +123,7 @@ static uint16_t calcSumByteAddOnes(uint8_t *bytes, uint8_t len, uint32_t mask) {
     return (~calcSumByteAdd(bytes, len, mask) & mask);
 }
 
-static uint16_t calcSumByteSub(uint8_t *bytes, uint8_t len, uint32_t mask) {
+static uint16_t calcSumByteSub(const uint8_t *bytes, uint8_t len, uint32_t mask) {
     uint32_t sum = 0;
     for (uint8_t i = 0; i < len; i++) {
         sum -= bytes[i];
@@ -134,7 +134,7 @@ static uint16_t calcSumByteSub(uint8_t *bytes, uint8_t len, uint32_t mask) {
 static uint16_t calcSumByteSubOnes(uint8_t *bytes, uint8_t len, uint32_t mask) {
     return (~calcSumByteSub(bytes, len, mask) & mask);
 }
-static uint16_t calcSumNibbleSub(uint8_t *bytes, uint8_t len, uint32_t mask) {
+static uint16_t calcSumNibbleSub(const uint8_t *bytes, uint8_t len, uint32_t mask) {
     uint32_t sum = 0;
     for (uint8_t i = 0; i < len; i++) {
         sum -= NIBBLE_LOW(bytes[i]);
@@ -148,7 +148,7 @@ static uint16_t calcSumNibbleSubOnes(uint8_t *bytes, uint8_t len, uint32_t mask)
 }
 
 // BSD shift checksum 8bit version
-static uint16_t calcBSDchecksum8(uint8_t *bytes, uint8_t len, uint32_t mask) {
+static uint16_t calcBSDchecksum8(const uint8_t *bytes, uint8_t len, uint32_t mask) {
     uint32_t sum = 0;
     for (uint8_t i = 0; i < len; i++) {
         sum = ((sum & 0xFF) >> 1) | ((sum & 0x1) << 7);   // rotate accumulator
@@ -159,7 +159,7 @@ static uint16_t calcBSDchecksum8(uint8_t *bytes, uint8_t len, uint32_t mask) {
     return (sum & 0xFFFF);
 }
 // BSD shift checksum 4bit version
-static uint16_t calcBSDchecksum4(uint8_t *bytes, uint8_t len, uint32_t mask) {
+static uint16_t calcBSDchecksum4(const uint8_t *bytes, uint8_t len, uint32_t mask) {
     uint32_t sum = 0;
     for (uint8_t i = 0; i < len; i++) {
         sum = ((sum & 0xF) >> 1) | ((sum & 0x1) << 3);   // rotate accumulator
diff --git a/client/src/cmddata.c b/client/src/cmddata.c
index 70bc65b44..5c70a99eb 100644
--- a/client/src/cmddata.c
+++ b/client/src/cmddata.c
@@ -36,9 +36,8 @@ int g_DemodClock = 0;
 
 static int CmdHelp(const char *Cmd);
 
-//set the g_DemodBuffer with given array ofq binary (one bit per byte)
-//by marshmellow
-void setDemodBuff(uint8_t *buff, size_t size, size_t start_idx) {
+// set the g_DemodBuffer with given array ofq binary (one bit per byte)
+void setDemodBuff(const uint8_t *buff, size_t size, size_t start_idx) {
     if (buff == NULL) return;
 
     if (size > MAX_DEMOD_BUF_LEN - start_idx)
diff --git a/client/src/cmddata.h b/client/src/cmddata.h
index ed8f076c7..4bddfcf0a 100644
--- a/client/src/cmddata.h
+++ b/client/src/cmddata.h
@@ -12,6 +12,7 @@
 #define CMDDATA_H__
 
 #include "common.h"
+#include <stdbool.h>
 
 #ifdef __cplusplus
 extern "C" {
@@ -70,7 +71,7 @@ int NRZrawDemod(int clk, int invert, int maxErr, bool verbose);
 
 int printDemodBuff(uint8_t offset, bool strip_leading, bool invert, bool print_hex);
 
-void setDemodBuff(uint8_t *buff, size_t size, size_t start_idx);
+void setDemodBuff(const uint8_t *buff, size_t size, size_t start_idx);
 bool getDemodBuff(uint8_t *buff, size_t *size);
 void save_restoreDB(uint8_t saveOpt);// option '1' to save g_DemodBuffer any other to restore
 int AutoCorrelate(const int *in, int *out, size_t len, size_t window, bool SaveGrph, bool verbose);
diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index d127010d5..6d3a89fa4 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -2234,14 +2234,6 @@ int infoHF14A4Applications(bool verbose) {
     return found;
 }
 
-static uint16_t get_sw(uint8_t *d, uint8_t n) {
-    if (n < 2) {
-        return 0;
-    }
-    n -= 2;
-    return d[n] * 0x0100 + d[n + 1];
-}
-
 static uint64_t inc_sw_error_occurrence(uint16_t sw, uint64_t all_sw[256][256]) {
     uint8_t sw1 = (uint8_t)(sw >> 8);
     uint8_t sw2 = (uint8_t)(0xff & sw);
diff --git a/client/src/cmdhf14b.c b/client/src/cmdhf14b.c
index 04e0a1a52..38aeb7f62 100644
--- a/client/src/cmdhf14b.c
+++ b/client/src/cmdhf14b.c
@@ -54,14 +54,6 @@ static int switch_off_field_14b(void) {
     return PM3_SUCCESS;
 }
 
-static uint16_t get_sw(uint8_t *d, uint8_t n) {
-    if (n < 2)
-        return 0;
-
-    n -= 2;
-    return d[n] * 0x0100 + d[n + 1];
-}
-
 static void hf14b_aid_search(bool verbose) {
 
     json_t *root = AIDSearchInit(verbose);
diff --git a/client/src/cmdhfmfp.c b/client/src/cmdhfmfp.c
index 5eb4f4f2f..d422a2c2b 100644
--- a/client/src/cmdhfmfp.c
+++ b/client/src/cmdhfmfp.c
@@ -1237,7 +1237,7 @@ static int CmdHFMFPChk(const char *Cmd) {
     }
 
     if (verbose == false)
-        PrintAndLogEx(NORMAL, "Search keys");
+        PrintAndLogEx(INFO, "Search keys");
 
     while (true) {
         res = MFPKeyCheck(startSector, endSector, startKeyAB, endKeyAB, keyList, keyListLen, foundKeys, verbose);
diff --git a/client/src/cmdhfseos.c b/client/src/cmdhfseos.c
index a28198d2f..a27bad605 100644
--- a/client/src/cmdhfseos.c
+++ b/client/src/cmdhfseos.c
@@ -19,19 +19,12 @@
 #include "ui.h"
 #include "cmdhf14a.h"           // manufacture
 #include "protocols.h"          // definitions of ISO14A/7816 protocol
-#include "iso7816/apduinfo.h"       // GetAPDUCodeDescription
+#include "iso7816/apduinfo.h"   // GetAPDUCodeDescription
 #include "crypto/asn1utils.h"   // ASN1 decode / print
+#include "commonutil.h"         // get_sw
 
 static int CmdHelp(const char *Cmd);
 
-static uint16_t get_sw(const uint8_t *d, uint8_t n) {
-    if (n < 2)
-        return 0;
-
-    n -= 2;
-    return d[n] * 0x0100 + d[n + 1];
-}
-
 static int seos_select(void) {
     bool activate_field = true;
     bool keep_field_on = true;
diff --git a/client/src/cmdhfst25ta.c b/client/src/cmdhfst25ta.c
index 83efcd365..111ea4b5f 100644
--- a/client/src/cmdhfst25ta.c
+++ b/client/src/cmdhfst25ta.c
@@ -12,16 +12,17 @@
 #include "cmdhfst.h"
 #include <ctype.h>
 #include "fileutils.h"
-#include "cmdparser.h"     // command_t
-#include "comms.h"         // clearCommandBuffer
+#include "cmdparser.h"         // command_t
+#include "comms.h"             // clearCommandBuffer
 #include "cmdtrace.h"
 #include "cliparser.h"
 #include "crc16.h"
 #include "cmdhf14a.h"
-#include "protocols.h"     // definitions of ISO14A/7816 protocol
+#include "protocols.h"         // definitions of ISO14A/7816 protocol
 #include "iso7816/apduinfo.h"  // GetAPDUCodeDescription
-#include "nfc/ndef.h"      // NDEFRecordsDecodeAndPrint
-#include "cmdnfc.h"        // print_type4_cc_info
+#include "nfc/ndef.h"          // NDEFRecordsDecodeAndPrint
+#include "cmdnfc.h"            // print_type4_cc_info
+#include "commonutil.h"        // get_sw
 
 #define TIMEOUT 2000
 
@@ -103,14 +104,6 @@ static void print_st25ta_system_info(uint8_t *d, uint8_t n) {
     */
 }
 
-static uint16_t get_sw(const uint8_t *d, uint8_t n) {
-    if (n < 2)
-        return 0;
-
-    n -= 2;
-    return d[n] * 0x0100 + d[n + 1];
-}
-
 // ST25TA
 static int infoHFST25TA(void) {
 
diff --git a/common/commonutil.c b/common/commonutil.c
index 1eb62b826..e460014d7 100644
--- a/common/commonutil.c
+++ b/common/commonutil.c
@@ -230,3 +230,11 @@ uint32_t rotr(uint32_t a, uint8_t n) {
     n &= 31;
     return (a >> n) | (a << (32 - n));
 }
+
+uint16_t get_sw(const uint8_t *d, uint8_t n) {
+    if (n < 2)
+        return 0;
+
+    n -= 2;
+    return d[n] * 0x0100 + d[n + 1];
+}
\ No newline at end of file
diff --git a/common/commonutil.h b/common/commonutil.h
index 7b548bd15..1a859e6fb 100644
--- a/common/commonutil.h
+++ b/common/commonutil.h
@@ -75,4 +75,6 @@ void htole24(uint32_t val, uint8_t data[3]);
 // rol on a u32
 uint32_t rotl(uint32_t a, uint8_t n);
 uint32_t rotr(uint32_t a, uint8_t n);
+
+uint16_t get_sw(const uint8_t *d, uint8_t n);
 #endif
