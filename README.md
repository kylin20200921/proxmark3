commit 9d1e75a9b5dba2052b2874af17c718e94592ecb1
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Mar 27 10:48:18 2022 +0200

    --verbose now also prints the keys

diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index 315c5c775..81463e3ed 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -245,7 +245,7 @@ static void mf_print_block(uint8_t blockno, uint8_t *d, bool verbose) {
 
         uint32_t value = 0;
         if (verbose && mfc_value(d, &value)) {
-            PrintAndLogEx(INFO, "%3d | " _CYAN_("%s") " %"PRIu32 , blockno, sprint_hex_ascii(d, MFBLOCK_SIZE), value);
+            PrintAndLogEx(INFO, "%3d | " _CYAN_("%s") " %"PRIu32, blockno, sprint_hex_ascii(d, MFBLOCK_SIZE), value);
         } else {
             PrintAndLogEx(INFO, "%3d | %s ", blockno, sprint_hex_ascii(d, MFBLOCK_SIZE));
         }
@@ -4181,6 +4181,10 @@ static int CmdHF14AMfEView(const char *Cmd) {
     }
 
     mf_print_blocks(block_cnt, dump, verbose);
+
+    if (verbose) {
+        mf_print_keys(block_cnt, dump);
+    }
     free(dump);
     return PM3_SUCCESS;
 }
@@ -5067,6 +5071,11 @@ static int CmdHF14AMfCView(const char *Cmd) {
 
     PrintAndLogEx(NORMAL, "");
     mf_print_blocks(block_cnt, dump, verbose);
+
+    if (verbose) {
+        mf_print_keys(block_cnt, dump);
+    }
+
     free(dump);
     return PM3_SUCCESS;
 }
@@ -6309,6 +6318,11 @@ static int CmdHF14AGen4View(const char *Cmd) {
 
     PrintAndLogEx(NORMAL, "");
     mf_print_blocks(block_cnt, dump, verbose);
+
+    if (verbose) {
+        mf_print_keys(block_cnt, dump);
+    }
+
     free(dump);
     return PM3_SUCCESS;
 }
