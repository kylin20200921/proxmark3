commit d00e138a319815af63cf00b89096d5e1887515ff
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Feb 18 06:19:44 2022 +0100

    added a mifare value block decoder

diff --git a/CHANGELOG.md b/CHANGELOG.md
index 39aba3c9b..913654326 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,7 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Added `hf mf value`  - decode a value block (@iceman1001)
  - Changed `hf mf nested`: removed option `--single` redundant with usage of `--tblk` (@doegox)
  - Fixed `hf mf chk` single block mode (@doegox)
  - Fixed `hf mf fchk/chk` internal logic to load keys (@doegox)
diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index d9b5eb1a3..cdd1bf278 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -6201,6 +6201,54 @@ static int CmdHF14AGen4View(const char *Cmd) {
     return PM3_SUCCESS;
 }
 
+static bool mfc_value(const uint8_t *d, uint32_t *val) {
+    // values
+    uint32_t a = MemLeToUint4byte(d);
+    uint32_t a_inv = MemLeToUint4byte(d + 4);
+    uint32_t b = MemLeToUint4byte(d + 8);
+
+    int val_checks = (
+        (a == b) && (a == ~a_inv) &&
+        (d[12] == (~d[13] & 0xFF)) &&
+        (d[14] == (~d[15] & 0xFF))
+    );
+
+    if (val) {
+        *val = a;
+    }
+    return (val_checks);
+}
+
+static int CmdHF14AMfValue(const char *Cmd) {
+    CLIParserContext *ctx;
+    CLIParserInit(&ctx, "hf mf value",
+                  "Decode of a MIFARE value block",
+                  "hf mf value -d 87D612007829EDFF87D6120011EE11EE\n"
+                 );
+    void *argtable[] = {
+        arg_param_begin,
+        arg_str1("d", "data", "<hex>", "16 hex bytes"),
+        arg_param_end
+    };
+    CLIExecWithReturn(ctx, Cmd, argtable, false);
+
+    int dlen = 0;
+    uint8_t data[16] = {0};
+    CLIGetHexWithReturn(ctx, 1, data, &dlen);
+    CLIParserFree(ctx);
+
+
+    uint32_t value = 0;
+
+    if (mfc_value(data, &value))  {
+        PrintAndLogEx(SUCCESS, "Dec... " _YELLOW_("%" PRIu32 ), value);
+        PrintAndLogEx(SUCCESS, "Hex... " _YELLOW_("0x%" PRIX32), value);
+    } else {
+        PrintAndLogEx(FAILED, "No value block detected");
+    }
+    return PM3_SUCCESS;
+}
+
 static command_t CommandTable[] = {
     {"help",        CmdHelp,                AlwaysAvailable, "This help"},
     {"list",        CmdHF14AMfList,         AlwaysAvailable, "List MIFARE history"},
@@ -6227,6 +6275,7 @@ static command_t CommandTable[] = {
     {"rdsc",        CmdHF14AMfRdSc,         IfPm3Iso14443a,  "Read MIFARE Classic sector"},
     {"restore",     CmdHF14AMfRestore,      IfPm3Iso14443a,  "Restore MIFARE Classic binary file to BLANK tag"},
     {"setmod",      CmdHf14AMfSetMod,       IfPm3Iso14443a,  "Set MIFARE Classic EV1 load modulation strength"},
+    {"value",       CmdHF14AMfValue,        AlwaysAvailable, "Decode a value block"},
     {"view",        CmdHF14AMfView,         AlwaysAvailable, "Display content from tag dump file"},
     {"wipe",        CmdHF14AMfWipe,         IfPm3Iso14443a,  "Wipe card to zeros and default keys/acc"},
     {"wrbl",        CmdHF14AMfWrBl,         IfPm3Iso14443a,  "Write MIFARE Classic block"},
