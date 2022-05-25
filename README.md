commit 2c72763a5ad19b58018cbc39c24a2c8b1880996c
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Oct 10 19:01:49 2021 +0200

    fix resource leak

diff --git a/client/src/cmdcrc.c b/client/src/cmdcrc.c
index 9d1353d3c..65d1fe206 100644
--- a/client/src/cmdcrc.c
+++ b/client/src/cmdcrc.c
@@ -454,13 +454,14 @@ static int CmdrevengSearch(const char *Cmd) {
         memset(result, 0, 30);
         char *inCRC = calloc(crcChars + 1, sizeof(char));
         if (inCRC == NULL) {
-            return 0;
+            return 0;   
         }
 
         memcpy(inCRC, inHexStr + (dataLen - crcChars), crcChars);
 
         char *outHex = calloc(dataLen - crcChars + 1, sizeof(char));
         if (outHex == NULL) {
+            free(inCRC);
             return 0;
         }
 
diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index d6d841bdf..053b1f60a 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -33,6 +33,7 @@
 #include "nfc/ndef.h"      // NDEFRecordsDecodeAndPrint
 #include "cmdnfc.h"        // print_type4_cc_info
 #include "fileutils.h"     // saveFile
+#include "atrs.h"          // getATRinfo
 
 static bool APDUInFramingEnable = true;
 
@@ -504,8 +505,9 @@ static int CmdHF14AReader(const char *Cmd) {
                 if (card.ats_len >= 3) { // a valid ATS consists of at least the length byte (TL) and 2 CRC bytes
                     if (card.ats_len == card.ats[0] + 2)
                         PrintAndLogEx(SUCCESS, " ATS: "  _GREEN_("%s"), sprint_hex(card.ats, card.ats[0]));
-                    else
+                    else {
                         PrintAndLogEx(SUCCESS, " ATS: [%d] "  _GREEN_("%s"), card.ats_len, sprint_hex(card.ats, card.ats_len));
+                    }
                 }
             }
             if (!disconnectAfter) {
@@ -1992,7 +1994,13 @@ int infoHF14A(bool verbose, bool do_nack_test, bool do_aid_search) {
                         break;
                 }
             } else {
-                PrintAndLogEx(SUCCESS, "   %s", sprint_hex_inrow(card.ats + pos, calen));
+
+                if (card.ats[pos] == 0x80)
+                    PrintAndLogEx(SUCCESS, "   %s  (compact TLV data object)", sprint_hex_inrow(card.ats + pos, calen));
+                else
+                    PrintAndLogEx(SUCCESS, "   %s", sprint_hex_inrow(card.ats + pos, calen));
+
+                PrintAndLogEx(NORMAL, "");
             }
         }
 
