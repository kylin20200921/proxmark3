commit 159d5ffd1a0a070469cbf151a7074ff2e274ba04
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Apr 9 19:04:31 2022 +0200

    fix #1661  -  hf sniff shall ignore lf config settings

diff --git a/client/src/cmddata.c b/client/src/cmddata.c
index 81ab70c87..bfd4b9da0 100644
--- a/client/src/cmddata.c
+++ b/client/src/cmddata.c
@@ -1731,17 +1731,17 @@ static uint8_t getByte(uint8_t bits_per_sample, BitstreamOut_t *b) {
 }
 
 int getSamples(uint32_t n, bool verbose) {
-    return getSamplesEx(0, n, verbose);
+    return getSamplesEx(0, n, verbose, false);
 }
 
-int getSamplesEx(uint32_t start, uint32_t end, bool verbose) {
+int getSamplesEx(uint32_t start, uint32_t end, bool verbose, bool ignore_lf_config) {
 
     if (end < start) {
         PrintAndLogEx(WARNING, "error, end (%u) is smaller than start (%u)", end, start);
         return PM3_EINVARG;
     }
 
-    //If we get all but the last byte in bigbuf,
+    // If we get all but the last byte in bigbuf,
     // we don't have to worry about remaining trash
     // in the last byte in case the bits-per-sample
     // does not line up on byte boundaries
@@ -1766,8 +1766,8 @@ int getSamplesEx(uint32_t start, uint32_t end, bool verbose) {
 
     uint8_t bits_per_sample = 8;
 
-    //Old devices without this feature would send 0 at arg[0]
-    if (response.oldarg[0] > 0) {
+    // Old devices without this feature would send 0 at arg[0]
+    if (response.oldarg[0] > 0 && (ignore_lf_config == false)) {
         sample_config *sc = (sample_config *) response.data.asBytes;
         if (verbose) PrintAndLogEx(INFO, "Samples @ " _YELLOW_("%d") " bits/smpl, decimation 1:%d ", sc->bits_per_sample, sc->decimation);
         bits_per_sample = sc->bits_per_sample;
diff --git a/client/src/cmddata.h b/client/src/cmddata.h
index bcf8afac1..aaeae864a 100644
--- a/client/src/cmddata.h
+++ b/client/src/cmddata.h
@@ -85,7 +85,7 @@ void save_restoreDB(uint8_t saveOpt);// option '1' to save g_DemodBuffer any oth
 int AutoCorrelate(const int *in, int *out, size_t len, size_t window, bool SaveGrph, bool verbose);
 
 int getSamples(uint32_t n, bool verbose);
-int getSamplesEx(uint32_t start, uint32_t end, bool verbose);
+int getSamplesEx(uint32_t start, uint32_t end, bool verbose, bool ignore_lf_config);
 
 void setClockGrid(uint32_t clk, int offset);
 int directionalThreshold(const int *in, int *out, size_t len, int8_t up, int8_t down);
diff --git a/client/src/cmdhf.c b/client/src/cmdhf.c
index 894fddb4c..ccb697966 100644
--- a/client/src/cmdhf.c
+++ b/client/src/cmdhf.c
@@ -348,7 +348,7 @@ int CmdHFSniff(const char *Cmd) {
                 // At the moment, sniff takes all free memory in bigbuff. If this changes,
                 // we can't start from beginning idx 0 but from that hi-to-start-of-allocated.
                 uint32_t start = g_pm3_capabilities.bigbuf_size - retval->len;
-                int res = getSamplesEx(start, start, false);
+                int res = getSamplesEx(start, start, false, true);
                 if (res != PM3_SUCCESS) {
                     PrintAndLogEx(WARNING, "failed to download samples to client");
                     return res;
