commit 9123eeac2d6db44d66ef08d870b545cf68e5647a
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Dec 17 03:08:35 2021 +0100

    added hf mf acl to decode and print MIFARE ACLs. Thanks for the idea @mwalker33! I know you did implement one but you never pushed it ;)

diff --git a/CHANGELOG.md b/CHANGELOG.md
index 36e9e6386..97f5b9ecd 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,7 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Added `hf mf acl` - decode and print MIFARE access rights (@iceman1001)
  - Added for readline, <tab> autocomplete work inside pm3 client (@iceman1001)
  - Fixed `hf iclass dump` - now uses the right key when suppling credit key (@iceman1001)
  - Changed crc16_legic to be supported in lua (@iceman1001)
diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index 9ae38b4eb..725354ee3 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -258,6 +258,49 @@ static void mf_print_sector_hdr(uint8_t sector) {
     PrintAndLogEx(INFO, "----+-------------------------------------------------+-----------------");
 }
 
+static int CmdHF14AMfAcl(const char *Cmd) {
+    CLIParserContext *ctx;
+    CLIParserInit(&ctx, "hf mf acl",
+                  "Print decoded MIFARE access rights (ACL), \n"
+                  "  A = key A\n"
+                  "  B = key B\n"
+                  "  AB = both key A and B\n"
+                  "  ACCESS = access bytes inside sector trailer block\n"
+                  "  Increment, decrement, transfer, restore is for value blocks",
+                  "hf mf acl\n"
+                  "hf mf acl -d FF0780\n");
+
+    void *argtable[] = {
+        arg_param_begin,
+        arg_str1("d", "data", "<hex>", "ACL bytes specified as 3 hex bytes"),
+        arg_param_end
+    };
+    CLIExecWithReturn(ctx, Cmd, argtable, true);
+
+    int acllen = 0;
+    uint8_t acl[3] = {0};
+    CLIGetHexWithReturn(ctx, 1, acl, &acllen);
+
+    CLIParserFree(ctx);
+
+    PrintAndLogEx(NORMAL, "");
+
+    // look up common default ACL bytes and print a fingerprint line about it.
+    if (memcmp(acl, "\xFF\x07\x80", 3) == 0) {
+        PrintAndLogEx(INFO, "ACL... " _GREEN_("%s") " (transport configuration)", sprint_hex(acl, sizeof(acl)));
+    }
+
+    PrintAndLogEx(NORMAL, "");
+    PrintAndLogEx(INFO, "  # | Access rights");
+    PrintAndLogEx(INFO, "----+-----------------------------------------------------------------");
+    for (int i = 0; i < 4; i++) {
+        PrintAndLogEx(INFO, "%3d | " _YELLOW_("%s"), i, mfGetAccessConditionsDesc(i, acl));
+    }
+    PrintAndLogEx(NORMAL, "");
+    return PM3_SUCCESS;
+}
+
+
 static int CmdHF14AMfDarkside(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mf darkside",
@@ -6190,12 +6233,12 @@ static int CmdHF14AGen3View(const char *Cmd) {
 
 static command_t CommandTable[] = {
     {"help",        CmdHelp,                AlwaysAvailable, "This help"},
-    {"list",        CmdHF14AMfList,         AlwaysAvailable,  "List MIFARE history"},
+    {"list",        CmdHF14AMfList,         AlwaysAvailable, "List MIFARE history"},
     {"-----------", CmdHelp,                IfPm3Iso14443a,  "----------------------- " _CYAN_("recovery") " -----------------------"},
     {"darkside",    CmdHF14AMfDarkside,     IfPm3Iso14443a,  "Darkside attack"},
     {"nested",      CmdHF14AMfNested,       IfPm3Iso14443a,  "Nested attack"},
     {"hardnested",  CmdHF14AMfNestedHard,   AlwaysAvailable, "Nested attack for hardened MIFARE Classic cards"},
-    {"staticnested", CmdHF14AMfNestedStatic, IfPm3Iso14443a,  "Nested attack against static nonce MIFARE Classic cards"},
+    {"staticnested", CmdHF14AMfNestedStatic, IfPm3Iso14443a, "Nested attack against static nonce MIFARE Classic cards"},
     {"autopwn",     CmdHF14AMfAutoPWN,      IfPm3Iso14443a,  "Automatic key recovery tool for MIFARE Classic"},
 //    {"keybrute",    CmdHF14AMfKeyBrute,     IfPm3Iso14443a,  "J_Run's 2nd phase of multiple sector nested authentication key recovery"},
     {"nack",        CmdHf14AMfNack,         IfPm3Iso14443a,  "Test for MIFARE NACK bug"},
@@ -6205,6 +6248,7 @@ static command_t CommandTable[] = {
     {"supercard",   CmdHf14AMfSuperCard,    IfPm3Iso14443a,  "Extract info from a `super card`"},
     {"-----------", CmdHelp,                IfPm3Iso14443a,  "----------------------- " _CYAN_("operations") " -----------------------"},
     {"auth4",       CmdHF14AMfAuth4,        IfPm3Iso14443a,  "ISO14443-4 AES authentication"},
+    {"acl",         CmdHF14AMfAcl,          AlwaysAvailable, "Decode and print MIFARE Classic access rights bytes"},
     {"dump",        CmdHF14AMfDump,         IfPm3Iso14443a,  "Dump MIFARE Classic tag to binary file"},
     {"mad",         CmdHF14AMfMAD,          IfPm3Iso14443a,  "Checks and prints MAD"},
     {"ndefread",    CmdHFMFNDEFRead,        IfPm3Iso14443a,  "Prints NDEF records from card"},
@@ -6213,7 +6257,7 @@ static command_t CommandTable[] = {
     {"rdsc",        CmdHF14AMfRdSc,         IfPm3Iso14443a,  "Read MIFARE Classic sector"},
     {"restore",     CmdHF14AMfRestore,      IfPm3Iso14443a,  "Restore MIFARE Classic binary file to BLANK tag"},
     {"setmod",      CmdHf14AMfSetMod,       IfPm3Iso14443a,  "Set MIFARE Classic EV1 load modulation strength"},
-    {"view",        CmdHF14AMfView,         AlwaysAvailable,  "Display content from tag dump file"},
+    {"view",        CmdHF14AMfView,         AlwaysAvailable, "Display content from tag dump file"},
     {"wipe",        CmdHF14AMfWipe,         IfPm3Iso14443a,  "Wipe card to zeros and default keys/acc"},
     {"wrbl",        CmdHF14AMfWrBl,         IfPm3Iso14443a,  "Write MIFARE Classic block"},
     {"-----------", CmdHelp,                IfPm3Iso14443a,  "----------------------- " _CYAN_("simulation") " -----------------------"},
diff --git a/client/src/mifare/mifare4.c b/client/src/mifare/mifare4.c
index b7ceb0929..266cfbbf2 100644
--- a/client/src/mifare/mifare4.c
+++ b/client/src/mifare/mifare4.c
@@ -45,30 +45,28 @@ const char *mfpGetErrorDescription(uint8_t errorCode) {
 }
 
 AccessConditions_t MFAccessConditions[] = {
-    {0x00, "read AB; write AB; increment AB; decrement transfer restore AB"},
-    {0x01, "read AB; decrement transfer restore AB"},
-    {0x02, "read AB"},
-    {0x03, "read B; write B"},
-    {0x04, "read AB; writeB"},
-    {0x05, "read B"},
-    {0x06, "read AB; write B; increment B; decrement transfer restore AB"},
-    {0x07, "none"}
+    {0x00, "read AB; write AB; increment AB; decrement transfer restore AB", "transport config"},
+    {0x01, "read AB; decrement transfer restore AB", "value block"},
+    {0x02, "read AB", "read/write block"},
+    {0x03, "read B; write B", "read/write block"},
+    {0x04, "read AB; write B", "read/write block"},
+    {0x05, "read B", "read/write block"},
+    {0x06, "read AB; write B; increment B; decrement transfer restore AB", "value block"},
+    {0x07, "none", "read/write block"}
 };
 
 AccessConditions_t MFAccessConditionsTrailer[] = {
-    {0x00, "read A by A; read ACCESS by A; read B by A; write B by A"},
-    {0x01, "write A by A; read ACCESS by A write ACCESS by A; read B by A; write B by A"},
-    {0x02, "read ACCESS by A; read B by A"},
-    {0x03, "write A by B; read ACCESS by AB; write ACCESS by B; write B by B"},
-    {0x04, "write A by B; read ACCESS by AB; write B by B"},
-    {0x05, "read ACCESS by AB; write ACCESS by B"},
-    {0x06, "read ACCESS by AB"},
-    {0x07, "read ACCESS by AB"}
+    {0x00, "read A by A; read ACCESS by A; read/write B by A", ""},
+    {0x01, "write A by A; read/write ACCESS by A; read/write B by A", ""},
+    {0x02, "read ACCESS by A; read B by A", ""},
+    {0x03, "write A by B; read ACCESS by AB; write ACCESS by B; write B by B", ""},
+    {0x04, "write A by B; read ACCESS by AB; write B by B", ""},
+    {0x05, "read ACCESS by AB; write ACCESS by B", ""},
+    {0x06, "read ACCESS by AB", ""},
+    {0x07, "read ACCESS by AB", ""}
 };
 
 const char *mfGetAccessConditionsDesc(uint8_t blockn, uint8_t *data) {
-    static char StaticNone[] = "none";
-
     uint8_t data1 = ((data[1] >> 4) & 0x0f) >> blockn;
     uint8_t data2 = ((data[2]) & 0x0f) >> blockn;
     uint8_t data3 = ((data[2] >> 4) & 0x0f) >> blockn;
@@ -87,6 +85,7 @@ const char *mfGetAccessConditionsDesc(uint8_t blockn, uint8_t *data) {
             }
     };
 
+    static char StaticNone[] = "none";
     return StaticNone;
 }
 /*
diff --git a/client/src/mifare/mifare4.h b/client/src/mifare/mifare4.h
index 994fb16a5..2774eea88 100644
--- a/client/src/mifare/mifare4.h
+++ b/client/src/mifare/mifare4.h
@@ -39,6 +39,7 @@ typedef enum {
 typedef struct {
     uint8_t cond;
     const char *description;
+    const char *application;
 } AccessConditions_t;
 
 
diff --git a/client/src/rl_vocabulory.h b/client/src/rl_vocabulory.h
index f8878ac11..42b64fbe3 100644
--- a/client/src/rl_vocabulory.h
+++ b/client/src/rl_vocabulory.h
@@ -278,6 +278,7 @@ const static vocabulory_t vocabulory[] = {
     { 1, "hf mf decrypt" }, 
     { 0, "hf mf supercard" }, 
     { 0, "hf mf auth4" }, 
+    { 1, "hf mf acl" }, 
     { 0, "hf mf dump" }, 
     { 0, "hf mf mad" }, 
     { 0, "hf mf ndefread" }, 
diff --git a/doc/commands.json b/doc/commands.json
index dfef59fe7..c3b854e9a 100644
--- a/doc/commands.json
+++ b/doc/commands.json
@@ -3109,6 +3109,20 @@
             ],
             "usage": "hf lto wrbl [-h] -d <hex> --block <dec>"
         },
+        "hf mf acl": {
+            "command": "hf mf acl",
+            "description": "print decoded mifare access rights (acl), a = key a b = key b ab = both key a and b access = access bytes inside sector trailer block increment, decrement, transfer, restore is for value blocks",
+            "notes": [
+                "hf mf acl",
+                "hf mf acl -d ff0780"
+            ],
+            "offline": true,
+            "options": [
+                "-h, --help this help",
+                "-d, --data <hex> acl bytes specified as 3 hex bytes"
+            ],
+            "usage": "hf mf acl [-h] -d <hex>"
+        },
         "hf mf auth4": {
             "command": "hf mf auth4",
             "description": "executes aes authentication command in iso14443-4",
@@ -3645,7 +3659,7 @@
         },
         "hf mf help": {
             "command": "hf mf help",
-            "description": "help this help list list mifare history hardnested nested attack for hardened mifare classic cards decrypt [nt] [ar_enc] [at_enc] [data] - to decrypt sniff or trace view display content from tag dump file --------------------------------------------------------------------------------------- hf mf list available offline: yes alias of `trace list -t mf` with selected protocol data to annotate trace buffer you can load a trace from file (see `trace load -h`) or it be downloaded from device by default it accepts all other arguments of `trace list`. note that some might not be relevant for this specific protocol",
+            "description": "help this help list list mifare history hardnested nested attack for hardened mifare classic cards decrypt [nt] [ar_enc] [at_enc] [data] - to decrypt sniff or trace acl decode and print mifare classic access rights bytes view display content from tag dump file --------------------------------------------------------------------------------------- hf mf list available offline: yes alias of `trace list -t mf` with selected protocol data to annotate trace buffer you can load a trace from file (see `trace load -h`) or it be downloaded from device by default it accepts all other arguments of `trace list`. note that some might not be relevant for this specific protocol",
             "notes": [
                 "hf mf list -f -> show frame delay times",
                 "hf mf list -1 -> use trace buffer"
@@ -10039,8 +10053,8 @@
         }
     },
     "metadata": {
-        "commands_extracted": 589,
+        "commands_extracted": 591,
         "extracted_by": "PM3Help2JSON v1.00",
-        "extracted_on": "2021-12-13T02:15:15"
+        "extracted_on": "2021-12-17T02:04:29"
     }
 }
\ No newline at end of file
diff --git a/doc/commands.md b/doc/commands.md
index a270dab94..0ce8b0341 100644
--- a/doc/commands.md
+++ b/doc/commands.md
@@ -417,6 +417,7 @@ Check column "offline" for their availability.
 |`hf mf decrypt          `|Y       |`[nt] [ar_enc] [at_enc] [data] - to decrypt sniff or trace`
 |`hf mf supercard        `|N       |`Extract info from a `super card``
 |`hf mf auth4            `|N       |`ISO14443-4 AES authentication`
+|`hf mf acl              `|Y       |`Decode and print MIFARE Classic access rights bytes`
 |`hf mf dump             `|N       |`Dump MIFARE Classic tag to binary file`
 |`hf mf mad              `|N       |`Checks and prints MAD`
 |`hf mf ndefread         `|N       |`Prints NDEF records from card`
