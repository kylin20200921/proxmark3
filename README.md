commit e2e06ab17a566feb390aa77833c51d1517312ef0
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Feb 12 12:59:42 2022 +0100

    hf mfu restore now takes bin/json files.  and textual adaptations

diff --git a/client/src/cmdhfmfu.c b/client/src/cmdhfmfu.c
index e62628253..7ecfd914c 100644
--- a/client/src/cmdhfmfu.c
+++ b/client/src/cmdhfmfu.c
@@ -52,10 +52,6 @@
 #define MAX_MY_D_MOVE_LEAN  0x0F
 #define MAX_UL_NANO_40      0x0A
 
-#define MFU_BLOCK_SIZE      0x04
-#define MFU_MAX_BLOCKS      0xFF
-#define MFU_MAX_BYTES       (MFU_MAX_BLOCKS * MFU_BLOCK_SIZE)
-
 static int CmdHelp(const char *Cmd);
 
 static uint8_t default_3des_keys[][16] = {
@@ -1529,8 +1525,8 @@ static int CmdHF14AMfUInfo(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str0("k", "key", "<hex>", "key for authentication (UL-C 16 bytes, EV1/NTAG 4 bytes)"),
-        arg_lit0("l", NULL, "swap entered key's endianness"),
+        arg_str0("k", "key", "<hex>", "Authentication key (UL-C 16 bytes, EV1/NTAG 4 bytes)"),
+        arg_lit0("l", NULL, "Swap entered key's endianness"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -1832,11 +1828,11 @@ static int CmdHF14AMfUWrBl(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str0("k", "key", "<hex>", "key for authentication (UL-C 16 bytes, EV1/NTAG 4 bytes)"),
-        arg_lit0("l", NULL, "swap entered key's endianness"),
-        arg_int1("b", "block", "<dec>", "block number to write"),
-        arg_str1("d", "data", "<hex>", "block data (4 or 16 hex bytes, 16 hex bytes will do a compatibility write)"),
-        arg_lit0(NULL, "force", "force operation even if address is out of range"),
+        arg_str0("k", "key", "<hex>", "Authentication key (UL-C 16 bytes, EV1/NTAG 4 bytes)"),
+        arg_lit0("l", NULL, "Swap entered key's endianness"),
+        arg_int1("b", "block", "<dec>", "Block number to write"),
+        arg_str1("d", "data", "<hex>", "Block data (4 or 16 hex bytes, 16 hex bytes will do a compatibility write)"),
+        arg_lit0(NULL, "force", "Force operation even if address is out of range"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -1964,10 +1960,10 @@ static int CmdHF14AMfURdBl(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str0("k", "key", "<hex>", "key for authentication (UL-C 16 bytes, EV1/NTAG 4 bytes)"),
-        arg_lit0("l", NULL, "swap entered key's endianness"),
-        arg_int1("b", "block", "<dec>", "block number to read"),
-        arg_lit0(NULL, "force", "force operation even if address is out of range"),
+        arg_str0("k", "key", "<hex>", "Authentication key (UL-C 16 bytes, EV1/NTAG 4 bytes)"),
+        arg_lit0("l", NULL, "Swap entered key's endianness"),
+        arg_int1("b", "block", "<dec>", "Nlock number to read"),
+        arg_lit0(NULL, "force", "Force operation even if address is out of range"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -2216,11 +2212,12 @@ static int CmdHF14AMfUDump(const char *Cmd) {
 
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfu dump",
-                  "Reads all pages from Ultralight, Ultralight-C, Ultralight EV1\n"
-                  "NTAG 203, NTAG 210, NTAG 212, NTAG 213, NTAG 215, NTAG 216\n"
-                  "and saves data into binary/json files.\n"
-                  "It autodetects card type.",
-                  "hf mfu dump -f myfile        -> dump whole tag, save to `myfile.bin`\n"
+                  "Dump MIFARE Ultralight/NTAG tag to binary/json files.\n"
+                  "It autodetects card type."
+                  "Supports:\n"
+                  "Ultralight, Ultralight-C, Ultralight EV1\n"
+                  "NTAG 203, NTAG 210, NTAG 212, NTAG 213, NTAG 215, NTAG 216\n",
+                  "hf mfu dump -f myfile\n"
                   "hf mfu dump -k AABBCCDD      -> dump whole tag using pwd AABBCCDD\n"
                   "hf mfu dump -p 10            -> start at page 10 and dump rest of blocks\n"
                   "hf mfu dump -p 10 -q 2       -> start at page 10 and dump two blocks\n"
@@ -2229,11 +2226,11 @@ static int CmdHF14AMfUDump(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str0("f", "file", "<fn>", "specify a filename for dump file"),
-        arg_str0("k", "key", "<hex>", "key for authentication (UL-C 16 bytes, EV1/NTAG 4 bytes)"),
-        arg_lit0("l", NULL, "swap entered key's endianness"),
-        arg_int0("p", "page", "<dec>", "manually set start page number to start from"),
-        arg_int0("q", "qty", "<dec>", "manually set number of pages to dump"),
+        arg_str0("f", "file", "<fn>", "Specify a filename for dump file"),
+        arg_str0("k", "key", "<hex>", "Key for authentication (UL-C 16 bytes, EV1/NTAG 4 bytes)"),
+        arg_lit0("l", NULL, "Swap entered key's endianness"),
+        arg_int0("p", "page", "<dec>", "Manually set start page number to start from"),
+        arg_int0("q", "qty", "<dec>", "Manually set number of pages to dump"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -2481,20 +2478,20 @@ static void wait4response(uint8_t b) {
 static int CmdHF14AMfURestore(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfu restore",
-                  "Restore dumpfile onto card.",
-                  "hf mfu restore -f myfile -s                 -> user specified filename and special write\n"
-                  "hf mfu restore -f myfile -k AABBCCDD -s     -> user specified filename, special write and use key\n"
-                  "hf mfu restore -f myfile -k AABBCCDD -ser   -> user specified filename, special write, use key, ..."
+                  "Restore MIFARE Ultralight/NTAG dump file to tag.\n",
+                  "hf mfu restore -f myfile -s                 -> special write\n"
+                  "hf mfu restore -f myfile -k AABBCCDD -s     -> special write, use key\n"
+                  "hf mfu restore -f myfile -k AABBCCDD -ser   -> special write, use key, write dump pwd, ..."
                  );
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str1("f", "file", "<fn>", "specify a filename to restore"),
+        arg_str1("f", "file", "<fn>", "specify dump filename (bin/json)"),
         arg_str0("k", "key", "<hex>", "key for authentication (UL-C 16 bytes, EV1/NTAG 4 bytes)"),
         arg_lit0("l", NULL, "swap entered key's endianness"),
         arg_lit0("s", NULL, "enable special write UID -MAGIC TAG ONLY-"),
         arg_lit0("e", NULL, "enable special write version/signature -MAGIC NTAG 21* ONLY-"),
-        arg_lit0("r", NULL, "use the password found in dumpfile to configure tag. requires " _YELLOW_("'-e'") " parameter to work"),
+        arg_lit0("r", NULL, "use password found in dumpfile to configure tag. Requires " _YELLOW_("'-e'") " parameter to work"),
         arg_lit0("v", "verbose", "verbose"),
         arg_param_end
     };
@@ -2527,9 +2524,6 @@ static int CmdHF14AMfURestore(const char *Cmd) {
         }
     }
 
-    uint8_t *dump = NULL;
-    size_t bytes_read = 0;
-
     if (fnlen == 0) {
         char *fptr = GenerateFilename("hf-mfu-", "-dump.bin");
         if (fptr != NULL) {
@@ -2540,9 +2534,37 @@ static int CmdHF14AMfURestore(const char *Cmd) {
         free(fptr);
     }
 
-    if (loadFile_safe(filename, "", (void **)&dump, &bytes_read) != PM3_SUCCESS) {
-        PrintAndLogEx(WARNING, "Could not find file " _YELLOW_("%s"), filename);
-        return PM3_EIO;
+   // reserve memory
+    uint8_t *dump = NULL;
+    size_t bytes_read = 0;
+    int res = 0;
+    DumpFileType_t dftype = getfiletype(filename);
+    switch (dftype) {
+        case BIN: {
+            res = loadFile_safe(filename, ".bin", (void **)&dump, &bytes_read);
+            break;
+        }
+        case JSON: {
+            dump = calloc(MFU_MAX_BYTES + MFU_DUMP_PREFIX_LENGTH, sizeof(uint8_t));
+            if (dump == NULL) {
+                PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
+                return PM3_EMALLOC;
+            }
+            res = loadFileJSON(filename, (void *)dump, MFU_MAX_BYTES + MFU_DUMP_PREFIX_LENGTH, &bytes_read, NULL);
+            break;
+        }
+        case EML:
+        case DICTIONARY: {
+            PrintAndLogEx(ERR, "Error: Only BIN/JSON formats allowed");
+            free(dump);
+            return PM3_EINVARG;
+        }
+    }
+
+    if (res != PM3_SUCCESS) {
+        PrintAndLogEx(FAILED, "File: " _YELLOW_("%s") ": not found or locked.", filename);
+        free(dump);
+        return PM3_EFILE;
     }
 
     if (bytes_read < MFU_DUMP_PREFIX_LENGTH) {
@@ -2551,7 +2573,7 @@ static int CmdHF14AMfURestore(const char *Cmd) {
         return PM3_ESOFT;
     }
 
-    int res = convert_mfu_dump_format(&dump, &bytes_read, verbose);
+    res = convert_mfu_dump_format(&dump, &bytes_read, verbose);
     if (res != PM3_SUCCESS) {
         PrintAndLogEx(FAILED, "Failed convert on load to new Ultralight/NTAG format");
         free(dump);
@@ -2559,10 +2581,11 @@ static int CmdHF14AMfURestore(const char *Cmd) {
     }
 
     mfu_dump_t *mem = (mfu_dump_t *)dump;
-    uint8_t pages = (bytes_read - MFU_DUMP_PREFIX_LENGTH) / 4;
+    uint8_t pages = (bytes_read - MFU_DUMP_PREFIX_LENGTH) / MFU_BLOCK_SIZE;
 
     if (pages - 1 != mem->pages) {
         PrintAndLogEx(ERR, "Error, invalid dump, wrong page count");
+        PrintAndLogEx(INFO, " %u  vs mempg %u", pages -1 ,  mem->pages);
         free(dump);
         return PM3_ESOFT;
     }
@@ -2572,6 +2595,8 @@ static int CmdHF14AMfURestore(const char *Cmd) {
     // print dump
     printMFUdumpEx(mem, pages, 0);
 
+    return PM3_SUCCESS;
+
     // Swap endianness
     if (swap_endian && has_key) {
         if (ak_len == 16)
@@ -2706,9 +2731,9 @@ static int CmdHF14AMfUeLoad(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str1("f", "file", "<fn>", "filename of dump"),
+        arg_str1("f", "file", "<fn>", "Filename of dump"),
         arg_lit1(NULL, "ul", "MIFARE Ultralight family"),
-        arg_int0("q", "qty", "<dec>", "number of blocks to load from eml file"),
+        arg_int0("q", "qty", "<dec>", "Number of blocks to load from eml file"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -2731,10 +2756,10 @@ static int CmdHF14AMfUSim(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_int1("t", "type", "<1-10> ", "Simulation type to use"),
-        arg_str0("u", "uid", "<hex>", "4, 7 or 10 byte UID"),
-        arg_int0("n", "num", "<dec>", "Exit simulation after <numreads> blocks have been read by reader. 0 = infinite"),
-        arg_lit0("v", "verbose", "verbose output"),
+        arg_int1("t", "type", "<1..10> ", "Simulation type to use"),
+        arg_str0("u", "uid", "<hex>", "<4|7|10> hex bytes UID"),
+        arg_int0("n", "num", "<dec>", "Exit simulation after <numreads> blocks. 0 = infinite"),
+        arg_lit0("v", "verbose", "Verbose output"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -2759,9 +2784,9 @@ static int CmdHF14AMfUCAuth(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str0(NULL, "key", "<hex>", "key for authentication (UL-C 16 bytes)"),
-        arg_lit0("l", NULL, "swap entered key's endianness"),
-        arg_lit0("k", NULL, "keep field on (only if a password is provided too)"),
+        arg_str0(NULL, "key", "<hex>", "Authentication key (UL-C 16 hex bytes)"),
+        arg_lit0("l", NULL, "Swap entered key's endianness"),
+        arg_lit0("k", NULL, "Keep field on (only if a password is provided)"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -2911,7 +2936,7 @@ static int CmdHF14AMfUCSetPwd(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str0("k", "key", "<hex>", "New key (16 bytes)"),
+        arg_str0("k", "key", "<hex>", "New key (16 hex bytes)"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -2951,14 +2976,14 @@ static int CmdHF14AMfUCSetUid(const char *Cmd) {
 
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfu setuid",
-                  "Set uid on MIFARE Ultralight tag.\n"
+                  "Set UID on MIFARE Ultralight tag.\n"
                   "This only works for `magic Ultralight` tags.",
                   "hf mfu setuid --uid 11223344556677"
                  );
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str0("u", "uid", "<hex>", "new uid (7 bytes)"),
+        arg_str0("u", "uid", "<hex>", "New UID (7 hex bytes)"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -3059,7 +3084,7 @@ static int CmdHF14AMfUGenDiverseKeys(const char *Cmd) {
     void *argtable[] = {
         arg_param_begin,
         arg_str0("u", "uid", "<hex>", "<4|7> hex byte UID"),
-        arg_lit0("r", NULL, "read UID from tag"),
+        arg_lit0("r", NULL, "Read UID from tag"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -3205,9 +3230,9 @@ static int CmdHF14AMfUPwdGen(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str0("u", "uid", "<hex>", "uid (7 bytes)"),
-        arg_lit0("r", NULL, "read uid from tag"),
-        arg_lit0("t", NULL, "selftest"),
+        arg_str0("u", "uid", "<hex>", "UID (7 hex bytes)"),
+        arg_lit0("r", NULL, "Read UID from tag"),
+        arg_lit0("t", NULL, "Selftest"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -3949,9 +3974,9 @@ int CmdHF14MfuNDEFRead(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str0("k", "key", "replace default key for NDEF", NULL),
-        arg_lit0("l", NULL, "swap entered key's endianness"),
-        arg_str0("f", "file", "<fn>", "save raw NDEF to file"),
+        arg_str0("k", "key", "Replace default key for NDEF", NULL),
+        arg_lit0("l", NULL, "Swap entered key's endianness"),
+        arg_str0("f", "file", "<fn>", "Save raw NDEF to file"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -4139,8 +4164,8 @@ static int CmdHF14AMfuView(const char *Cmd) {
                  );
     void *argtable[] = {
         arg_param_begin,
-        arg_str1("f", "file", "<fn>", "filename of dump"),
-        arg_lit0("v", "verbose", "verbose output"),
+        arg_str1("f", "file", "<fn>", "Filename of dump"),
+        arg_lit0("v", "verbose", "Verbose output"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -4269,7 +4294,7 @@ static command_t CommandTable[] = {
     {"view",     CmdHF14AMfuView,           AlwaysAvailable, "Display content from tag dump file"},
     {"wrbl",     CmdHF14AMfUWrBl,           IfPm3Iso14443a,  "Write block"},
     {"---------", CmdHelp,                  IfPm3Iso14443a,  "----------------------- " _CYAN_("simulation") " -----------------------"},
-    {"eload",    CmdHF14AMfUeLoad,          IfPm3Iso14443a,  "load Ultralight .eml dump file into emulator memory"},
+    {"eload",    CmdHF14AMfUeLoad,          IfPm3Iso14443a,  "Load Ultralight .eml dump file into emulator memory"},
     {"eview",    CmdHF14AMfuEView,          IfPm3Iso14443a,  "View emulator memory"},
     {"sim",      CmdHF14AMfUSim,            IfPm3Iso14443a,  "Simulate MIFARE Ultralight from emulator memory"},
     {"---------", CmdHelp,                  IfPm3Iso14443a,  "----------------------- " _CYAN_("magic") " ----------------------------"},
diff --git a/client/src/cmdhfmfu.h b/client/src/cmdhfmfu.h
index b6387f191..568e3c4a9 100644
--- a/client/src/cmdhfmfu.h
+++ b/client/src/cmdhfmfu.h
@@ -22,6 +22,11 @@
 
 #include "mifare.h" // structs
 
+
+#define MFU_BLOCK_SIZE      0x04
+#define MFU_MAX_BLOCKS      0xFF
+#define MFU_MAX_BYTES       (MFU_MAX_BLOCKS * MFU_BLOCK_SIZE)
+
 // Old Ultralight/NTAG dump file format
 // It is used only for converting
 #define OLD_MFU_DUMP_PREFIX_LENGTH 48
diff --git a/client/src/fileutils.c b/client/src/fileutils.c
index e6f9a35db..ddf8580a9 100644
--- a/client/src/fileutils.c
+++ b/client/src/fileutils.c
@@ -1137,14 +1137,16 @@ int loadFileJSONex(const char *preferredName, void *data, size_t maxdatalen, siz
             sprintf(blocks, "$.blocks.%d", i);
 
             size_t len = 0;
-            JsonLoadBufAsHex(root, blocks, &mem->data[sptr], 4, &len);
+            JsonLoadBufAsHex(root, blocks, &mem->data[sptr], MFU_BLOCK_SIZE, &len);
             if (!len)
                 break;
 
             sptr += len;
             mem->pages++;
         }
-
+        // remove one, since pages indicates a index rather than number of available pages
+        --mem->pages;
+        
         *datalen += sptr;
     }
 
