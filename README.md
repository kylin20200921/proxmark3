commit 3b5a1e8de5381ca76cc775ac9788a561bd03ae37
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Feb 24 19:31:47 2022 +0100

    added some helptexts

diff --git a/armsrc/iso14443b.c b/armsrc/iso14443b.c
index f6304fcb5..655ed367e 100644
--- a/armsrc/iso14443b.c
+++ b/armsrc/iso14443b.c
@@ -864,7 +864,7 @@ void SimulateIso14443bTag(uint8_t *pupi) {
                         }
                     } else {
                         if (g_dbglevel >= DBG_DEBUG) {
-                            DbpString("CRC passed");
+                            DbpString("CRC ok");
                         }
                     }
                     cardSTATE = SIM_IDLE;
@@ -1064,7 +1064,7 @@ void Simulate_iso14443b_srx_tag(uint8_t *uid) {
                         }
                     } else {
                         if (g_dbglevel >= DBG_DEBUG) {
-                            DbpString("CRC passed");
+                            DbpString("CRC ok");
                         }
                     }
                     cardSTATE = SIM_IDLE;
diff --git a/client/src/cipurse/cipursetest.c b/client/src/cipurse/cipursetest.c
index 6e1ba9609..5632145f7 100644
--- a/client/src/cipurse/cipursetest.c
+++ b/client/src/cipurse/cipursetest.c
@@ -362,7 +362,7 @@ static bool TestAPDU(void) {
 bool CIPURSETest(bool verbose) {
     bool res = true;
 
-    PrintAndLogEx(INFO, "------ " _CYAN_("CIPURSE Tests") " ------");
+    PrintAndLogEx(INFO, "------ " _CYAN_("CIPURSE tests") " ------");
 
     res = res && TestKVV();
     res = res && TestISO9797M2();
diff --git a/client/src/cmdhfcipurse.c b/client/src/cmdhfcipurse.c
index 9db4d1cc4..ba3f2d956 100644
--- a/client/src/cmdhfcipurse.c
+++ b/client/src/cmdhfcipurse.c
@@ -1803,6 +1803,17 @@ bool CheckCardCipurse(void) {
 }
 
 static int CmdHFCipurseTest(const char *Cmd) {
+    CLIParserContext *ctx;
+    CLIParserInit(&ctx, "hf cipurse test",
+                  "Regression tests",
+                  "hf cipurse test");
+
+    void *argtable[] = {
+        arg_param_begin,
+        arg_param_end
+    };
+    CLIExecWithReturn(ctx, Cmd, argtable, true);
+    CLIParserFree(ctx);
     CIPURSETest(true);
     return PM3_SUCCESS;
 }
@@ -1885,7 +1896,7 @@ static command_t CommandTable[] = {
     {"updkey",    CmdHFCipurseUpdateKey,     IfPm3Iso14443a,  "Update key"},
     {"updakey",   CmdHFCipurseUpdateKeyAttr, IfPm3Iso14443a,  "Update key attributes"},
     {"default",   CmdHFCipurseDefault,       IfPm3Iso14443a,  "Set default key and file id for all the other commands"},
-    {"test",      CmdHFCipurseTest,          AlwaysAvailable, "Tests"},
+    {"test",      CmdHFCipurseTest,          AlwaysAvailable, "Regression tests"},
     {NULL, NULL, 0, NULL}
 };
 
diff --git a/client/src/cmdhfmfdes.c b/client/src/cmdhfmfdes.c
index d7f6b62e2..5b9b851c2 100644
--- a/client/src/cmdhfmfdes.c
+++ b/client/src/cmdhfmfdes.c
@@ -5561,6 +5561,17 @@ static int CmdHF14ADesDump(const char *Cmd) {
 }
 
 static int CmdHF14ADesTest(const char *Cmd) {
+    CLIParserContext *ctx;
+    CLIParserInit(&ctx, "hf mfdes test",
+                  "Regression crypto tests",
+                  "hf mfdes test");
+
+    void *argtable[] = {
+        arg_param_begin,
+        arg_param_end
+    };
+    CLIExecWithReturn(ctx, Cmd, argtable, true);
+    CLIParserFree(ctx);
     DesfireTest(true);
     return PM3_SUCCESS;
 }
@@ -5609,7 +5620,7 @@ static command_t CommandTable[] = {
     {"value",            CmdHF14ADesValueOperations,  IfPm3Iso14443a,  "Operations with value file (get/credit/limited credit/debit/clear)"},
     {"clearrecfile",     CmdHF14ADesClearRecordFile,  IfPm3Iso14443a,  "Clear record File"},
     {"-----------",      CmdHelp,                     IfPm3Iso14443a,  "----------------------- " _CYAN_("System") " -----------------------"},
-    {"test",             CmdHF14ADesTest,             AlwaysAvailable, "Test crypto"},
+    {"test",             CmdHF14ADesTest,             AlwaysAvailable, "Regression crypto tests"},
     {NULL, NULL, NULL, NULL}
 };
 
diff --git a/client/src/cmdlffdxb.c b/client/src/cmdlffdxb.c
index 1d406ea20..f7726c54f 100644
--- a/client/src/cmdlffdxb.c
+++ b/client/src/cmdlffdxb.c
@@ -208,7 +208,7 @@ static int CmdFDXBdemodBI(const char *Cmd) {
     PrintAndLogEx(SUCCESS, "Reserved/RFU:      %u", reservedCode);
     PrintAndLogEx(SUCCESS, "Animal Tag:        %s", animalBit ? _YELLOW_("True") : "False");
     PrintAndLogEx(SUCCESS, "Has extended data: %s [0x%X]", dataBlockBit ? _YELLOW_("True") : "False", extended);
-    PrintAndLogEx(SUCCESS, "CRC:           0x%04X - [%04X] - %s", crc_16, calcCrc, (calcCrc == crc_16) ? _GREEN_("Passed") : _RED_("Fail") );
+    PrintAndLogEx(SUCCESS, "CRC:           0x%04X - [%04X] - %s", crc_16, calcCrc, (calcCrc == crc_16) ? _GREEN_("ok") : _RED_("fail") );
 
     if (g_debugMode) {
         PrintAndLogEx(DEBUG, "Start marker %d;   Size %d", preambleIndex, size);
