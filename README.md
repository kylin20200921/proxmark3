commit f9ac27c2230f35b1375644dc0c5be22585487dd5
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Dec 31 09:27:26 2021 +0100

    text

diff --git a/CHANGELOG.md b/CHANGELOG.md
index 1659bea3c..34734520d 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,7 +3,8 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
- - Changed `hf mf gview` - now supports user supplied password (@iceman1001)
+ - Added option `lf gprox demod --raw` - now supports decoding of raw bytes (@iceman1001)
+ - Added option `hf mf gview --pwd` - now supports user supplied password (@iceman1001)
  - Added option `--force` to `hf mfu rdb/wrbl` to force operation even if address is out of range (@doegox)
  - Added documentation for detailed usage of the Ultimate Magic Card (@doegox)
  - Changed HitagS trace record and parsing to deal with partial bytes and to check CRC8 (@doegox)
diff --git a/client/src/cmdlfguard.c b/client/src/cmdlfguard.c
index df2f430a5..0f258f3d1 100644
--- a/client/src/cmdlfguard.c
+++ b/client/src/cmdlfguard.c
@@ -27,6 +27,64 @@
 
 static int CmdHelp(const char *Cmd);
 
+static int demod_guard_raw(uint8_t* raw, uint8_t rlen) {
+
+    if (rlen != 12) {
+        return PM3_EINVARG;
+    }
+
+    uint8_t bits[96] = {0x00};
+    bytes_to_bytebits(raw, rlen, bits);
+
+    // start after 6 bit preamble
+    size_t start_idx = 6;
+    uint8_t bits_no_spacer[90];
+    memcpy(bits_no_spacer, bits + start_idx, 90);
+
+    // remove the 18 (90/5=18) parity bits (down to 72 bits (96-6-18=72))
+    size_t len = removeParity(bits_no_spacer, 0, 5, 3, 90); // source, startloc, paritylen, ptype, length_to_run
+    if (len != 72) {
+        PrintAndLogEx(DEBUG, "DEBUG: Error - gProxII spacer removal did not produce 72 bits: %zu, start: %zu", len, start_idx);
+        return PM3_ESOFT;
+    }
+
+    uint8_t plain[8] = {0x00};
+
+    // get key and then get all 8 bytes of payload decoded
+    uint8_t xorKey = (uint8_t)bytebits_to_byteLSBF(bits_no_spacer, 8);
+    for (size_t idx = 0; idx < 8; idx++) {
+        plain[idx] = ((uint8_t)bytebits_to_byteLSBF(bits_no_spacer + 8 + (idx * 8), 8)) ^ xorKey;
+        PrintAndLogEx(DEBUG, "DEBUG: gProxII byte %zu after xor: %02x", idx, plain[idx]);
+    }
+
+    // plain contains 8 Bytes (64 bits) of decrypted raw tag data
+    uint8_t fmtlen = plain[0] >> 2;
+    uint32_t FC = 0;
+    uint32_t Card = 0;
+
+    bool unknown = false;
+    switch (fmtlen) {
+        case 36:
+            FC = ((plain[3] & 0x7F) << 7) | (plain[4] >> 1);
+            Card = ((plain[4] & 1) << 19) | (plain[5] << 11) | (plain[6] << 3) | ((plain[7] & 0xE0) >> 5);
+            break;
+        case 26:
+            FC = ((plain[3] & 0x7F) << 1) | (plain[4] >> 7);
+            Card = ((plain[4] & 0x7F) << 9) | (plain[5] << 1) | (plain[6] >> 7);
+            break;
+        default :
+            unknown = true;
+            break;
+    }
+
+    if (unknown)
+        PrintAndLogEx(SUCCESS, "G-Prox-II - Unknown len: " _GREEN_("%u") ", Raw: %s", fmtlen, sprint_hex_inrow(raw, rlen));
+    else
+        PrintAndLogEx(SUCCESS, "G-Prox-II - len: " _GREEN_("%u")" FC: " _GREEN_("%u") " Card: " _GREEN_("%u") ", Raw: %s", fmtlen, FC, Card,  sprint_hex_inrow(raw, rlen));
+
+    return PM3_SUCCESS;
+}
+
 // attempts to demodulate and identify a G_Prox_II verex/chubb card
 // WARNING: if it fails during some points it will destroy the g_DemodBuffer data
 // but will leave the g_GraphBuffer intact.
@@ -59,7 +117,6 @@ int demodGuard(bool verbose) {
     }
 
     // got a good demod of 96 bits
-
     uint8_t plain[8] = {0x00};
     uint8_t xorKey = 0;
     size_t startIdx = preambleIndex + 6; //start after 6 bit preamble
@@ -119,16 +176,32 @@ static int CmdGuardDemod(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "lf gproxii demod",
                   "Try to find Guardall Prox-II preamble, if found decode / descramble data",
-                  "lf gproxii demod"
+                  "lf gproxii demod -> use graphbuffer to decode\n"
+                  "lf gproxii demod --raw fb8ee718ee3b8cc785c11b92   ->"
                  );
 
     void *argtable[] = {
         arg_param_begin,
+        arg_strx0("r", "raw", "<hex>", "raw bytes"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
+
+    int raw_len = 0;
+    uint8_t raw[12] = {0};
+    CLIGetHexWithReturn(ctx, 1, raw, &raw_len);
+
     CLIParserFree(ctx);
-    return demodGuard(true);
+
+    if (raw_len != 12 && raw_len != 0) {
+        PrintAndLogEx(FAILED, "Must specify 12 bytes, got " _YELLOW_("%u"), raw_len);
+        return PM3_EINVARG;
+    }
+
+    if (raw_len == 0)
+        return demodGuard(true);
+    else
+        return demod_guard_raw(raw, raw_len);
 }
 
 static int CmdGuardReader(const char *Cmd) {
