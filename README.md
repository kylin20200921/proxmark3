commit d7459d47b8c2907ed594df9f319c4a260dfcddc2
Author: iceman1001 <iceman@iuse.se>
Date:   Sat May 14 15:26:48 2022 +0200

    fix value block decoding. Its signed.  Thanks @mwalker33

diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index ab11f3415..d7ef53eb8 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -219,9 +219,9 @@ static char GetFormatFromSector(uint8_t sectors) {
     }
 }
 
-static bool mfc_value(const uint8_t *d, uint32_t *val) {
+static bool mfc_value(const uint8_t *d, int32_t *val) {
     // values
-    uint32_t a = MemLeToUint4byte(d);
+    int32_t a = (int32_t)MemLeToUint4byte(d);
     uint32_t a_inv = MemLeToUint4byte(d + 4);
     uint32_t b = MemLeToUint4byte(d + 8);
 
@@ -243,9 +243,9 @@ static void mf_print_block(uint8_t blockno, uint8_t *d, bool verbose) {
         PrintAndLogEx(INFO, "%3d | " _YELLOW_("%s"), blockno, sprint_hex_ascii(d, MFBLOCK_SIZE));
     } else {
 
-        uint32_t value = 0;
+        int32_t value = 0;
         if (verbose && mfc_value(d, &value)) {
-            PrintAndLogEx(INFO, "%3d | " _CYAN_("%s") " %"PRIu32, blockno, sprint_hex_ascii(d, MFBLOCK_SIZE), value);
+            PrintAndLogEx(INFO, "%3d | " _CYAN_("%s") " %"PRIi32, blockno, sprint_hex_ascii(d, MFBLOCK_SIZE), value);
         } else {
             PrintAndLogEx(INFO, "%3d | %s ", blockno, sprint_hex_ascii(d, MFBLOCK_SIZE));
         }
@@ -309,11 +309,11 @@ static void mf_print_values(uint16_t n, uint8_t *d) {
     PrintAndLogEx(INFO, "Looking for value blocks...");
     PrintAndLogEx(NORMAL, "");
     uint8_t cnt = 0;
-    uint32_t value = 0;
+    int32_t value = 0;
     for (uint16_t i = 0; i < n; i++) {
 
         if (mfc_value(d + (i * MFBLOCK_SIZE), &value))  {
-            PrintAndLogEx(INFO, "%03d | " _YELLOW_("%" PRIu32) " " _YELLOW_("0x%" PRIX32), i, value, value);
+            PrintAndLogEx(INFO, "%03d | " _YELLOW_("%" PRIi32) " " _YELLOW_("0x%" PRIX32), i, value, value);
             ++cnt;
         }
     }
@@ -6370,10 +6370,9 @@ static int CmdHF14AMfValue(const char *Cmd) {
     CLIGetHexWithReturn(ctx, 1, data, &dlen);
     CLIParserFree(ctx);
 
-    uint32_t value = 0;
-
+    int32_t value = 0;
     if (mfc_value(data, &value))  {
-        PrintAndLogEx(SUCCESS, "Dec... " _YELLOW_("%" PRIu32), value);
+        PrintAndLogEx(SUCCESS, "Dec... " _YELLOW_("%" PRIi32), value);
         PrintAndLogEx(SUCCESS, "Hex... " _YELLOW_("0x%" PRIX32), value);
     } else {
         PrintAndLogEx(FAILED, "No value block detected");
