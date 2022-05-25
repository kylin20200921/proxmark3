commit ff1d66da619be3a0bf68934430e4d4fa4304c1d4
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Dec 27 18:27:38 2021 +0100

    remake style and output

diff --git a/client/src/cmdhfksx6924.c b/client/src/cmdhfksx6924.c
index ab6888ac1..edcc11ab5 100644
--- a/client/src/cmdhfksx6924.c
+++ b/client/src/cmdhfksx6924.c
@@ -1,4 +1,3 @@
-// -*- mode: c; indent-tabs-mode: nil; tab-width: 3 -*-
 //-----------------------------------------------------------------------------
 // Copyright (C) 2019 micolous+git@gmail.com
 //
@@ -42,66 +41,65 @@
 
 static int CmdHelp(const char *Cmd);
 
-static void getAndPrintBalance(void) {
-    uint32_t balance;
-    bool ret = KSX6924GetBalance(&balance);
-    if (!ret) {
+static int get_and_print_balance(void) {
+    uint32_t balance = 0;
+    if (KSX6924GetBalance(&balance) == false) {
         PrintAndLogEx(ERR, "Error getting balance");
-        return;
+        return PM3_ESOFT;
     }
 
-    PrintAndLogEx(SUCCESS, "Current balance: %ld won/cents", balance);
+    PrintAndLogEx(SUCCESS, "Current balance: " _YELLOW_("%ld") " won/cents", balance);
+    return PM3_SUCCESS;
 }
 
 static int CmdHFKSX6924Balance(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf ksx6924 balance",
-                  "Gets the current purse balance.\n",
-                  "Usage:\n\thf ksx6924 balance\n");
+                  "Gets the current purse balance",
+                  "hf ksx6924 balance\n");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("kK",  "keep",    "keep field ON for next command"),
-        arg_lit0("aA",  "apdu",    "show APDU reqests and responses"),
+        arg_lit0("k", "keep", "keep field ON for next command"),
+        arg_lit0("a", "apdu", "show APDU reqests and responses"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
 
-    bool leaveSignalON = arg_get_lit(ctx, 1);
+    bool keep = arg_get_lit(ctx, 1);
     bool APDULogging = arg_get_lit(ctx, 2);
 
     CLIParserFree(ctx);
     SetAPDULogging(APDULogging);
 
-    bool ret = KSX6924TrySelect();
-    if (!ret) {
-        goto end;
+    if ( KSX6924TrySelect()) {
+        get_and_print_balance();
     }
 
-    getAndPrintBalance();
-
-end:
-    if (!leaveSignalON) {
+    if (keep == false) {
         DropField();
     }
-    return 0;
+
+    return PM3_SUCCESS;
 }
 
 static int CmdHFKSX6924Info(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf ksx6924 info",
-                  "Get info about a KS X 6924 transit card.\nThis application is used by T-Money (South Korea) and Snapper+ (Wellington, New Zealand).\n",
-                  "Usage:\n\thf ksx6924 info\n");
+                  "Get info about a KS X 6924 transit card.\n"
+                  "This application is used by T-Money (South Korea) and\n"
+                  "Snapper+ (Wellington, New Zealand).",
+                  "hf ksx6924 info\n");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("kK",  "keep",    "keep field ON for next command"),
-        arg_lit0("aA",  "apdu",    "show APDU reqests and responses"),
+        arg_lit0("k", "keep", "keep field ON for next command"),
+        arg_lit0("a", "apdu", "show APDU reqests and responses"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
 
-    bool leaveSignalON = arg_get_lit(ctx, 1);
+    bool keep = arg_get_lit(ctx, 1);
     bool APDULogging = arg_get_lit(ctx, 2);
 
     CLIParserFree(ctx);
@@ -114,7 +112,7 @@ static int CmdHFKSX6924Info(const char *Cmd) {
     int res = KSX6924Select(true, true, buf, sizeof(buf), &len, &sw);
 
     if (res) {
-        if (!leaveSignalON) {
+        if (keep == false) {
             DropField();
         }
         return res;
@@ -200,24 +198,24 @@ static int CmdHFKSX6924Info(const char *Cmd) {
 
     KSX6924PrintPurseInfo(&purseInfo);
 
-    getAndPrintBalance();
+    get_and_print_balance();
 
 end:
-    if (!leaveSignalON) {
+    if (keep == false) {
         DropField();
     }
-    return 0;
+    return PM3_SUCCESS;
 }
 
 static int CmdHFKSX6924Select(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf ksx6924 select",
-                  "Selects KS X 6924 application, and leaves field up.\n",
-                  "Usage:\n\thf ksx6924 select\n");
+                  "Selects KS X 6924 application, and leaves field up",
+                  "hf ksx6924 select\n");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("aA",  "apdu",    "show APDU reqests and responses"),
+        arg_lit0("a", "apdu", "show APDU reqests and responses"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -226,133 +224,139 @@ static int CmdHFKSX6924Select(const char *Cmd) {
     CLIParserFree(ctx);
     SetAPDULogging(APDULogging);
 
-    bool ret = KSX6924TrySelect();
-    if (ret) {
-        PrintAndLogEx(SUCCESS, "OK");
+    if (KSX6924TrySelect()) {
+        PrintAndLogEx(SUCCESS, "Card is selected and field is up");
     } else {
         // Wrong app, drop field.
         DropField();
     }
 
-    return 0;
+    return PM3_SUCCESS;
 }
 
-static int CmdHFKSX6924InitializeCard(const char *Cmd) {
+static int CmdHFKSX6924Initialize(const char *Cmd) {
     CLIParserContext *ctx;
-    CLIParserInit(&ctx, "hf ksx6924 initializecard",
-                  "Perform transaction initialization. (Mpda)\n",
-                  "Usage:\n\thf ksx6924 initializecard 000003e8 -> Mpda\n");
+    CLIParserInit(&ctx, "hf ksx6924 initialize",
+                  "Perform transaction initialization (mpda)",
+                  "hf ksx6924 initialize 000003e8 -> mpda\n");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("kK",  "keep",    "keep field ON for next command"),
-        arg_lit0("aA",  "apdu",    "show APDU reqests and responses"),
-        arg_strx1(NULL,  NULL,     "<Mpda 4byte hex>", NULL),
+        arg_lit0("k",  "keep", "keep field ON for next command"),
+        arg_lit0("a",  "apdu", "show APDU reqests and responses"),
+        arg_strx1(NULL, NULL,  "<mpda 4byte hex>", NULL),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
 
-    bool leaveSignalON = arg_get_lit(ctx, 1);
+    bool keep = arg_get_lit(ctx, 1);
     bool APDULogging = arg_get_lit(ctx, 2);
+
     uint8_t data[APDU_RES_LEN] = {0};
     int datalen = 0;
     CLIGetHexWithReturn(ctx, 3, data, &datalen);
+
     CLIParserFree(ctx);
     SetAPDULogging(APDULogging);
 
     if (datalen != 4) {
         PrintAndLogEx(WARNING, "Mpda parameter must be 4 byte long (eg: 000003e8)");
-        goto end;
+        return PM3_EINVARG;
     }
 
-    bool ret = KSX6924TrySelect();
-    if (!ret) {
+    // try selecting card
+    if (KSX6924TrySelect() == false) {
         goto end;
     }
 
-    PrintAndLogEx(NORMAL, "Initialize Card : Mpda -> %02X %02X %02X %02X", data[0], data[1], data[2], data[3]);
-    uint8_t response[25];
-    if (!KSX6924InitializeCard(data[0], data[1], data[2], data[3], response)) {
+    PrintAndLogEx(SUCCESS, "Initialize Card : Mpda -> %02X %02X %02X %02X", data[0], data[1], data[2], data[3]);
+
+    uint8_t response[25] = {0};
+    if (KSX6924InitializeCard(data[0], data[1], data[2], data[3], response) ) {
+        PrintAndLogEx(SUCCESS, "Response : %s", sprint_hex(response, sizeof(response)));
+    } else {
         PrintAndLogEx(FAILED, "Initialize Card Error");
-        goto end;
     }
 
-    PrintAndLogEx(NORMAL, "Response : %s", sprint_hex(response, sizeof(response)));
-
 end:
-    if (!leaveSignalON) {
+    if (keep == false) {
         DropField();
     }
-    return 0;
+
+    return PM3_SUCCESS;
 }
 
 static int CmdHFKSX6924PRec(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf ksx6924 prec",
-                  "Executes proprietary read record command.\nData format is unknown. Other records are available with 'emv getrec'.\n",
-                  "Usage:\n\thf ksx6924 prec 0b -> read proprietary record 0x0b\n");
+                  "Executes proprietary read record command.\n"
+                  "Data format is unknown. Other records are available with 'emv getrec'.\n",
+                  "hf ksx6924 prec 0b -> read proprietary record 0x0b");
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("kK",  "keep",    "keep field ON for next command"),
-        arg_lit0("aA",  "apdu",    "show APDU reqests and responses"),
-        arg_strx1(NULL,  NULL,     "<record 1byte HEX>", NULL),
+        arg_lit0("k",   "keep", "keep field ON for next command"),
+        arg_lit0("a",   "apdu", "show APDU reqests and responses"),
+        arg_strx1(NULL,  NULL,  "<record 1byte HEX>", NULL),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
 
-    bool leaveSignalON = arg_get_lit(ctx, 1);
+    bool keep = arg_get_lit(ctx, 1);
     bool APDULogging = arg_get_lit(ctx, 2);
+
     uint8_t data[APDU_RES_LEN] = {0};
     int datalen = 0;
     CLIGetHexWithReturn(ctx, 3, data, &datalen);
+
     CLIParserFree(ctx);
     SetAPDULogging(APDULogging);
 
     if (datalen != 1) {
         PrintAndLogEx(WARNING, "Record parameter must be 1 byte long (eg: 0f)");
-        goto end;
+        return PM3_EINVARG;
     }
 
-    bool ret = KSX6924TrySelect();
-    if (!ret) {
+    if (KSX6924TrySelect() == false) {
         goto end;
     }
 
-    PrintAndLogEx(NORMAL, "Getting record %02x...", data[0]);
-    uint8_t recordData[0x10];
-    if (!KSX6924ProprietaryGetRecord(data[0], recordData, sizeof(recordData))) {
+    PrintAndLogEx(SUCCESS, "Getting record %02x ...", data[0]);
+
+    uint8_t recordData[0x10] = {0};
+    if (KSX6924ProprietaryGetRecord(data[0], recordData, sizeof(recordData))) {
+        PrintAndLogEx(SUCCESS, "  %s", sprint_hex(recordData, sizeof(recordData)));
+    } else {
         PrintAndLogEx(FAILED, "Error getting record");
-        goto end;
     }
 
-    PrintAndLogEx(NORMAL, "  %s", sprint_hex(recordData, sizeof(recordData)));
-
 end:
-    if (!leaveSignalON) {
+    if (keep == false) {
         DropField();
     }
-    return 0;
+    return PM3_SUCCESS;
 }
 
 static command_t CommandTable[] = {
-    {"help",    CmdHelp,             AlwaysAvailable, "This help."},
-    {"info",    CmdHFKSX6924Info,    AlwaysAvailable, "Get info about a KS X 6924 (T-Money, Snapper+) transit card"},
-    {"select",  CmdHFKSX6924Select,  AlwaysAvailable, "Select application, and leave field up"},
-    {"balance", CmdHFKSX6924Balance, AlwaysAvailable, "Get current purse balance"},
-    {"initializecard", CmdHFKSX6924InitializeCard, AlwaysAvailable, "Perform transaction initialization (Mpda)"},
-    {"prec",    CmdHFKSX6924PRec,    AlwaysAvailable, "Send proprietary get record command (CLA=90, INS=4C)"},
+    {"help",       CmdHelp,                AlwaysAvailable, "This help"},
+    {"balance",    CmdHFKSX6924Balance,    IfPm3Iso14443a,  "Get current purse balance"},
+    {"info",       CmdHFKSX6924Info,       IfPm3Iso14443a,  "Get info about a KS X 6924 (T-Money, Snapper+) transit card"},
+    {"initialize", CmdHFKSX6924Initialize, IfPm3Iso14443a,  "Perform transaction initialization (Mpda)"},
+    {"prec",       CmdHFKSX6924PRec,       IfPm3Iso14443a,  "Send proprietary get record command (CLA=90, INS=4C)"},
+    {"select",     CmdHFKSX6924Select,     IfPm3Iso14443a,  "Select application, and leave field up"},
     {NULL, NULL, NULL, NULL}
 };
 
-int CmdHFKSX6924(const char *Cmd) {
-    (void)WaitForResponseTimeout(CMD_ACK, NULL, 100);
-    CmdsParse(CommandTable, Cmd);
-    return 0;
-}
-
 static int CmdHelp(const char *Cmd) {
+    (void)Cmd; // Cmd is not used so far
     CmdsHelp(CommandTable);
-    return 0;
+    return PM3_SUCCESS;
 }
 
+int CmdHFKSX6924(const char *Cmd) {
+    clearCommandBuffer();
+    return CmdsParse(CommandTable, Cmd);
+}
+
+
+
diff --git a/client/src/cmdhfksx6924.h b/client/src/cmdhfksx6924.h
index 8364dfb63..3917479f8 100644
--- a/client/src/cmdhfksx6924.h
+++ b/client/src/cmdhfksx6924.h
@@ -1,4 +1,3 @@
-// -*- mode: c; indent-tabs-mode: nil; tab-width: 3 -*-
 //-----------------------------------------------------------------------------
 // Copyright (C) 2019 micolous+git@gmail.com
 //
diff --git a/client/src/ksx6924/ksx6924core.c b/client/src/ksx6924/ksx6924core.c
index e538ce28c..8685934b0 100644
--- a/client/src/ksx6924/ksx6924core.c
+++ b/client/src/ksx6924/ksx6924core.c
@@ -1,4 +1,3 @@
-// -*- mode: c; indent-tabs-mode: nil; tab-width: 3 -*-
 //-----------------------------------------------------------------------------
 // Copyright (C) 2019 micolous+git@gmail.com
 //
@@ -21,11 +20,13 @@
 //-----------------------------------------------------------------------------
 
 #include "ksx6924core.h"
+
 #ifdef _WIN32
 # include <winsock2.h> // ntohl
 #else
 # include <arpa/inet.h> // ntohl
 #endif
+
 #include <string.h>
 #include "emv/emvcore.h"
 #include "iso7816/apduinfo.h"
@@ -230,43 +231,39 @@ static int64_t bcdToLong(const uint8_t *buf, size_t len) {
 /**
  * Converts a date from on-card format to ksx6924_date format.
  */
-static bool convertInternalDate(
-    const _ksx6924_internal_date_t i, struct ksx6924_date *ret) {
+static bool convert_internal_date( const _ksx6924_internal_date_t i, struct ksx6924_date *ret) {
+
     int64_t year = bcdToLong(i.year, 2);
     int16_t month = bcdToInteger(i.month[0]);
     int16_t day = bcdToInteger(i.day[0]);
 
-    if (year < 0 || year > 0xffff || month < 0 || day < 0) {
-        goto fail;
+    if (year < 0 || year > 0xFFFF || month < 0 || day < 0) {
+        memset(ret, 0, sizeof(struct ksx6924_date));
+        return false;
     }
 
-    ret->year = year & 0xffff;
+    ret->year = year & 0xFFFF;
     ret->month = month;
     ret->day = day;
     return true;
-
-fail:
-    memset(ret, 0, sizeof(struct ksx6924_date));
-    return false;
 }
 
 
 /**
  * Parses purse info in FCI tag b0
  */
-bool KSX6924ParsePurseInfo(const uint8_t *purseInfo, size_t purseLen,
-                           struct ksx6924_purse_info *ret) {
+bool KSX6924ParsePurseInfo(const uint8_t *purseInfo, size_t purseLen, struct ksx6924_purse_info *ret) {
+
+    memset(ret, 0, sizeof(struct ksx6924_purse_info));
+
     if (purseLen != sizeof(_ksx6924_internal_purse_info_t)) {
         // Invalid size!
-        PrintAndLogEx(NORMAL, "Expected %ld bytes, got %ld\n",
-                      sizeof(_ksx6924_internal_purse_info_t), purseLen);
-        goto fail;
+        PrintAndLogEx(FAILED, "Expected %ld bytes, got %ld\n", sizeof(_ksx6924_internal_purse_info_t), purseLen);
+        return false;
     }
 
     const _ksx6924_internal_purse_info_t *internalPurseInfo = (const _ksx6924_internal_purse_info_t *)purseInfo;
 
-    memset(ret, 0, sizeof(struct ksx6924_purse_info));
-
     // Simple copies
     ret->cardType = internalPurseInfo->cardType;
     ret->alg = internalPurseInfo->alg;
@@ -279,9 +276,12 @@ bool KSX6924ParsePurseInfo(const uint8_t *purseInfo, size_t purseLen,
 
     // Fields that need rewriting
     hex_to_buffer(ret->csn, internalPurseInfo->csn,
-                  sizeof(internalPurseInfo->csn), sizeof(ret->csn) - 1,
-                  /* min_str_len */ 0, /* spaces_between */ 0,
-                  /* uppercase */ false);
+                  sizeof(internalPurseInfo->csn),
+                  sizeof(ret->csn) - 1,
+                  0,    // min_str_len
+                  0,    // spaces_between
+                  false // uppercase
+                  );
 
     int64_t idtr = bcdToLong(internalPurseInfo->idtr, 5);
     if (idtr < 0) {
@@ -293,95 +293,88 @@ bool KSX6924ParsePurseInfo(const uint8_t *purseInfo, size_t purseLen,
     if (bra < 0) {
         bra = 0; // fail
     }
-    ret->bra = bra & 0xffff;
 
-    convertInternalDate(internalPurseInfo->issueDate, &(ret->issueDate));
-    convertInternalDate(internalPurseInfo->expiryDate, &(ret->expiryDate));
+    ret->bra = bra & 0xFFFF;
+
+    convert_internal_date(internalPurseInfo->issueDate, &(ret->issueDate));
+    convert_internal_date(internalPurseInfo->expiryDate, &(ret->expiryDate));
 
     ret->balMax = ntohl(*(uint32_t *)(internalPurseInfo->balMax));
     ret->mmax = ntohl(*(uint32_t *)(internalPurseInfo->mmax));
+
     memcpy(&ret->rfu, &internalPurseInfo->rfu, 8);
 
     // TODO
     return true;
-
-fail:
-    memset(ret, 0, sizeof(struct ksx6924_purse_info));
-    return false;
 };
 
 /**
  * Prints out a ksx6924_purse_info
  */
 void KSX6924PrintPurseInfo(const struct ksx6924_purse_info *purseInfo) {
+
     if (purseInfo == NULL) {
         return;
     }
 
-    PrintAndLogEx(NORMAL, "## KS X 6924 Purse Info:");
-    PrintAndLogEx(NORMAL, "");
-    PrintAndLogEx(NORMAL, "cardType .............................. %02x (%s)",
-                  purseInfo->cardType,
+    PrintAndLogEx(INFO, "--- " _CYAN_("KS X 6924 Purse Info") " ---------------------------");
+    PrintAndLogEx(INFO, "");
+    PrintAndLogEx(INFO, "  cardType .............................. %02x ( %s )", purseInfo->cardType,
                   KSX6924LookupCardType(purseInfo->cardType, KSX6924_UNKNOWN));
-    PrintAndLogEx(NORMAL, "alg (encryption algorithm) ............ %02x (%s)",
-                  purseInfo->alg,
+    PrintAndLogEx(INFO, "  alg (encryption algorithm) ............ %02x ( %s )", purseInfo->alg,
                   KSX6924LookupAlg(purseInfo->alg, KSX6924_UNKNOWN));
-    PrintAndLogEx(NORMAL, "vk (keyset version) ................... %02x",
-                  purseInfo->vk);
-    PrintAndLogEx(NORMAL, "idCenter (issuer ID) .................. %02x (%s)",
-                  purseInfo->idCenter,
+    PrintAndLogEx(INFO, "  vk (keyset version) ................... %02x", purseInfo->vk);
+    PrintAndLogEx(INFO, "  idCenter (issuer ID) .................. %02x ( %s )", purseInfo->idCenter,
                   KSX6924LookupTMoneyIDCenter(purseInfo->idCenter, KSX6924_UNKNOWN));
-    PrintAndLogEx(NORMAL, "csn (card number) ..................... %s",
-                  purseInfo->csn);
-    PrintAndLogEx(NORMAL, "idtr (card usage authentication ID) ... %i",
-                  purseInfo->idtr);
-    PrintAndLogEx(NORMAL, "issueDate ............................. %04i-%02i-%02i",
-                  purseInfo->issueDate.year, purseInfo->issueDate.month, purseInfo->issueDate.day);
-    PrintAndLogEx(NORMAL, "expiryDate ............................ %04i-%02i-%02i",
-                  purseInfo->expiryDate.year, purseInfo->expiryDate.month, purseInfo->expiryDate.day);
-    PrintAndLogEx(NORMAL, "userCode (ticket type) ................ %02x (%s)",
-                  purseInfo->userCode,
+    PrintAndLogEx(INFO, "  CSN (card number) ..................... %s", purseInfo->csn);
+    PrintAndLogEx(INFO, "  idtr (card usage authentication ID) ... %i", purseInfo->idtr);
+    PrintAndLogEx(INFO, "  issue date ............................ %04i-%02i-%02i",
+                  purseInfo->issueDate.year,
+                  purseInfo->issueDate.month,
+                  purseInfo->issueDate.day);
+    PrintAndLogEx(INFO, "  expiry date ........................... %04i-%02i-%02i",
+                  purseInfo->expiryDate.year,
+                  purseInfo->expiryDate.month,
+                  purseInfo->expiryDate.day);
+    PrintAndLogEx(INFO, "  user code (ticket type) ............... %02x ( %s )", purseInfo->userCode,
                   KSX6924LookupTMoneyUserCode(purseInfo->userCode, KSX6924_UNKNOWN));
-    PrintAndLogEx(NORMAL, "disRate (discount type) ............... %02x (%s)",
-                  purseInfo->disRate,
+    PrintAndLogEx(INFO, "  disRate (discount type) ............... %02x ( %s )", purseInfo->disRate,
                   KSX6924LookupTMoneyDisRate(purseInfo->disRate, KSX6924_UNKNOWN));
-    PrintAndLogEx(NORMAL, "balMax (in won/cents) ................. %ld",
-                  purseInfo->balMax);
-    PrintAndLogEx(NORMAL, "bra (branch code) ..................... %04x",
-                  purseInfo->bra);
-    PrintAndLogEx(NORMAL, "mmax (one-time transaction limit) ..... %ld",
-                  purseInfo->mmax);
-    PrintAndLogEx(NORMAL, "tcode (telecom carrier ID) ............ %02x (%s)",
-                  purseInfo->tcode,
+    PrintAndLogEx(INFO, "  balMax (in won/cents) ................. %ld", purseInfo->balMax);
+    PrintAndLogEx(INFO, "  bra (branch code) ..................... %04x", purseInfo->bra);
+    PrintAndLogEx(INFO, "  mmax (one-time transaction limit) ..... %ld", purseInfo->mmax);
+    PrintAndLogEx(INFO, "  tcode (telecom carrier ID) ............ %02x ( %s )", purseInfo->tcode,
                   KSX6924LookupTMoneyTCode(purseInfo->tcode, KSX6924_UNKNOWN));
-    PrintAndLogEx(NORMAL, "ccode (credit card company ID) ........ %02x (%s)",
-                  purseInfo->ccode,
+    PrintAndLogEx(INFO, "  ccode (credit card company ID) ........ %02x ( %s )", purseInfo->ccode,
                   KSX6924LookupTMoneyCCode(purseInfo->ccode, KSX6924_UNKNOWN));
-    PrintAndLogEx(NORMAL, "rfu (reserved) ........................ %s",
-                  sprint_hex(purseInfo->rfu, sizeof(purseInfo->rfu)));
-    PrintAndLogEx(NORMAL, "");
+    PrintAndLogEx(INFO, "  rfu (reserved) ........................ %s", sprint_hex(purseInfo->rfu, sizeof(purseInfo->rfu)));
+    PrintAndLogEx(INFO, "");
 }
 
-
 /**
  * Selects the KS X 6924 Application, D4100000030001, and returns the response
  * data.
  */
-int KSX6924Select(
-    bool ActivateField, bool LeaveFieldON,
-    uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw) {
+int KSX6924Select(bool ActivateField, bool LeaveFieldON, uint8_t *Result, size_t MaxResultLen, size_t *ResultLen, uint16_t *sw) {
+
     // T-Money + Snapper
     uint8_t aid[] = {0xD4, 0x10, 0x00, 0x00, 0x03, 0x00, 0x01};
 
     // Cashbee
     //uint8_t aid[] = {0xD4, 0x10, 0x00, 0x00, 0x14, 0x00, 0x01};
-
-
-    return EMVSelect(CC_CONTACTLESS, ActivateField, LeaveFieldON, aid,
-                     sizeof(aid), Result, MaxResultLen, ResultLen, sw, NULL);
+    return EMVSelect(CC_CONTACTLESS,
+                     ActivateField,
+                     LeaveFieldON,
+                     aid,
+                     sizeof(aid),
+                     Result,
+                     MaxResultLen,
+                     ResultLen,
+                     sw,
+                     NULL
+            );
 }
 
-
 /**
  * Selects the KS X 6924 Application. Returns true if selected successfully.
  */
@@ -398,8 +391,11 @@ bool KSX6924TrySelect(void) {
 
     if (sw != 0x9000) {
         if (sw) {
-            PrintAndLogEx(FAILED, "Not a KS X 6924 card! APDU response: %04x - %s",
-                          sw, GetAPDUCodeDescription(sw >> 8, sw & 0xff));
+            PrintAndLogEx(FAILED,
+                          "Not a KS X 6924 card! APDU response: %04x - %s",
+                          sw,
+                          GetAPDUCodeDescription(sw >> 8, sw & 0xff)
+                );
         } else {
             PrintAndLogEx(FAILED, "APDU exchange error. Card returns 0x0000.");
         }
@@ -418,30 +414,29 @@ bool KSX6924GetBalance(uint32_t *result) {
         return false;
     }
 
-    uint16_t sw;
-    size_t result_len;
-    uint8_t rawResult[4 /* message */ + 2 /* sw */];
-    //uint8_t apdu[] = {0x90, 0x4c, 0x00, 0x00, 4, rawResult};
-    memset(rawResult, 0, sizeof(rawResult));
+    *result = 0;
+
+    uint8_t arr[4 + 2]; // message , sw
+    //uint8_t apdu[] = {0x90, 0x4c, 0x00, 0x00, 4, arr};
+    memset(arr, 0, sizeof(arr));
+
     uint8_t data[] = {0x00, 0x00, 0x00, 0x00};
-    int res = FIDOExchange((sAPDU_t) {0x90, 0x4c, 0x00, 0x00, 4, data}, rawResult, sizeof(rawResult),
-    &result_len, &sw);
+
+    uint16_t sw = 0;
+    size_t rlen = 0;
+
+    int res = FIDOExchange((sAPDU_t) {0x90, 0x4c, 0x00, 0x00, 4, data}, arr, sizeof(arr), &rlen, &sw);
+
     if (res) {
-        // error communicating
-        goto fail;
+        return false;
     }
 
     if (sw != 0x9000) {
-        // card returned error
-        goto fail;
+        return false;
     }
 
-    *result = ntohl(*(uint32_t *)(rawResult));
-    return true;
-
-fail:
-    *result = 0;
-    return false;
+    *result = ntohl(*(uint32_t *)(arr));
+    return true;   
 }
 
 
@@ -449,34 +444,32 @@ fail:
  * Perform transaction initialization.
  */
 bool KSX6924InitializeCard(uint8_t mpda1, uint8_t mpda2, uint8_t mpda3, uint8_t mpda4, uint8_t *result) {
+
     if (result == NULL) {
         return false;
     }
 
-    uint16_t sw;
-    size_t result_len;
-    uint8_t rawResult[1 /* ALGep */ + 1 /* VKep */ + 4 /* BALep */ + 1 /* IDcenter */ + 8 /* IDep */ + 4 /* NTep */ + 4 /* Sign1 */ + 2 /* sw */];
-    memset(rawResult, 0, sizeof(rawResult));
+    *result = 0;
+    uint16_t sw = 0;
+    size_t rlen = 0;
+
+    //  ALGep +  VKep + BALep + IDcenter + IDep + NTep + Sign1 +  sw 
+    uint8_t arr[1 + 1 + 4 + 1 + 8 + 4 + 4 + 2];
+    memset(arr, 0, sizeof(arr));
+
     uint8_t data[] = {mpda1, mpda2, mpda3, mpda4};
-    int res = FIDOExchange((sAPDU_t) {0x90, 0x02, 0x00, 0x00, 0x04, data}, rawResult, sizeof(rawResult),
-    &result_len, &sw);
+    int res = FIDOExchange((sAPDU_t) {0x90, 0x02, 0x00, 0x00, 0x04, data}, arr, sizeof(arr), &rlen, &sw);
     if (res) {
-        // error communicating
-        goto fail;
+        return false;
     }
 
     if (sw != 0x9000) {
-        // card returned error
-        goto fail;
+        return false;
     }
 
-    //*result = ntohl(*(uint32_t*)(rawResult));
-    memcpy(result, rawResult, result_len + 2 /* sw */);
+    //*result = ntohl(*(uint32_t*)(arr));
+    memcpy(result, arr, rlen + 2); // skip 2 sw bytes
     return true;
-
-fail:
-    *result = 0;
-    return false;
 }
 
 
@@ -491,38 +484,29 @@ fail:
  *
  * Returns false on error.
  */
-bool KSX6924ProprietaryGetRecord(uint8_t id, uint8_t *result,
-                                 size_t resultLen) {
+bool KSX6924ProprietaryGetRecord(uint8_t id, uint8_t *result, size_t result_len) {
     if (result == NULL) {
         return false;
     }
-    memset(result, 0, resultLen);
+    memset(result, 0, result_len);
 
-    uint16_t sw;
-    size_t result_len;
+    uint8_t arr[result_len + 2]; // message + sw
+    memset(arr, 0, sizeof(arr));
 
-    uint8_t rawResult[resultLen /* message */ + 2 /* sw */];
-    memset(rawResult, 0, sizeof(rawResult));
+    uint16_t sw = 0;
+    size_t rlen = 0;
 
-    //uint8_t apdu[] = {0x90, 0x78, id, 0x00, resultLen, rawResult};
-    int res = FIDOExchange((sAPDU_t) {0x90, 0x78, id, 0x00, resultLen, rawResult}, rawResult, sizeof(rawResult),
-    &result_len, &sw);
+    //uint8_t apdu[] = {0x90, 0x78, id, 0x00, resultLen, arr};
+    int res = FIDOExchange((sAPDU_t) {0x90, 0x78, id, 0x00, result_len, arr}, arr, sizeof(arr), &rlen, &sw);
     if (res) {
-        // error communicating
-        goto fail;
+        return false;
     }
 
     if (sw != 0x9000) {
-        // card returned error
-        goto fail;
+        return false;
     }
 
-    // Copy into the actual result buffer
-    memcpy(result, rawResult, resultLen);
+    memcpy(result, arr, result_len);
     return true;
-
-fail:
-    memset(result, 0, resultLen);
-    return false;
 }
 
diff --git a/client/src/ksx6924/ksx6924core.h b/client/src/ksx6924/ksx6924core.h
index 700fa90e6..6e9bccc74 100644
--- a/client/src/ksx6924/ksx6924core.h
+++ b/client/src/ksx6924/ksx6924core.h
@@ -1,4 +1,3 @@
-// -*- mode: c; indent-tabs-mode: nil; tab-width: 3 -*-
 //-----------------------------------------------------------------------------
 // Copyright (C) 2019 micolous+git@gmail.com
 //
