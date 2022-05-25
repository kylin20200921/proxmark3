commit 581232217f31f8d89024e5faa62fdc7f498f3093
Author: iceman1001 <iceman@iuse.se>
Date:   Wed Jan 26 06:17:28 2022 +0100

    added a AID and some minor style

diff --git a/client/resources/aidlist.json b/client/resources/aidlist.json
index 82e31b8d4..0188397f3 100644
--- a/client/resources/aidlist.json
+++ b/client/resources/aidlist.json
@@ -2198,5 +2198,13 @@
         "Name": "CPM Person SAM",
         "Description": "Personalization of end user cards",
         "Type": "transport"
+    },
+    {
+        "AID": "A0000008381010",
+        "Vendor": "SL ",
+        "Country": "Sweden",
+        "Name": "SL Resekort",
+        "Description": "transport card",
+        "Type": "transport"
     }
 ]
diff --git a/client/src/emv/cmdemv.c b/client/src/emv/cmdemv.c
index 10aa7e5a0..837f4619e 100644
--- a/client/src/emv/cmdemv.c
+++ b/client/src/emv/cmdemv.c
@@ -151,9 +151,12 @@ static int CmdEMVSearch(const char *Cmd) {
     bool leaveSignalON = arg_get_lit(ctx, 2);
     bool APDULogging = arg_get_lit(ctx, 3);
     bool decodeTLV = arg_get_lit(ctx, 4);
+
     Iso7816CommandChannel channel = CC_CONTACTLESS;
-    if (arg_get_lit(ctx, 5))
+    if (arg_get_lit(ctx, 5)) {
         channel = CC_CONTACT;
+    }
+
     PrintChannel(channel);
     CLIParserFree(ctx);
 
@@ -170,7 +173,7 @@ static int CmdEMVSearch(const char *Cmd) {
     PrintAndLogEx(SUCCESS, "Search completed.");
 
     // print list here
-    if (!decodeTLV) {
+    if (decodeTLV == false) {
         TLVPrintAIDlistFromSelectTLV(t);
     }
 
@@ -841,17 +844,23 @@ static int CmdEMVExec(const char *Cmd) {
     bool forceSearch = arg_get_lit(ctx, 5);
 
     enum TransactionType TrType = TT_MSD;
+
     if (arg_get_lit(ctx, 7))
         TrType = TT_QVSDCMCHIP;
+
     if (arg_get_lit(ctx, 8))
         TrType = TT_CDA;
+
     if (arg_get_lit(ctx, 9))
         TrType = TT_VSDC;
 
     bool GenACGPO = arg_get_lit(ctx, 10);
+
     Iso7816CommandChannel channel = CC_CONTACTLESS;
-    if (arg_get_lit(ctx, 11))
+    if (arg_get_lit(ctx, 11)) {
         channel = CC_CONTACT;
+    }
+
     PrintChannel(channel);
     uint8_t psenum = (channel == CC_CONTACT) ? 1 : 2;
     CLIParserFree(ctx);
diff --git a/client/src/emv/emv_tags.c b/client/src/emv/emv_tags.c
index ce2872254..7c80aa94f 100644
--- a/client/src/emv/emv_tags.c
+++ b/client/src/emv/emv_tags.c
@@ -467,6 +467,7 @@ static void emv_tag_dump_bitmask(const struct tlv *tlv, const struct emv_tag *ta
         unsigned char val = tlv->value[byte - 1];
         PrintAndLogEx(INFO, "%*s" NOLF, (level * 4), " ");
         PrintAndLogEx(NORMAL, "    Byte %u (%02x)", byte, val);
+        
         for (bit = 8; bit > 0; bit--, val <<= 1) {
             if (val & 0x80) {
                 PrintAndLogEx(INFO, "%*s" NOLF, (level * 4), " ");
@@ -490,8 +491,7 @@ static void emv_tag_dump_dol(const struct tlv *tlv, const struct emv_tag *tag, i
         const struct emv_tag *doltag;
 
         if (!tlv_parse_tl(&buf, &left, &doltlv)) {
-            PrintAndLogEx(INFO, "%*s" NOLF, (level * 4), " ");
-            PrintAndLogEx(NORMAL, "Invalid Tag-Len");
+            PrintAndLogEx(INFO, "%*sInvalid Tag-Len" , (level * 4), " ");
             continue;
         }
 
@@ -503,7 +503,7 @@ static void emv_tag_dump_dol(const struct tlv *tlv, const struct emv_tag *tag, i
 }
 
 static void emv_tag_dump_string(const struct tlv *tlv, const struct emv_tag *tag, int level) {
-    PrintAndLogEx(NORMAL, "    String value '%s'", sprint_hex_inrow(tlv->value, tlv->len));
+    PrintAndLogEx(NORMAL, "    String value '" _YELLOW_("%s")"'", sprint_hex_inrow(tlv->value, tlv->len));
 }
 
 static unsigned long emv_value_numeric(const struct tlv *tlv, unsigned start, unsigned end) {
@@ -538,12 +538,12 @@ static unsigned long emv_value_numeric(const struct tlv *tlv, unsigned start, un
 
 static void emv_tag_dump_numeric(const struct tlv *tlv, const struct emv_tag *tag, int level) {
     PrintAndLogEx(INFO, "%*s" NOLF, (level * 4), " ");
-    PrintAndLogEx(NORMAL, "    Numeric value %lu", emv_value_numeric(tlv, 0, tlv->len * 2));
+    PrintAndLogEx(NORMAL, "    Numeric value " _YELLOW_("%lu"), emv_value_numeric(tlv, 0, tlv->len * 2));
 }
 
 static void emv_tag_dump_yymmdd(const struct tlv *tlv, const struct emv_tag *tag, int level) {
     PrintAndLogEx(INFO, "%*s" NOLF, (level * 4), " ");
-    PrintAndLogEx(NORMAL, "    Date: 20%02lu.%lu.%lu",
+    PrintAndLogEx(NORMAL, "    Date: " _YELLOW_("20%02lu.%lu.%lu"),
                   emv_value_numeric(tlv, 0, 2),
                   emv_value_numeric(tlv, 2, 4),
                   emv_value_numeric(tlv, 4, 6)
@@ -557,14 +557,12 @@ static uint32_t emv_get_binary(const unsigned char *S) {
 // https://github.com/binaryfoo/emv-bertlv/blob/master/src/main/resources/fields/visa-cvr.txt
 static void emv_tag_dump_cvr(const struct tlv *tlv, const struct emv_tag *tag, int level) {
     if (tlv == NULL || tlv->len < 1) {
-        PrintAndLogEx(INFO, "%*s" NOLF, (level * 4), " ");
-        PrintAndLogEx(NORMAL, "    INVALID length!");
+        PrintAndLogEx(INFO, "%*s    INVALID length!" , (level * 4), " ");
         return;
     }
 
     if (tlv->len != 5 && tlv->len != tlv->value[0] + 1) {
-        PrintAndLogEx(INFO, "%*s" NOLF, (level * 4), " ");
-        PrintAndLogEx(NORMAL, "    INVALID length!");
+        PrintAndLogEx(INFO, "%*s    INVALID length!", (level * 4), " ");
         return;
     }
 
@@ -633,8 +631,7 @@ static void emv_tag_dump_cvr(const struct tlv *tlv, const struct emv_tag *tag, i
 // EMV Book 3
 static void emv_tag_dump_cid(const struct tlv *tlv, const struct emv_tag *tag, int level) {
     if (tlv == NULL || tlv->len < 1) {
-        PrintAndLogEx(INFO, "%*s" NOLF, (level * 4), " ");
-        PrintAndLogEx(NORMAL, "    INVALID!");
+        PrintAndLogEx(INFO, "%*s    INVALID!", (level * 4), " ");
         return;
     }
 
@@ -682,8 +679,7 @@ static void emv_tag_dump_cvm_list(const struct tlv *tlv, const struct emv_tag *t
     int i;
 
     if (tlv->len < 10 || tlv->len % 2) {
-        PrintAndLogEx(INFO, "%*s" NOLF, (level * 4), " ");
-        PrintAndLogEx(NORMAL, "    INVALID!");
+        PrintAndLogEx(INFO, "%*s    INVALID!", (level * 4), " ");
         return;
     }
 
@@ -781,8 +777,7 @@ static void emv_tag_dump_cvm_list(const struct tlv *tlv, const struct emv_tag *t
 
 static void emv_tag_dump_afl(const struct tlv *tlv, const struct emv_tag *tag, int level) {
     if (tlv->len < 4 || tlv->len % 4) {
-        PrintAndLogEx(INFO, "%*s" NOLF, (level * 4), " ");
-        PrintAndLogEx(NORMAL, "    INVALID!");
+        PrintAndLogEx(INFO, "%*s    INVALID!", (level * 4), " ");
         return;
     }
 
diff --git a/client/src/emv/emvcore.c b/client/src/emv/emvcore.c
index 6cb6107d0..324ffe097 100644
--- a/client/src/emv/emvcore.c
+++ b/client/src/emv/emvcore.c
@@ -137,6 +137,7 @@ static const AIDList_t AIDlist [] = {
     { CV_OTHER, "D5280050218002" },              // The Netherlands - ? - (Netherlands)
     { CV_OTHER, "D5780000021010" },              // Bankaxept    Norway  Bankaxept   Norwegian domestic debit card
     { CV_OTHER, "F0000000030001" },              // BRADESCO - Brazilian Bank Banco Bradesco
+    { CV_OTHER, "A0000008381010" },              // SL Resekort - Swedish domestic transportation card with payment
 };
 
 enum CardPSVendor GetCardPSVendor(uint8_t *AID, size_t AIDlen) {
@@ -496,7 +497,7 @@ int EMVSearch(Iso7816CommandChannel channel, bool ActivateField, bool LeaveField
             } else {
                 // (1) - card select error, (4) reply timeout, (200) - result length = 0
                 if (res == 1 || res == 4 || res == 200) {
-                    if (!LeaveFieldON)
+                    if (LeaveFieldON == false)
                         DropFieldEx(channel);
 
                     PrintAndLogEx(WARNING, "exiting...");
@@ -522,8 +523,9 @@ int EMVSearch(Iso7816CommandChannel channel, bool ActivateField, bool LeaveField
         }
     }
 
-    if (!LeaveFieldON)
+    if (LeaveFieldON == false) {
         DropFieldEx(channel);
+    }
 
     return 0;
 }
