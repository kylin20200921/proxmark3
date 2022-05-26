commit e174a1e1bef9c3fbf6cf1aad0039ab84652f8bf6
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Feb 12 17:30:30 2022 +0100

    hf mfu restore/eload/eview/view  now handles  bin/eml/json.   Only hf mfu dump that doesnt save EML yet

diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index 8fe003c4c..dd4636b09 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -3876,8 +3876,8 @@ int CmdHF14AMfELoad(const char *Cmd) {
     } else if (m4) {
         block_cnt = MIFARE_4K_MAXBLOCK;
     } else if (mu) {
-        block_cnt = 255;
-        block_width = 4;
+        block_cnt = MFU_MAX_BLOCKS;
+        block_width = MFU_BLOCK_SIZE;
     } else {
         PrintAndLogEx(WARNING, "Please specify a MIFARE Type");
         return PM3_EINVARG;
@@ -3932,7 +3932,7 @@ int CmdHF14AMfELoad(const char *Cmd) {
     }
 
     // convert plain or old mfu format to new format
-    if (block_width == 4) {
+    if (block_width == MFU_BLOCK_SIZE) {
         res = convert_mfu_dump_format(&data, &datalen, true);
         if (res != PM3_SUCCESS) {
             PrintAndLogEx(FAILED, "Failed convert on load to new Ultralight/NTAG format");
@@ -3944,7 +3944,7 @@ int CmdHF14AMfELoad(const char *Cmd) {
         printMFUdumpEx(mfu_dump, mfu_dump->pages + 1, 0);
 
         // update expected blocks to match converted data.
-        block_cnt = datalen / 4;
+        block_cnt = datalen / MFU_BLOCK_SIZE;
         PrintAndLogEx(INFO, "MIFARE Ultralight override, will use %d blocks ( %u bytes )", block_cnt, block_cnt * block_width);
     }
 
@@ -3978,7 +3978,7 @@ int CmdHF14AMfELoad(const char *Cmd) {
     free(data);
     PrintAndLogEx(NORMAL, "");
 
-    if (block_width == 4) {
+    if (block_width == MFU_BLOCK_SIZE) {
         PrintAndLogEx(HINT, "You are ready to simulate. See " _YELLOW_("`hf mfu sim -h`"));
         // MFU / NTAG
         if ((cnt != block_cnt)) {
diff --git a/client/src/cmdhfmfu.c b/client/src/cmdhfmfu.c
index 7ecfd914c..25d9269c6 100644
--- a/client/src/cmdhfmfu.c
+++ b/client/src/cmdhfmfu.c
@@ -2486,7 +2486,7 @@ static int CmdHF14AMfURestore(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str1("f", "file", "<fn>", "specify dump filename (bin/json)"),
+        arg_str1("f", "file", "<fn>", "specify dump filename (bin/eml/json)"),
         arg_str0("k", "key", "<hex>", "key for authentication (UL-C 16 bytes, EV1/NTAG 4 bytes)"),
         arg_lit0("l", NULL, "swap entered key's endianness"),
         arg_lit0("s", NULL, "enable special write UID -MAGIC TAG ONLY-"),
@@ -2544,6 +2544,10 @@ static int CmdHF14AMfURestore(const char *Cmd) {
             res = loadFile_safe(filename, ".bin", (void **)&dump, &bytes_read);
             break;
         }
+        case EML: {
+            res = loadFileEML_safe(filename, (void **)&dump, &bytes_read);
+            break;
+        }
         case JSON: {
             dump = calloc(MFU_MAX_BYTES + MFU_DUMP_PREFIX_LENGTH, sizeof(uint8_t));
             if (dump == NULL) {
@@ -2553,7 +2557,6 @@ static int CmdHF14AMfURestore(const char *Cmd) {
             res = loadFileJSON(filename, (void *)dump, MFU_MAX_BYTES + MFU_DUMP_PREFIX_LENGTH, &bytes_read, NULL);
             break;
         }
-        case EML:
         case DICTIONARY: {
             PrintAndLogEx(ERR, "Error: Only BIN/JSON formats allowed");
             free(dump);
@@ -2595,8 +2598,6 @@ static int CmdHF14AMfURestore(const char *Cmd) {
     // print dump
     printMFUdumpEx(mem, pages, 0);
 
-    return PM3_SUCCESS;
-
     // Swap endianness
     if (swap_endian && has_key) {
         if (ak_len == 16)
@@ -2723,22 +2724,30 @@ static int CmdHF14AMfUeLoad(const char *Cmd) {
 
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfu eload",
-                  "Load emulator memory with data from `filename.eml` dump file\n"
-                  "\nSee `script run data_mfu_bin2eml` to convert the .bin to .eml",
-                  "hf mfu eload --ul -f hf-mfu-04010203040506.eml\n"
-                  "hf mfu eload --ul -f hf-mfu-04010203040506.eml -q 57   -> load 57 blocks from myfile"
+                  "Load emulator memory with data from (bin/eml/json) dump file\n",
+                  "hf mfu eload -f hf-mfu-04010203040506.bin\n"
+                  "hf mfu eload -f hf-mfu-04010203040506.bin -q 57   -> load 57 blocks from myfile"
                  );
 
     void *argtable[] = {
         arg_param_begin,
         arg_str1("f", "file", "<fn>", "Filename of dump"),
-        arg_lit1(NULL, "ul", "MIFARE Ultralight family"),
         arg_int0("q", "qty", "<dec>", "Number of blocks to load from eml file"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
     CLIParserFree(ctx);
-    return CmdHF14AMfELoad(Cmd);
+
+    char *nc = calloc(strlen(Cmd) + 6, 1);
+    if (nc == NULL) {
+        return CmdHF14AMfELoad(Cmd);
+    }
+
+    sprintf(nc, "%s --ul", Cmd);
+    int res = CmdHF14AMfELoad(nc);
+    free(nc);
+
+    return res;
 }
 //
 //  Simulate tag
@@ -4142,15 +4151,7 @@ static int CmdHF14AMfuEView(const char *Cmd) {
         return PM3_ETIMEOUT;
     }
 
-    PrintAndLogEx(NORMAL, "");
-    PrintAndLogEx(INFO, "----+-------------+-------");
-    PrintAndLogEx(INFO, "blk | data        | ascii");
-    PrintAndLogEx(INFO, "----+-------------+-------");
-    for (uint16_t i = 0; i < blocks; i++) {
-        PrintAndLogEx(INFO, "%03d | %s ", i, sprint_hex_ascii(dump + (i * 4), 4));
-    }
-    PrintAndLogEx(INFO, "----+-------------+-------");
-    PrintAndLogEx(NORMAL, "");
+    printMFUdumpEx( (mfu_dump_t *)dump, blocks, 0);
     free(dump);
     return PM3_SUCCESS;
 }
@@ -4159,7 +4160,7 @@ static int CmdHF14AMfuView(const char *Cmd) {
 
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfu view",
-                  "Print a MIFARE Ultralight/NTAG dump file (bin/json)",
+                  "Print a MIFARE Ultralight/NTAG dump file (bin/eml/json)",
                   "hf mfu view -f hf-mfu-01020304-dump.bin"
                  );
     void *argtable[] = {
@@ -4195,6 +4196,8 @@ static int CmdHF14AMfuView(const char *Cmd) {
             break;
         }
         case EML:
+            res = loadFileEML_safe(filename, (void **)&dump, &bytes_read);
+            break;
         case DICTIONARY: {
             PrintAndLogEx(ERR, "Error: Only BIN/JSON formats allowed");
             free(dump);
