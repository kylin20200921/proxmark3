commit debf2796cc41c48f3531a04befa0bf6854921739
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 1 09:25:53 2022 +0100

    text - unified

diff --git a/client/src/emv/cmdemv.c b/client/src/emv/cmdemv.c
index e738db089..45d534981 100644
--- a/client/src/emv/cmdemv.c
+++ b/client/src/emv/cmdemv.c
@@ -84,7 +84,7 @@ static int CmdEMVSelect(const char *Cmd) {
         arg_lit0("aA",  "apdu",    "show APDU requests and responses"),
         arg_lit0("tT",  "tlv",     "TLV decode results"),
         arg_lit0("wW",  "wired",   "Send data via contact (iso7816) interface. Contactless interface set by default."),
-        arg_str1(NULL,  NULL,     "<HEX applet AID>", NULL),
+        arg_str1(NULL, NULL, "<hex>", "Applet AID"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -247,7 +247,7 @@ static int CmdEMVGPO(const char *Cmd) {
         arg_lit0("aA",  "apdu",    "show APDU reqests and responses"),
         arg_lit0("tT",  "tlv",     "TLV decode results of selected applets"),
         arg_lit0("wW",  "wired",   "Send data via contact (iso7816) interface. Contactless interface set by default."),
-        arg_strx0(NULL,  NULL,     "<HEX PDOLdata/PDOL>", NULL),
+        arg_strx0(NULL,  NULL,     "<hex>", "PDOLdata/PDOL"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -354,7 +354,7 @@ static int CmdEMVReadRecord(const char *Cmd) {
         arg_lit0("aA",  "apdu",    "show APDU reqests and responses"),
         arg_lit0("tT",  "tlv",     "TLV decode results of selected applets"),
         arg_lit0("wW",  "wired",   "Send data via contact (iso7816) interface. Contactless interface set by default."),
-        arg_strx1(NULL,  NULL,     "<SFI 1byte HEX><SFIrecord 1byte HEX>", NULL),
+        arg_strx1(NULL,  NULL,     "<hex>", "<SFI 1 byte><SFIrecord 1 byte"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -418,7 +418,7 @@ static int CmdEMVAC(const char *Cmd) {
         arg_lit0("aA",  "apdu",     "show APDU reqests and responses"),
         arg_lit0("tT",  "tlv",      "TLV decode results of selected applets"),
         arg_lit0("wW",  "wired",   "Send data via contact (iso7816) interface. Contactless interface set by default."),
-        arg_strx1(NULL,  NULL,      "<HEX CDOLdata/CDOL>", NULL),
+        arg_strx1(NULL,  NULL,      "<hex>", "CDOLdata/CDOL"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -590,7 +590,7 @@ static int CmdEMVInternalAuthenticate(const char *Cmd) {
         arg_lit0("aA",  "apdu",    "show APDU reqests and responses"),
         arg_lit0("tT",  "tlv",     "TLV decode results of selected applets"),
         arg_lit0("wW",  "wired",   "Send data via contact (iso7816) interface. Contactless interface set by default."),
-        arg_strx1(NULL,  NULL,     "<HEX DDOLdata/DDOL>", NULL),
+        arg_strx1(NULL,  NULL,     "<hex>", "DDOLdata/DDOL"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -1442,7 +1442,7 @@ static int CmdEMVScan(const char *Cmd) {
         arg_lit0("gG",  "acgpo",    "VISA. generate AC from GPO."),
         arg_lit0("mM",  "merge",    "Merge output file with card's data. (warning: the file may be corrupted!)"),
         arg_lit0("wW",  "wired",    "Send data via contact (iso7816) interface. Contactless interface set by default."),
-        arg_str1(NULL,  NULL,       "output.json", "JSON output file name"),
+        arg_str1(NULL,  NULL,       "<fn>", "JSON output filename"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
