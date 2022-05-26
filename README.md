commit cdf079a0c1ead7000df804e47fbe8f6b71e11f5d
Author: iceman1001 <iceman@iuse.se>
Date:   Wed Mar 23 23:03:23 2022 +0100

    less false positives with indala.  Check ratio of zeros in demod.

diff --git a/client/src/cmdlf.c b/client/src/cmdlf.c
index e8b14e611..e18dd15b7 100644
--- a/client/src/cmdlf.c
+++ b/client/src/cmdlf.c
@@ -1506,7 +1506,7 @@ int CmdLFfind(const char *Cmd) {
                 }
             }
 
-            PrintAndLogEx(INPLACE, "Searching for COTAG tag...    ");
+            PrintAndLogEx(INPLACE, "Searching for COTAG tag...");
             if (readCOTAGUid()) {
                 PrintAndLogEx(SUCCESS, "\nValid " _GREEN_("COTAG ID") " found!");
                 if (search_cont) {
@@ -1518,9 +1518,9 @@ int CmdLFfind(const char *Cmd) {
 
             PrintAndLogEx(NORMAL, "");
             PrintAndLogEx(FAILED, _RED_("No data found!"));
-            PrintAndLogEx(INFO, "Signal looks like noise. Maybe not an LF tag?");
+            PrintAndLogEx(HINT, "Maybe not an LF tag?");
             PrintAndLogEx(NORMAL, "");
-            if (! search_cont) {
+            if (search_cont == 0) {
                 return PM3_ESOFT;
             }
         }
diff --git a/client/src/cmdlfindala.c b/client/src/cmdlfindala.c
index 0a8a9bb16..a2bdddc9c 100644
--- a/client/src/cmdlfindala.c
+++ b/client/src/cmdlfindala.c
@@ -159,6 +159,20 @@ int demodIndalaEx(int clk, int invert, int maxErr, bool verbose) {
     //uint64_t foo = (((uint64_t)uid1 << 32) & 0x1FFFFFFF) | (uid2 & 0x7FFFFFFF);
     uint64_t foo = uid2 & 0x7FFFFFFF;
 
+    // to reduce false_positives
+    // let's check the ratio of zeros in the demod buffer.
+    size_t cnt_zeros = 0;
+    for (size_t i=0; i< g_DemodBufferLen; i++) {
+        if (g_DemodBuffer[i] == 0x00)
+            ++cnt_zeros;
+    }
+
+    // if more than 95% zeros in the demodbuffer then assume its wrong
+    int32_t stats = (int32_t)((cnt_zeros * 100 / g_DemodBufferLen));
+    if ( stats > 95) {
+        return PM3_ESOFT;
+    }
+
     if (g_DemodBufferLen == 64) {
         PrintAndLogEx(SUCCESS, "Indala (len %zu)  Raw: " _GREEN_("%x%08x"), g_DemodBufferLen, uid1, uid2);
 
@@ -226,6 +240,11 @@ int demodIndalaEx(int clk, int invert, int maxErr, bool verbose) {
         decodeHeden2L(g_DemodBuffer);
 
     } else {
+
+        if (g_DemodBufferLen != 224) {
+            PrintAndLogEx(INFO, "Odd size,  false positive?");
+        }
+
         uint32_t uid3 = bytebits_to_byte(g_DemodBuffer + 64, 32);
         uint32_t uid4 = bytebits_to_byte(g_DemodBuffer + 96, 32);
         uint32_t uid5 = bytebits_to_byte(g_DemodBuffer + 128, 32);
