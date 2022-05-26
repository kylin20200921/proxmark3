commit b9baae4b32426aebef652519bec717315dfa6fbd
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Mar 26 20:00:01 2022 +0100

    --verbose flag now also prints a found value block in cyan color and the decoded value

diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index 8c873d122..315c5c775 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -236,25 +236,32 @@ static bool mfc_value(const uint8_t *d, uint32_t *val) {
     }
     return (val_checks);
 }
-static void mf_print_block(uint8_t blockno, uint8_t *d) {
+static void mf_print_block(uint8_t blockno, uint8_t *d, bool verbose) {
     if (blockno == 0) {
         PrintAndLogEx(INFO, "%3d | " _RED_("%s"), blockno, sprint_hex_ascii(d, MFBLOCK_SIZE));
     } else if (mfIsSectorTrailer(blockno)) {
         PrintAndLogEx(INFO, "%3d | " _YELLOW_("%s"), blockno, sprint_hex_ascii(d, MFBLOCK_SIZE));
     } else {
-        PrintAndLogEx(INFO, "%3d | %s ", blockno, sprint_hex_ascii(d, MFBLOCK_SIZE));
+
+        uint32_t value = 0;
+        if (verbose && mfc_value(d, &value)) {
+            PrintAndLogEx(INFO, "%3d | " _CYAN_("%s") " %"PRIu32 , blockno, sprint_hex_ascii(d, MFBLOCK_SIZE), value);
+        } else {
+            PrintAndLogEx(INFO, "%3d | %s ", blockno, sprint_hex_ascii(d, MFBLOCK_SIZE));
+        }
     }
 }
 
-static void mf_print_blocks(uint16_t n, uint8_t *d) {
+static void mf_print_blocks(uint16_t n, uint8_t *d, bool verbose) {
     PrintAndLogEx(NORMAL, "");
     PrintAndLogEx(INFO, "----+-------------------------------------------------+-----------------");
     PrintAndLogEx(INFO, "blk | data                                            | ascii");
     PrintAndLogEx(INFO, "----+-------------------------------------------------+-----------------");
     for (uint16_t i = 0; i < n; i++) {
-        mf_print_block(i, d + (i * MFBLOCK_SIZE));
+        mf_print_block(i, d + (i * MFBLOCK_SIZE), verbose);
     }
     PrintAndLogEx(INFO, "----+-------------------------------------------------+-----------------");
+    PrintAndLogEx(HINT, _CYAN_("cyan") " = value block with decoded value");
     PrintAndLogEx(NORMAL, "");
 }
 
@@ -295,6 +302,7 @@ static int mf_print_keys(uint16_t n, uint8_t *d) {
     return PM3_SUCCESS;
 }
 
+/*
 static void mf_print_values(uint16_t n, uint8_t *d) {
 
     PrintAndLogEx(NORMAL, "");
@@ -315,6 +323,7 @@ static void mf_print_values(uint16_t n, uint8_t *d) {
         PrintAndLogEx(NORMAL, "");
     }
 }
+*/
 
 static void mf_print_sector_hdr(uint8_t sector) {
     PrintAndLogEx(NORMAL, "");
@@ -561,7 +570,7 @@ static int CmdHF14AMfRdBl(const char *Cmd) {
 
         uint8_t sector = mfSectorNum(blockno);
         mf_print_sector_hdr(sector);
-        mf_print_block(blockno, data);
+        mf_print_block(blockno, data, verbose);
         if (verbose) {
             decode_print_st(blockno, data);
         }
@@ -624,7 +633,7 @@ static int CmdHF14AMfRdSc(const char *Cmd) {
 
         mf_print_sector_hdr(sector);
         for (int i = 0; i < blocks; i++) {
-            mf_print_block(start + i, data + (i * MFBLOCK_SIZE));
+            mf_print_block(start + i, data + (i * MFBLOCK_SIZE), verbose);
         }
 
         if (verbose) {
@@ -3721,7 +3730,7 @@ static int CmdHF14AMfEGetBlk(const char *Cmd) {
 
         uint8_t sector = mfSectorNum(blockno);
         mf_print_sector_hdr(sector);
-        mf_print_block(blockno, data);
+        mf_print_block(blockno, data, verbose);
     }
     if (verbose) {
         decode_print_st(blockno, data);
@@ -3763,7 +3772,7 @@ static int CmdHF14AMfEGetSc(const char *Cmd) {
     for (int i = 0; i < blocks; i++) {
         int res = mfEmlGetMem(data, start + i, 1);
         if (res == PM3_SUCCESS) {
-            mf_print_block(start + i, data);
+            mf_print_block(start + i, data, verbose);
         }
     }
     if (verbose) {
@@ -4122,6 +4131,7 @@ static int CmdHF14AMfEView(const char *Cmd) {
         arg_lit0(NULL, "1k", "MIFARE Classic 1k / S50 (def)"),
         arg_lit0(NULL, "2k", "MIFARE Classic/Plus 2k"),
         arg_lit0(NULL, "4k", "MIFARE Classic 4k / S70"),
+        arg_lit0("v", "verbose", "verbose output"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -4129,6 +4139,7 @@ static int CmdHF14AMfEView(const char *Cmd) {
     bool m1 = arg_get_lit(ctx, 2);
     bool m2 = arg_get_lit(ctx, 3);
     bool m4 = arg_get_lit(ctx, 4);
+    bool verbose = arg_get_lit(ctx, 5);
     CLIParserFree(ctx);
 
     // validations
@@ -4169,7 +4180,7 @@ static int CmdHF14AMfEView(const char *Cmd) {
         return PM3_ETIMEOUT;
     }
 
-    mf_print_blocks(block_cnt, dump);
+    mf_print_blocks(block_cnt, dump, verbose);
     free(dump);
     return PM3_SUCCESS;
 }
@@ -4724,7 +4735,7 @@ static int CmdHF14AMfCGetBlk(const char *Cmd) {
 
     uint8_t sector = mfSectorNum(blockno);
     mf_print_sector_hdr(sector);
-    mf_print_block(blockno, data);
+    mf_print_block(blockno, data, verbose);
 
     if (verbose) {
         decode_print_st(blockno, data);
@@ -4777,7 +4788,7 @@ static int CmdHF14AMfCGetSc(const char *Cmd) {
             PrintAndLogEx(ERR, "Can't read block. %d error=%d", start + i, res);
             return PM3_ESOFT;
         }
-        mf_print_block(start + i, data);
+        mf_print_block(start + i, data, verbose);
     }
     if (verbose) {
         decode_print_st(start + blocks - 1, data);
@@ -4958,6 +4969,7 @@ static int CmdHF14AMfCView(const char *Cmd) {
         arg_lit0(NULL, "1k", "MIFARE Classic 1k / S50 (def)"),
         arg_lit0(NULL, "2k", "MIFARE Classic/Plus 2k"),
         arg_lit0(NULL, "4k", "MIFARE Classic 4k / S70"),
+        arg_lit0("v", "verbose", "verbose output"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -4965,6 +4977,7 @@ static int CmdHF14AMfCView(const char *Cmd) {
     bool m1 = arg_get_lit(ctx, 2);
     bool m2 = arg_get_lit(ctx, 3);
     bool m4 = arg_get_lit(ctx, 4);
+    bool verbose = arg_get_lit(ctx, 5);
     CLIParserFree(ctx);
 
     // validations
@@ -5053,7 +5066,7 @@ static int CmdHF14AMfCView(const char *Cmd) {
     }
 
     PrintAndLogEx(NORMAL, "");
-    mf_print_blocks(block_cnt, dump);
+    mf_print_blocks(block_cnt, dump, verbose);
     free(dump);
     return PM3_SUCCESS;
 }
@@ -6173,11 +6186,10 @@ static int CmdHF14AMfView(const char *Cmd) {
         PrintAndLogEx(INFO, "File size %zu bytes, file blocks %d (0x%x)", bytes_read, block_cnt, block_cnt);
     }
 
-    mf_print_blocks(block_cnt, dump);
+    mf_print_blocks(block_cnt, dump, verbose);
 
     if (verbose) {
         mf_print_keys(block_cnt, dump);
-        mf_print_values(block_cnt, dump);
     }
 
     free(dump);
@@ -6199,6 +6211,7 @@ static int CmdHF14AGen4View(const char *Cmd) {
         arg_lit0(NULL, "2k", "MIFARE Classic/Plus 2k"),
         arg_lit0(NULL, "4k", "MIFARE Classic 4k / S70"),
         arg_str0("p", "pwd", "<hex>", "password 4bytes"),
+        arg_lit0("v", "verbose", "verbose output"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -6210,6 +6223,8 @@ static int CmdHF14AGen4View(const char *Cmd) {
     int pwd_len = 0;
     uint8_t pwd[4] = {0};
     CLIGetHexWithReturn(ctx, 5, pwd, &pwd_len);
+
+    bool verbose = arg_get_lit(ctx, 6);
     CLIParserFree(ctx);
 
     // validations
@@ -6293,7 +6308,7 @@ static int CmdHF14AGen4View(const char *Cmd) {
     }
 
     PrintAndLogEx(NORMAL, "");
-    mf_print_blocks(block_cnt, dump);
+    mf_print_blocks(block_cnt, dump, verbose);
     free(dump);
     return PM3_SUCCESS;
 }
