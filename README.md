commit 6447653c641a1661eb4073fd22122d67e5c71216
Author: iceman1001 <iceman@iuse.se>
Date:   Thu May 20 10:07:04 2021 +0200

    hf seos - added the skeleton for future use

diff --git a/client/CMakeLists.txt b/client/CMakeLists.txt
index d1fda1e6e..dc56ac9b2 100644
--- a/client/CMakeLists.txt
+++ b/client/CMakeLists.txt
@@ -251,6 +251,7 @@ set (TARGET_SOURCES
         ${PM3_ROOT}/client/src/cmdhfmfhard.c
         ${PM3_ROOT}/client/src/cmdhfmfp.c
         ${PM3_ROOT}/client/src/cmdhfmfu.c
+        ${PM3_ROOT}/client/src/cmdhfseos.c
         ${PM3_ROOT}/client/src/cmdhfst.c
         ${PM3_ROOT}/client/src/cmdhfthinfilm.c
         ${PM3_ROOT}/client/src/cmdhftopaz.c
diff --git a/client/Makefile b/client/Makefile
index a4546cf86..31e7e68ba 100644
--- a/client/Makefile
+++ b/client/Makefile
@@ -481,6 +481,7 @@ SRCS =  aiddesfire.c \
 		cmdhfmfhard.c \
 		cmdhfmfu.c \
 		cmdhfmfp.c \
+		cmdhfseos.c \
 		cmdhfst.c \
 		cmdhfthinfilm.c \
 		cmdhftopaz.c \
diff --git a/client/src/cmdhf.c b/client/src/cmdhf.c
index a0bb0dba7..75069610f 100644
--- a/client/src/cmdhf.c
+++ b/client/src/cmdhf.c
@@ -36,6 +36,7 @@
 #include "cmdhfthinfilm.h"  // Thinfilm
 #include "cmdhflto.h"       // LTO-CM
 #include "cmdhfcryptorf.h"  // CryptoRF
+#include "cmdhfseos.h"      // SEOS
 #include "cmdhfst.h"        // ST rothult
 #include "cmdhfwaveshare.h" // Waveshare
 #include "cmdtrace.h"       // trace list
@@ -410,6 +411,7 @@ static command_t CommandTable[] = {
     {"mfp",         CmdHFMFP,         AlwaysAvailable, "{ MIFARE Plus RFIDs...                }"},
     {"mfu",         CmdHFMFUltra,     AlwaysAvailable, "{ MIFARE Ultralight RFIDs...          }"},
     {"mfdes",       CmdHFMFDes,       AlwaysAvailable, "{ MIFARE Desfire RFIDs...             }"},
+    {"seos",        CmdHFSeos,        AlwaysAvailable, "{ SEOS RFIDs...                       }"},
     {"st",          CmdHFST,          AlwaysAvailable, "{ ST Rothult RFIDs...                 }"},
     {"thinfilm",    CmdHFThinfilm,    AlwaysAvailable, "{ Thinfilm RFIDs...                   }"},
     {"topaz",       CmdHFTopaz,       AlwaysAvailable, "{ TOPAZ (NFC Type 1) RFIDs...         }"},
diff --git a/client/src/cmdhfseos.c b/client/src/cmdhfseos.c
new file mode 100644
index 000000000..80bfd6beb
--- /dev/null
+++ b/client/src/cmdhfseos.c
@@ -0,0 +1,132 @@
+//-----------------------------------------------------------------------------
+// Copyright (C) 2021 iceman
+//
+// This code is licensed to you under the terms of the GNU GPL, version 2 or,
+// at your option, any later version. See the LICENSE.txt file for the text of
+// the license.
+//-----------------------------------------------------------------------------
+// SEOS commands
+//-----------------------------------------------------------------------------
+#include "cmdhfseos.h"
+#include <stdio.h>
+#include <string.h>
+#include <ctype.h>              // tolower
+#include "cliparser.h"
+#include "cmdparser.h"          // command_t
+#include "comms.h"              // clearCommandBuffer
+#include "cmdtrace.h"
+#include "crc16.h"
+#include "ui.h"
+#include "cmdhf14a.h"           // manufacture
+#include "protocols.h"          // definitions of ISO14A/7816 protocol
+#include "emv/apduinfo.h"       // GetAPDUCodeDescription
+#include "crypto/asn1utils.h"   // ASN1 decode / print
+
+static int CmdHelp(const char *Cmd);
+
+static uint16_t get_sw(uint8_t *d, uint8_t n) {
+    if (n < 2)
+        return 0;
+
+    n -= 2;
+    return d[n] * 0x0100 + d[n + 1];
+}
+
+static int seos_select(void) {
+    bool activate_field = true;
+    bool keep_field_on = true;
+    uint8_t response[PM3_CMD_DATA_SIZE];
+    int resplen = 0;
+
+    // ---------------  Select NDEF Tag application ----------------
+    uint8_t aSELECT_AID[80];
+    int aSELECT_AID_n = 0;
+    param_gethex_to_eol("00a404000aa000000440000101000100", 0, aSELECT_AID, sizeof(aSELECT_AID), &aSELECT_AID_n);
+    int res = ExchangeAPDU14a(aSELECT_AID, aSELECT_AID_n, activate_field, keep_field_on, response, sizeof(response), &resplen);
+    if (res != PM3_SUCCESS) {
+        DropField();
+        return res;
+    }
+
+    if (resplen < 2) {
+        DropField();
+        return PM3_ESOFT;
+    }
+
+    uint16_t sw = get_sw(response, resplen);
+    if (sw != 0x9000) {
+        PrintAndLogEx(ERR, "Selecting SEOS applet aid failed (%04x - %s).", sw, GetAPDUCodeDescription(sw >> 8, sw & 0xff));
+        DropField();
+        return PM3_ESOFT;
+    }
+
+    activate_field = false;
+    keep_field_on = false;
+    // ---------------  CC file reading ----------------
+
+    uint8_t aSELECT_FILE_ADF[30];
+    int aSELECT_FILE_ADF_n = 0;
+    param_gethex_to_eol("80a504001306112b0601040181e43801010201180101020200", 0, aSELECT_FILE_ADF, sizeof(aSELECT_FILE_ADF), &aSELECT_FILE_ADF_n);
+    res = ExchangeAPDU14a(aSELECT_FILE_ADF, aSELECT_FILE_ADF_n, activate_field, keep_field_on, response, sizeof(response), &resplen);
+    if (res != PM3_SUCCESS) {
+        DropField();
+        return res;
+    }
+
+    sw = get_sw(response, resplen);
+    if (sw != 0x9000) {
+        PrintAndLogEx(ERR, "Selecting ADF file failed (%04x - %s).", sw, GetAPDUCodeDescription(sw >> 8, sw & 0xff));
+        DropField();
+        return PM3_ESOFT;
+    }
+
+    // remove the 2byte SW
+    asn1_print(response, resplen - 2, "  ");
+    return PM3_SUCCESS;
+}
+
+int infoSeos(bool verbose) {
+    int res = seos_select();
+    if (res == PM3_SUCCESS) {
+        PrintAndLogEx(NORMAL, "");
+        PrintAndLogEx(INFO, "--- " _CYAN_("Tag Information") " ---------------------------");
+    }
+    return PM3_SUCCESS;
+}
+
+static int CmdHfSeosInfo(const char *Cmd) {
+    CLIParserContext *ctx;
+    CLIParserInit(&ctx, "hf seos info",
+                  "Get info from SEOS tags",
+                  "hf seos info");
+
+    void *argtable[] = {
+        arg_param_begin,
+        arg_param_end
+    };
+    CLIExecWithReturn(ctx, Cmd, argtable, true);
+    CLIParserFree(ctx);
+    return infoSeos(true);
+}
+
+static int CmdHfSeosList(const char *Cmd) {
+    return CmdTraceListAlias(Cmd, "hf seos", "7816");
+}
+
+static command_t CommandTable[] = {
+    {"help",    CmdHelp,        AlwaysAvailable, "This help"},
+    {"info",    CmdHfSeosInfo,  IfPm3NfcBarcode, "Tag information"},
+    {"list",    CmdHfSeosList,  AlwaysAvailable, "List SEOS history"},
+    {NULL, NULL, NULL, NULL}
+};
+
+static int CmdHelp(const char *Cmd) {
+    (void)Cmd; // Cmd is not used so far
+    CmdsHelp(CommandTable);
+    return PM3_SUCCESS;
+}
+
+int CmdHFSeos(const char *Cmd) {
+    clearCommandBuffer();
+    return CmdsParse(CommandTable, Cmd);
+}
diff --git a/client/src/cmdhfseos.h b/client/src/cmdhfseos.h
new file mode 100644
index 000000000..8f77d3610
--- /dev/null
+++ b/client/src/cmdhfseos.h
@@ -0,0 +1,19 @@
+//-----------------------------------------------------------------------------
+// Copyright (C) 2021 iceman
+//
+// This code is licensed to you under the terms of the GNU GPL, version 2 or,
+// at your option, any later version. See the LICENSE.txt file for the text of
+// the license.
+//-----------------------------------------------------------------------------
+// SEOS commands
+//-----------------------------------------------------------------------------
+
+#ifndef CMDHFSEOS_H__
+#define CMDHFSEOS_H__
+
+#include "common.h"
+
+int infoSeos(bool verbose);
+int CmdHFSeos(const char *Cmd);
+
+#endif
