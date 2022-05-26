commit 3169bf2763d6bfdfafc6b077ffac1cf839ff0a77
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Feb 12 11:26:40 2022 +0100

    hf mfu view - view binary/json mfu dump files

diff --git a/CHANGELOG.md b/CHANGELOG.md
index 7dad22edd..2627c60bc 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,7 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Added `hf mfu view` - view contents of MFU dump files (@iceman1001)
  - Changed `hf_mf_uidbruteforce` - added support for S70, enhance UID length management (@cactuschibre)
  - Fixed build issues that may happen from building `mfd_aes_brute` (@linuxgemini)
  - Added silicon data parsing logic for NXP chips in `hf mfu info` (@linuxgemini)
diff --git a/client/src/cmdhfmfu.c b/client/src/cmdhfmfu.c
index 57d134fd2..e62628253 100644
--- a/client/src/cmdhfmfu.c
+++ b/client/src/cmdhfmfu.c
@@ -4130,6 +4130,85 @@ static int CmdHF14AMfuEView(const char *Cmd) {
     return PM3_SUCCESS;
 }
 
+static int CmdHF14AMfuView(const char *Cmd) {
+
+    CLIParserContext *ctx;
+    CLIParserInit(&ctx, "hf mfu view",
+                  "Print a MIFARE Ultralight/NTAG dump file (bin/json)",
+                  "hf mfu view -f hf-mfu-01020304-dump.bin"
+                 );
+    void *argtable[] = {
+        arg_param_begin,
+        arg_str1("f", "file", "<fn>", "filename of dump"),
+        arg_lit0("v", "verbose", "verbose output"),
+        arg_param_end
+    };
+    CLIExecWithReturn(ctx, Cmd, argtable, false);
+    int fnlen = 0;
+    char filename[FILE_PATH_SIZE];
+    CLIParamStrToBuf(arg_get_str(ctx, 1), (uint8_t *)filename, FILE_PATH_SIZE, &fnlen);
+    bool verbose = arg_get_lit(ctx, 2);
+    CLIParserFree(ctx);
+
+    // reserve memory
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
+    }
+
+    if (bytes_read < MFU_DUMP_PREFIX_LENGTH) {
+        PrintAndLogEx(ERR, "Error, dump file is too small");
+        free(dump);
+        return PM3_ESOFT;
+    }
+
+    res = convert_mfu_dump_format(&dump, &bytes_read, verbose);
+    if (res != PM3_SUCCESS) {
+        PrintAndLogEx(FAILED, "Failed convert on load to new Ultralight/NTAG format");
+        free(dump);
+        return res;
+    }
+
+    uint16_t block_cnt =  ((bytes_read - MFU_DUMP_PREFIX_LENGTH) / MFU_BLOCK_SIZE);
+
+    if (verbose) {
+        PrintAndLogEx(INFO, "File: " _YELLOW_("%s"), filename);
+        PrintAndLogEx(INFO, "File size %zu bytes, file blocks %d (0x%x)", bytes_read, block_cnt, block_cnt);
+    }
+
+    printMFUdumpEx( (mfu_dump_t *)dump, block_cnt, 0);
+    free(dump);
+    return PM3_SUCCESS;
+}
+
+
 /*
 static int CmdHF14AMfUCDecryptAmiibo(const char *Cmd){
 
@@ -4175,24 +4254,25 @@ static int CmdHF14AMfUCDecryptAmiibo(const char *Cmd){
 //------------------------------------
 static command_t CommandTable[] = {
     {"help",     CmdHelp,                   AlwaysAvailable, "This help"},
-    {"-----------", CmdHelp,               IfPm3Iso14443a,  "----------------------- " _CYAN_("recovery") " -------------------------"},
+    {"-----------", CmdHelp,                IfPm3Iso14443a,  "----------------------- " _CYAN_("recovery") " -------------------------"},
     {"keygen",   CmdHF14AMfUGenDiverseKeys, AlwaysAvailable, "Generate 3DES MIFARE diversified keys"},
     {"pwdgen",   CmdHF14AMfUPwdGen,         AlwaysAvailable, "Generate pwd from known algos"},
     {"otptear",  CmdHF14AMfuOtpTearoff,     IfPm3Iso14443a,  "Tear-off test on OTP bits"},
 //    {"tear_cnt", CmdHF14AMfuEv1CounterTearoff,     IfPm3Iso14443a,  "Tear-off test on Ev1/NTAG Counter bits"},
-    {"-----------", CmdHelp,               IfPm3Iso14443a,  "----------------------- " _CYAN_("operations") " -----------------------"},
+    {"-----------", CmdHelp,                IfPm3Iso14443a,  "----------------------- " _CYAN_("operations") " -----------------------"},
     {"cauth",    CmdHF14AMfUCAuth,          IfPm3Iso14443a,  "Authentication - Ultralight-C"},
     {"dump",     CmdHF14AMfUDump,           IfPm3Iso14443a,  "Dump MIFARE Ultralight family tag to binary file"},
     {"info",     CmdHF14AMfUInfo,           IfPm3Iso14443a,  "Tag information"},
     {"ndefread", CmdHF14MfuNDEFRead,        IfPm3Iso14443a,  "Prints NDEF records from card"},
     {"rdbl",     CmdHF14AMfURdBl,           IfPm3Iso14443a,  "Read block"},
     {"restore",  CmdHF14AMfURestore,        IfPm3Iso14443a,  "Restore a dump onto a MFU MAGIC tag"},
+    {"view",     CmdHF14AMfuView,           AlwaysAvailable, "Display content from tag dump file"},
     {"wrbl",     CmdHF14AMfUWrBl,           IfPm3Iso14443a,  "Write block"},
-    {"---------", CmdHelp,                 IfPm3Iso14443a,  "----------------------- " _CYAN_("simulation") " -----------------------"},
+    {"---------", CmdHelp,                  IfPm3Iso14443a,  "----------------------- " _CYAN_("simulation") " -----------------------"},
     {"eload",    CmdHF14AMfUeLoad,          IfPm3Iso14443a,  "load Ultralight .eml dump file into emulator memory"},
     {"eview",    CmdHF14AMfuEView,          IfPm3Iso14443a,  "View emulator memory"},
     {"sim",      CmdHF14AMfUSim,            IfPm3Iso14443a,  "Simulate MIFARE Ultralight from emulator memory"},
-    {"---------", CmdHelp,                 IfPm3Iso14443a,  "----------------------- " _CYAN_("magic") " ----------------------------"},
+    {"---------", CmdHelp,                  IfPm3Iso14443a,  "----------------------- " _CYAN_("magic") " ----------------------------"},
     {"setpwd",   CmdHF14AMfUCSetPwd,        IfPm3Iso14443a,  "Set 3DES key - Ultralight-C"},
     {"setuid",   CmdHF14AMfUCSetUid,        IfPm3Iso14443a,  "Set UID - MAGIC tags only"},
 //    {"---------", CmdHelp,                 IfPm3Iso14443a,  "----------------------- " _CYAN_("amiibo") " ----------------------------"},
