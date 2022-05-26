commit f4974a74e6b5e8f4d25c2e8fe93e1e062a68175c
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Feb 7 22:43:58 2022 +0100

    unify

diff --git a/client/src/emv/cmdemv.c b/client/src/emv/cmdemv.c
index 837f4619e..a855b672f 100644
--- a/client/src/emv/cmdemv.c
+++ b/client/src/emv/cmdemv.c
@@ -87,11 +87,11 @@ static int CmdEMVSelect(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("sS",  "select",  "activate field and select card"),
-        arg_lit0("kK",  "keep",    "keep field for next command"),
-        arg_lit0("aA",  "apdu",    "show APDU requests and responses"),
+        arg_lit0("sS",  "select",  "Activate field and select card"),
+        arg_lit0("kK",  "keep",    "Keep field for next command"),
+        arg_lit0("aA",  "apdu",    "Show APDU requests and responses"),
         arg_lit0("tT",  "tlv",     "TLV decode results"),
-        arg_lit0("wW",  "wired",   "Send data via contact (iso7816) interface. Contactless interface set by default."),
+        arg_lit0("wW",  "wired",   "Send data via contact (iso7816) interface. (def: Contactless interface)"),
         arg_str1(NULL, NULL, "<hex>", "Applet AID"),
         arg_param_end
     };
