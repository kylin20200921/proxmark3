commit c98bdfe8578b92337676981a52025dbb20e82a58
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Feb 24 17:16:31 2022 +0100

    fixes to "hf cipurse info"

diff --git a/client/src/cipurse/cipursecore.c b/client/src/cipurse/cipursecore.c
index 738839b00..fc20c4854 100644
--- a/client/src/cipurse/cipursecore.c
+++ b/client/src/cipurse/cipursecore.c
@@ -17,16 +17,15 @@
 //-----------------------------------------------------------------------------
 
 #include "cipursecore.h"
+#include <string.h>      // memcpy memset
 
 #include "commonutil.h"  // ARRAYLEN
 #include "comms.h"       // DropField
 #include "util_posix.h"  // msleep
-#include <string.h>      // memcpy memset
-
 #include "cmdhf14a.h"
-#include "emv/emvcore.h"
-#include "emv/emvjson.h"
-#include "iso7816/apduinfo.h"
+#include "../emv/emvcore.h"
+#include "../emv/emvjson.h"
+#include "../iso7816/apduinfo.h"       // sAPDU_t
 #include "ui.h"
 #include "util.h"
 
@@ -299,21 +298,21 @@ void CIPURSECSetActChannelSecurityLevels(CipurseChannelSecurityLevel req, Cipurs
 
 static void CIPURSEPrintPersoMode(uint8_t data) {
     if ((data & 0x01) == 0x01)
-        PrintAndLogEx(INFO, "Perso... " _YELLOW_("filesystem"));
+        PrintAndLogEx(INFO, "Perso.......... " _YELLOW_("filesystem"));
     if ((data & 0x02) == 0x02)
-        PrintAndLogEx(INFO, "Perso... " _YELLOW_("EMV"));
+        PrintAndLogEx(INFO, "Perso.......... " _YELLOW_("EMV"));
     if ((data & 0x04) == 0x04)
-        PrintAndLogEx(INFO, "Perso... " _YELLOW_("transaction supported"));
+        PrintAndLogEx(INFO, "Perso.......... " _YELLOW_("transaction supported"));
 }
 
 // 2021 iceman: what is the description text of profile L,S,T ?
 static void CIPURSEPrintProfileInfo(uint8_t data) {
     if ((data & 0x01) == 0x01)
-        PrintAndLogEx(INFO, "Profile... L");
+        PrintAndLogEx(INFO, "Profile........ L");
     if ((data & 0x02) == 0x02)
-        PrintAndLogEx(INFO, "Profile... S");
+        PrintAndLogEx(INFO, "Profile........ S");
     if ((data & 0x04) == 0x04)
-        PrintAndLogEx(INFO, "Profile... T");
+        PrintAndLogEx(INFO, "Profile........ T");
 }
 
 static void CIPURSEPrintManufacturerInfo(uint8_t data) {
@@ -330,8 +329,8 @@ void CIPURSEPrintInfoFile(uint8_t *data, size_t len) {
     }
 
     PrintAndLogEx(INFO, "--- " _CYAN_("CIPURSE Information") "---------------------");
-    PrintAndLogEx(INFO, "version.... " _YELLOW_("%d"), data[0]);
-    PrintAndLogEx(INFO, "revision... " _YELLOW_("%d"), data[1]);
+    PrintAndLogEx(INFO, "version........ " _YELLOW_("%d"), data[0]);
+    PrintAndLogEx(INFO, "revision....... " _YELLOW_("%d"), data[1]);
 
     if (len >= 3)
         CIPURSEPrintPersoMode(data[2]);
diff --git a/client/src/cipurse/cipursecore.h b/client/src/cipurse/cipursecore.h
index 603bab765..f7bdd9be0 100644
--- a/client/src/cipurse/cipursecore.h
+++ b/client/src/cipurse/cipursecore.h
@@ -22,7 +22,7 @@
 #include <jansson.h>
 #include <stdbool.h>
 #include "common.h"
-#include "iso7816/apduinfo.h"       // sAPDU_t
+#include "../iso7816/apduinfo.h"       // sAPDU_t
 #include "cipurse/cipursecrypto.h"
 
 
diff --git a/client/src/cmdhfcipurse.c b/client/src/cmdhfcipurse.c
index ffbed5bd4..eb3453eec 100644
--- a/client/src/cmdhfcipurse.c
+++ b/client/src/cmdhfcipurse.c
@@ -113,7 +113,11 @@ static int SelectAndPrintInfoFile(void) {
 
     if (len > 0) {
         PrintAndLogEx(INFO, "Info file ( " _GREEN_("ok") " )");
-        PrintAndLogEx(INFO, "[%zu]: %s", len, sprint_hex(buf, len));
+
+        PrintAndLogEx(INFO, " # | bytes                                           | ascii");
+        PrintAndLogEx(INFO, "---+-------------------------------------------------+-----------------");
+        print_hex_break(buf, len, 16);
+        PrintAndLogEx(NORMAL, "");
         CIPURSEPrintInfoFile(buf, len);
         PrintAndLogEx(INFO, "");
     }
@@ -149,7 +153,7 @@ static int CmdHFCipurseInfo(const char *Cmd) {
     int res = CIPURSESelectMFEx(true, true, buf, sizeof(buf), &len, &sw);
     if (res == PM3_SUCCESS && sw == 0x9000) {
         mfExist = true;
-        PrintAndLogEx(INFO, _CYAN_("MasterFile") " exist and can be selected.");
+        PrintAndLogEx(INFO, _YELLOW_("MasterFile") " exist and can be selected.");
 
         res = SelectAndPrintInfoFile();
         infoPrinted = (res == PM3_SUCCESS);
@@ -173,7 +177,7 @@ static int CmdHFCipurseInfo(const char *Cmd) {
         DropField();
         return res;
     }
-    PrintAndLogEx(INFO, "Application `AF F1` selected " _GREEN_("successfully"));
+    PrintAndLogEx(INFO, "Application `" _YELLOW_("AF F1") "` selected " _GREEN_("successfully"));
 
     if (sw != 0x9000) {
         if (sw == 0x0000) {
@@ -1276,7 +1280,8 @@ static int CmdHFCipurseCreateDGI(const char *Cmd) {
 
     res = CIPURSECreateFile(hdata, hdatalen, buf, sizeof(buf), &len, &sw);
     if (res != 0 || sw != 0x9000) {
-        PrintAndLogEx(ERR, "Create file command " _RED_("ERROR") ". Card returns:\n  0x%04x - %s", sw,
+        PrintAndLogEx(ERR, "Create file command " _RED_("ERROR"));
+        PrintAndLogEx(ERR, "0x%04x - %s", sw,
                       GetSpecificAPDUCodeDesc(SelectAPDUCodeDescriptions, ARRAYLEN(SelectAPDUCodeDescriptions), sw));
         DropField();
         return PM3_ESOFT;
@@ -1403,7 +1408,8 @@ static int CmdHFCipurseDeleteFile(const char *Cmd) {
     if (useChildFID) {
         res = CIPURSEDeleteFile(childFileId, buf, sizeof(buf), &len, &sw);
         if (res != 0 || sw != 0x9000) {
-            PrintAndLogEx(ERR, "Delete child file " _CYAN_("%04x ") _RED_("ERROR") ". Card returns:\n  0x%04x - %s", childFileId, sw,
+            PrintAndLogEx(ERR, "Delete child file " _CYAN_("%04x ") _RED_("ERROR"));
+            PrintAndLogEx(ERR, "0x%04x - %s", childFileId, sw,
                           GetSpecificAPDUCodeDesc(DeleteAPDUCodeDescriptions, ARRAYLEN(DeleteAPDUCodeDescriptions), sw));
             DropField();
             return PM3_ESOFT;
@@ -1412,7 +1418,8 @@ static int CmdHFCipurseDeleteFile(const char *Cmd) {
     } else if (useFID) {
         res = CIPURSEDeleteFile(fileId, buf, sizeof(buf), &len, &sw);
         if (res != 0 || sw != 0x9000) {
-            PrintAndLogEx(ERR, "Delete file " _CYAN_("%04x ") _RED_("ERROR") ". Card returns:\n  0x%04x - %s", fileId, sw,
+            PrintAndLogEx(ERR, "Delete file " _CYAN_("%04x ") _RED_("ERROR"));
+            PrintAndLogEx(ERR, "0x%04x - %s", fileId, sw,
                           GetSpecificAPDUCodeDesc(DeleteAPDUCodeDescriptions, ARRAYLEN(DeleteAPDUCodeDescriptions), sw));
             DropField();
             return PM3_ESOFT;
@@ -1421,7 +1428,8 @@ static int CmdHFCipurseDeleteFile(const char *Cmd) {
     } else {
         res = CIPURSEDeleteFileAID(aid, aidLen, buf, sizeof(buf), &len, &sw);
         if (res != 0 || sw != 0x9000) {
-            PrintAndLogEx(ERR, "Delete application " _CYAN_("%s ") _RED_("ERROR") ". Card returns:\n  0x%04x - %s",
+            PrintAndLogEx(ERR, "Delete application " _CYAN_("%s ") _RED_("ERROR"));
+            PrintAndLogEx(ERR, "0x%04x - %s",
                           sprint_hex_inrow(aid, aidLen),
                           sw,
                           GetSpecificAPDUCodeDesc(DeleteAPDUCodeDescriptions, ARRAYLEN(DeleteAPDUCodeDescriptions), sw));
@@ -1617,7 +1625,8 @@ static int CmdHFCipurseUpdateKey(const char *Cmd) {
 
     res = CIPURSEUpdateKey(encKeyId, newKeyId, keydata, sizeof(keydata), buf, sizeof(buf), &len, &sw);
     if (res != 0 || sw != 0x9000) {
-        PrintAndLogEx(ERR, "Update key command " _RED_("ERROR") ". Card returns:\n  0x%04x - %s", sw,
+        PrintAndLogEx(ERR, "Update key command " _RED_("ERROR"));
+        PrintAndLogEx(ERR, "0x%04x - %s", sw,
                       GetSpecificAPDUCodeDesc(UAPDpdateKeyCodeDescriptions, ARRAYLEN(UAPDpdateKeyCodeDescriptions), sw));
         DropField();
         return PM3_ESOFT;
@@ -1764,7 +1773,8 @@ static int CmdHFCipurseUpdateKeyAttr(const char *Cmd) {
 
     res = CIPURSEUpdateKeyAttrib(trgKeyId, hdata[0], buf, sizeof(buf), &len, &sw);
     if (res != 0 || sw != 0x9000) {
-        PrintAndLogEx(ERR, "Update key attributes command " _RED_("ERROR") ". Card returns:\n  0x%04x - %s", sw,
+        PrintAndLogEx(ERR, "Update key attributes command " _RED_("ERROR"));
+        PrintAndLogEx(ERR, "0x%04x - %s", sw,
                       GetSpecificAPDUCodeDesc(UAPDpdateKeyAttrCodeDescriptions, ARRAYLEN(UAPDpdateKeyAttrCodeDescriptions), sw));
         DropField();
         return PM3_ESOFT;