@@ -138,11 +138,11 @@ static int CmdEMVSearch(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("sS",  "select",  "activate field and select card"),
-        arg_lit0("kK",  "keep",    "keep field ON for next command"),
-        arg_lit0("aA",  "apdu",    "show APDU reqests and responses"),
+        arg_lit0("sS",  "select",  "Activate field and select card"),
+        arg_lit0("kK",  "keep",    "Keep field ON for next command"),
+        arg_lit0("aA",  "apdu",    "Show APDU reqests and responses"),
         arg_lit0("tT",  "tlv",     "TLV decode results of selected applets"),
-        arg_lit0("wW",  "wired",   "Send data via contact (iso7816) interface. Contactless interface set by default."),
+        arg_lit0("wW",  "wired",   "Send data via contact (iso7816) interface. (def: Contactless interface)"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -192,13 +192,13 @@ static int CmdEMVPPSE(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("sS",  "select",  "activate field and select card"),
-        arg_lit0("kK",  "keep",    "keep field ON for next command"),
-        arg_lit0("1",   "pse",     "pse (1PAY.SYS.DDF01) mode"),
-        arg_lit0("2",   "ppse",    "ppse (2PAY.SYS.DDF01) mode (default mode)"),
-        arg_lit0("aA",  "apdu",    "show APDU reqests and responses"),
+        arg_lit0("sS",  "select",  "Activate field and select card"),
+        arg_lit0("kK",  "keep",    "Keep field ON for next command"),
+        arg_lit0("1",   "pse",     "PSE (1PAY.SYS.DDF01) mode"),
+        arg_lit0("2",   "ppse",    "PPSE (2PAY.SYS.DDF01) mode (def)"),
+        arg_lit0("aA",  "apdu",    "Show APDU reqests and responses"),
         arg_lit0("tT",  "tlv",     "TLV decode results of selected applets"),
-        arg_lit0("wW",  "wired",   "Send data via contact (iso7816) interface. Contactless interface set by default."),
+        arg_lit0("wW",  "wired",   "Send data via contact (iso7816) interface. (def: Contactless interface)"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -252,12 +252,12 @@ static int CmdEMVGPO(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("kK",  "keep",    "keep field ON for next command"),
-        arg_lit0("pP",  "params",  "load parameters from `emv_defparams.json` file for PDOLdata making from PDOL and parameters"),
-        arg_lit0("mM",  "make",    "make PDOLdata from PDOL (tag 9F38) and parameters (by default uses default parameters)"),
-        arg_lit0("aA",  "apdu",    "show APDU reqests and responses"),
+        arg_lit0("kK",  "keep",    "Keep field ON for next command"),
+        arg_lit0("pP",  "params",  "Load parameters from `emv_defparams.json` file for PDOLdata making from PDOL and parameters"),
+        arg_lit0("mM",  "make",    "Make PDOLdata from PDOL (tag 9F38) and parameters (def: uses default parameters)"),
+        arg_lit0("aA",  "apdu",    "Show APDU reqests and responses"),
         arg_lit0("tT",  "tlv",     "TLV decode results of selected applets"),
-        arg_lit0("wW",  "wired",   "Send data via contact (iso7816) interface. Contactless interface set by default."),
+        arg_lit0("wW",  "wired",   "Send data via contact (iso7816) interface. (def: Contactless interface)"),
         arg_strx0(NULL,  NULL,     "<hex>", "PDOLdata/PDOL"),
         arg_param_end
     };
@@ -361,10 +361,10 @@ static int CmdEMVReadRecord(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("kK",  "keep",    "keep field ON for next command"),
-        arg_lit0("aA",  "apdu",    "show APDU reqests and responses"),
+        arg_lit0("kK",  "keep",    "Keep field ON for next command"),
+        arg_lit0("aA",  "apdu",    "Show APDU reqests and responses"),
         arg_lit0("tT",  "tlv",     "TLV decode results of selected applets"),
-        arg_lit0("wW",  "wired",   "Send data via contact (iso7816) interface. Contactless interface set by default."),
+        arg_lit0("wW",  "wired",   "Send data via contact (iso7816) interface. (def: Contactless interface)"),
         arg_strx1(NULL,  NULL,     "<hex>", "<SFI 1 byte><SFIrecord 1 byte"),
         arg_param_end
     };
@@ -421,14 +421,14 @@ static int CmdEMVAC(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("kK",  "keep",     "keep field ON for next command"),
-        arg_lit0("cC",  "cda",      "executes CDA transaction. Needs to get SDAD in results."),
+        arg_lit0("kK",  "keep",     "Keep field ON for next command"),
+        arg_lit0("cC",  "cda",      "Executes CDA transaction. Needs to get SDAD in results."),
         arg_str0("dD",  "decision", "<aac|tc|arqc>", "Terminal decision. aac - declined, tc - approved, arqc - online authorisation requested"),
-        arg_lit0("pP",  "params",   "load parameters from `emv_defparams.json` file for CDOLdata making from CDOL and parameters"),
-        arg_lit0("mM",  "make",     "make CDOLdata from CDOL (tag 8C and 8D) and parameters (by default uses default parameters)"),
-        arg_lit0("aA",  "apdu",     "show APDU reqests and responses"),
+        arg_lit0("pP",  "params",   "Load parameters from `emv_defparams.json` file for CDOLdata making from CDOL and parameters"),
+        arg_lit0("mM",  "make",     "Make CDOLdata from CDOL (tag 8C and 8D) and parameters (def: use default parameters)"),
+        arg_lit0("aA",  "apdu",     "Show APDU reqests and responses"),
         arg_lit0("tT",  "tlv",      "TLV decode results of selected applets"),
-        arg_lit0("wW",  "wired",   "Send data via contact (iso7816) interface. Contactless interface set by default."),
+        arg_lit0("wW",  "wired",    "Send data via contact (iso7816) interface. (def: Contactless interface)"),
         arg_strx1(NULL,  NULL,      "<hex>", "CDOLdata/CDOL"),
         arg_param_end
     };
@@ -543,9 +543,9 @@ static int CmdEMVGenerateChallenge(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("kK",  "keep",    "keep field ON for next command"),
-        arg_lit0("aA",  "apdu",    "show APDU reqests and responses"),
-        arg_lit0("wW",  "wired",   "Send data via contact (iso7816) interface. Contactless interface set by default."),
+        arg_lit0("kK",  "keep",    "Keep field ON for next command"),
+        arg_lit0("aA",  "apdu",    "Show APDU reqests and responses"),
+        arg_lit0("wW",  "wired",   "Send data via contact (iso7816) interface. (def: Contactless interface)"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -595,12 +595,12 @@ static int CmdEMVInternalAuthenticate(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("kK",  "keep",    "keep field ON for next command"),
-        arg_lit0("pP",  "params",  "load parameters from `emv_defparams.json` file for DDOLdata making from DDOL and parameters"),
-        arg_lit0("mM",  "make",    "make DDOLdata from DDOL (tag 9F49) and parameters (by default uses default parameters)"),
-        arg_lit0("aA",  "apdu",    "show APDU reqests and responses"),
+        arg_lit0("kK",  "keep",    "Keep field ON for next command"),
+        arg_lit0("pP",  "params",  "Load parameters from `emv_defparams.json` file for DDOLdata making from DDOL and parameters"),
+        arg_lit0("mM",  "make",    "Make DDOLdata from DDOL (tag 9F49) and parameters (def: use default parameters)"),
+        arg_lit0("aA",  "apdu",    "Show APDU reqests and responses"),
         arg_lit0("tT",  "tlv",     "TLV decode results of selected applets"),
-        arg_lit0("wW",  "wired",   "Send data via contact (iso7816) interface. Contactless interface set by default."),
+        arg_lit0("wW",  "wired",   "Send data via contact (iso7816) interface. (def: Contactless interface)"),
         arg_strx1(NULL,  NULL,     "<hex>", "DDOLdata/DDOL"),
         arg_param_end
     };
@@ -822,17 +822,17 @@ static int CmdEMVExec(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("sS",  "select",   "activate field and select card."),
-        arg_lit0("aA",  "apdu",     "show APDU reqests and responses."),
-        arg_lit0("tT",  "tlv",      "TLV decode results."),
-        arg_lit0("jJ",  "jload",    "Load transaction parameters from `emv_defparams.json` file."),
-        arg_lit0("fF",  "forceaid", "Force search AID. Search AID instead of execute PPSE."),
+        arg_lit0("sS",  "select",   "Activate field and select card"),
+        arg_lit0("aA",  "apdu",     "Show APDU reqests and responses"),
+        arg_lit0("tT",  "tlv",      "TLV decode results"),
+        arg_lit0("jJ",  "jload",    "Load transaction parameters from `emv_defparams.json` file"),
+        arg_lit0("fF",  "forceaid", "Force search AID. Search AID instead of execute PPSE"),
         arg_rem("By default:",      "Transaction type - MSD"),
-        arg_lit0("vV",  "qvsdc",    "Transaction type - qVSDC or M/Chip."),
-        arg_lit0("cC",  "qvsdccda", "Transaction type - qVSDC or M/Chip plus CDA (SDAD generation)."),
-        arg_lit0("xX",  "vsdc",     "Transaction type - VSDC. For test only. Not a standard behavior."),
-        arg_lit0("gG",  "acgpo",    "VISA. generate AC from GPO."),
-        arg_lit0("wW",  "wired",   "Send data via contact (iso7816) interface. Contactless interface set by default."),
+        arg_lit0("vV",  "qvsdc",    "Transaction type - qVSDC or M/Chip"),
+        arg_lit0("cC",  "qvsdccda", "Transaction type - qVSDC or M/Chip plus CDA (SDAD generation)"),
+        arg_lit0("xX",  "vsdc",     "Transaction type - VSDC. For test only. Not a standard behavior"),
+        arg_lit0("gG",  "acgpo",    "VISA. generate AC from GPO"),
+        arg_lit0("wW",  "wired",    "Send data via contact (iso7816) interface. (def: Contactless interface)"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -1448,17 +1448,17 @@ static int CmdEMVScan(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("aA",  "apdu",     "show APDU reqests and responses."),
-        arg_lit0("tT",  "tlv",      "TLV decode results."),
+        arg_lit0("aA",  "apdu",     "Show APDU reqests and responses"),
+        arg_lit0("tT",  "tlv",      "TLV decode results"),
         arg_lit0("eE",  "extract",  "Extract TLV elements and fill Application Data"),
-        arg_lit0("jJ",  "jload",    "Load transaction parameters from `emv_defparams.json` file."),
+        arg_lit0("jJ",  "jload",    "Load transaction parameters from `emv_defparams.json` file"),
         arg_rem("By default:",      "Transaction type - MSD"),
-        arg_lit0("vV",  "qvsdc",    "Transaction type - qVSDC or M/Chip."),
-        arg_lit0("cC",  "qvsdccda", "Transaction type - qVSDC or M/Chip plus CDA (SDAD generation)."),
-        arg_lit0("xX",  "vsdc",     "Transaction type - VSDC. For test only. Not a standard behavior."),
-        arg_lit0("gG",  "acgpo",    "VISA. generate AC from GPO."),
+        arg_lit0("vV",  "qvsdc",    "Transaction type - qVSDC or M/Chip"),
+        arg_lit0("cC",  "qvsdccda", "Transaction type - qVSDC or M/Chip plus CDA (SDAD generation)"),
+        arg_lit0("xX",  "vsdc",     "Transaction type - VSDC. For test only. Not a standard behavior"),
+        arg_lit0("gG",  "acgpo",    "VISA. generate AC from GPO"),
         arg_lit0("mM",  "merge",    "Merge output file with card's data. (warning: the file may be corrupted!)"),
-        arg_lit0("wW",  "wired",    "Send data via contact (iso7816) interface. Contactless interface set by default."),
+        arg_lit0("wW",  "wired",    "Send data via contact (iso7816) interface. (def: Contactless interface)"),
         arg_str1(NULL,  NULL,       "<fn>", "JSON output filename"),
         arg_param_end
     };
@@ -1839,8 +1839,8 @@ static int CmdEMVTest(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("i", "ignore", "ignore timing tests for VM"),
-        arg_lit0("l", "long", "run long tests too"),
+        arg_lit0("i", "ignore", "Ignore timing tests for VM"),
+        arg_lit0("l", "long",   "Run long tests too"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -1871,9 +1871,9 @@ static int CmdEMVRoca(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("tT",  "selftest",   "self test"),
-        arg_lit0("aA",  "apdu",    "show APDU reqests and responses"),
-        arg_lit0("wW",  "wired",   "Send data via contact (iso7816) interface. Contactless interface set by default"),
+        arg_lit0("tT",  "selftest", "Self test"),
+        arg_lit0("aA",  "apdu",     "Show APDU reqests and responses"),
+        arg_lit0("wW",  "wired",    "Send data via contact (iso7816) interface. (def: Contactless interface)"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -2124,17 +2124,17 @@ out:
 
 static command_t CommandTable[] =  {
     {"help",        CmdHelp,                        AlwaysAvailable, "This help"},
-    {"exec",        CmdEMVExec,                     IfPm3Iso14443,   "Executes EMV contactless transaction."},
-    {"pse",         CmdEMVPPSE,                     IfPm3Iso14443,   "Execute PPSE. It selects 2PAY.SYS.DDF01 or 1PAY.SYS.DDF01 directory."},
-    {"search",      CmdEMVSearch,                   IfPm3Iso14443,   "Try to select all applets from applets list and print installed applets."},
-    {"select",      CmdEMVSelect,                   IfPm3Iso14443,   "Select applet."},
-    {"gpo",         CmdEMVGPO,                      IfPm3Iso14443,   "Execute GetProcessingOptions."},
-    {"readrec",     CmdEMVReadRecord,               IfPm3Iso14443,   "Read files from card."},
-    {"genac",       CmdEMVAC,                       IfPm3Iso14443,   "Generate ApplicationCryptogram."},
-    {"challenge",   CmdEMVGenerateChallenge,        IfPm3Iso14443,   "Generate challenge."},
-    {"intauth",     CmdEMVInternalAuthenticate,     IfPm3Iso14443,   "Internal authentication."},
-    {"scan",        CmdEMVScan,                     IfPm3Iso14443,   "Scan EMV card and save it contents to json file for emulator."},
-    {"test",        CmdEMVTest,                     AlwaysAvailable, "Crypto logic test."},
+    {"exec",        CmdEMVExec,                     IfPm3Iso14443,   "Executes EMV contactless transaction"},
+    {"pse",         CmdEMVPPSE,                     IfPm3Iso14443,   "Execute PPSE. It selects 2PAY.SYS.DDF01 or 1PAY.SYS.DDF01 directory"},
+    {"search",      CmdEMVSearch,                   IfPm3Iso14443,   "Try to select all applets from applets list and print installed applets"},
+    {"select",      CmdEMVSelect,                   IfPm3Iso14443,   "Select applet"},
+    {"gpo",         CmdEMVGPO,                      IfPm3Iso14443,   "Execute GetProcessingOptions"},
+    {"readrec",     CmdEMVReadRecord,               IfPm3Iso14443,   "Read files from card"},
+    {"genac",       CmdEMVAC,                       IfPm3Iso14443,   "Generate ApplicationCryptogram"},
+    {"challenge",   CmdEMVGenerateChallenge,        IfPm3Iso14443,   "Generate challenge"},
+    {"intauth",     CmdEMVInternalAuthenticate,     IfPm3Iso14443,   "Internal authentication"},
+    {"scan",        CmdEMVScan,                     IfPm3Iso14443,   "Scan EMV card and save it contents to json file for emulator"},
+    {"test",        CmdEMVTest,                     AlwaysAvailable, "Crypto logic test"},
     /*
     {"getrng",      CmdEMVGetrng,                   IfPm3Iso14443,   "get random number from terminal"},
     {"eload",       CmdEmvELoad,                    IfPm3Iso14443,   "load EMV tag into device"},
@@ -2142,7 +2142,7 @@ static command_t CommandTable[] =  {
     {"sim",         CmdEmvSim,                      IfPm3Iso14443,   "simulate EMV tag"},
     {"clone",       CmdEmvClone,                    IfPm3Iso14443,   "clone an EMV tag"},
     */
-    {"list",        CmdEMVList,                     AlwaysAvailable,   "List ISO7816 history"},
+    {"list",        CmdEMVList,                     AlwaysAvailable, "List ISO7816 history"},
     {"roca",        CmdEMVRoca,                     IfPm3Iso14443,   "Extract public keys and run ROCA test"},
     {NULL, NULL, NULL, NULL}
 };
