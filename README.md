commit 0d5b7864aa3d458010aa02b9cc1528e96c861f60
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Feb 25 21:03:22 2022 +0100

    coverity fixes

diff --git a/client/src/cmdhfcipurse.c b/client/src/cmdhfcipurse.c
index 95e6e7bad..a1c1c3eb9 100644
--- a/client/src/cmdhfcipurse.c
+++ b/client/src/cmdhfcipurse.c
@@ -377,8 +377,8 @@ static int SelectCommandEx(bool selectDefaultFile, bool useAID, uint8_t *aid, si
         res = CIPURSESelectFileEx(true, true, fileId, buf, bufSize, len, sw);
         if (res != 0 || *sw != 0x9000) {
             if (verbose) {
-                PrintAndLogEx(ERR, "Cipurse select file 0x%04x  ( %s )",  _RED_("fail"));
-                PrintAndLogEx(ERR, "Card returns 0x%04x", fileId, *sw);
+                PrintAndLogEx(ERR, "Cipurse select file 0x%04x  ( %s )", fileId, _RED_("fail"));
+                PrintAndLogEx(ERR, "Card returns 0x%04x", *sw);
             }
             return PM3_ESOFT;
         }
@@ -1406,9 +1406,11 @@ static int CmdHFCipurseDeleteFile(const char *Cmd) {
     if (useChildFID) {
         res = CIPURSEDeleteFile(childFileId, buf, sizeof(buf), &len, &sw);
         if (res != 0 || sw != 0x9000) {
-            PrintAndLogEx(ERR, "Delete child file " _CYAN_("%04x ") _RED_("ERROR"));
-            PrintAndLogEx(ERR, "0x%04x - %s", childFileId, sw,
-                          GetSpecificAPDUCodeDesc(DeleteAPDUCodeDescriptions, ARRAYLEN(DeleteAPDUCodeDescriptions), sw));
+            PrintAndLogEx(ERR, "Delete child file " _CYAN_("%04x ") " %s", childFileId, _RED_("ERROR"));
+            PrintAndLogEx(ERR, "0x%04x - %s", 
+                    sw,
+                    GetSpecificAPDUCodeDesc(DeleteAPDUCodeDescriptions, ARRAYLEN(DeleteAPDUCodeDescriptions), sw)
+                );
             DropField();
             return PM3_ESOFT;
         }
@@ -1416,9 +1418,11 @@ static int CmdHFCipurseDeleteFile(const char *Cmd) {
     } else if (useFID) {
         res = CIPURSEDeleteFile(fileId, buf, sizeof(buf), &len, &sw);
         if (res != 0 || sw != 0x9000) {
-            PrintAndLogEx(ERR, "Delete file " _CYAN_("%04x ") _RED_("ERROR"));
-            PrintAndLogEx(ERR, "0x%04x - %s", fileId, sw,
-                          GetSpecificAPDUCodeDesc(DeleteAPDUCodeDescriptions, ARRAYLEN(DeleteAPDUCodeDescriptions), sw));
+            PrintAndLogEx(ERR, "Delete file " _CYAN_("%04x ") " %s", fileId, _RED_("ERROR"));
+            PrintAndLogEx(ERR, "0x%04x - %s",
+                    sw,
+                    GetSpecificAPDUCodeDesc(DeleteAPDUCodeDescriptions, ARRAYLEN(DeleteAPDUCodeDescriptions), sw)
+                );
             DropField();
             return PM3_ESOFT;
         }
@@ -1426,11 +1430,11 @@ static int CmdHFCipurseDeleteFile(const char *Cmd) {
     } else {
         res = CIPURSEDeleteFileAID(aid, aidLen, buf, sizeof(buf), &len, &sw);
         if (res != 0 || sw != 0x9000) {
-            PrintAndLogEx(ERR, "Delete application " _CYAN_("%s ") _RED_("ERROR"));
+            PrintAndLogEx(ERR, "Delete application " _CYAN_("%s ") " %s", sprint_hex_inrow(aid, aidLen), _RED_("ERROR"));
             PrintAndLogEx(ERR, "0x%04x - %s",
-                          sprint_hex_inrow(aid, aidLen),
-                          sw,
-                          GetSpecificAPDUCodeDesc(DeleteAPDUCodeDescriptions, ARRAYLEN(DeleteAPDUCodeDescriptions), sw));
+                    sw,
+                    GetSpecificAPDUCodeDesc(DeleteAPDUCodeDescriptions, ARRAYLEN(DeleteAPDUCodeDescriptions), sw)
+                );
             DropField();
             return PM3_ESOFT;
         }
diff --git a/client/src/cmdhfmfdes.c b/client/src/cmdhfmfdes.c
index 5b9b851c2..21e779c8f 100644
--- a/client/src/cmdhfmfdes.c
+++ b/client/src/cmdhfmfdes.c
@@ -142,6 +142,7 @@ typedef enum {
     DESFIRE_MF3ICD40,
     DESFIRE_EV1,
     DESFIRE_EV2,
+    DESFIRE_EV2_XL,
     DESFIRE_EV3,
     DESFIRE_LIGHT,
     PLUS_EV1,
@@ -248,7 +249,7 @@ static char *getVersionStr(uint8_t major, uint8_t minor) {
     else if (major == 0x12 && minor == 0x00)
         snprintf(retStr, sizeof(buf), "%x.%x ( " _GREEN_("DESFire EV2") " )", major, minor);
     else if (major == 0x22 && minor == 0x00)
-        snprintf(retStr, sizeof(buf), "%x.%x ( " _GREEN_("DESFire EV2") " )", major, minor);
+        snprintf(retStr, sizeof(buf), "%x.%x ( " _GREEN_("DESFire EV2 XL") " )", major, minor);
     else if (major == 0x42 && minor == 0x00)
         snprintf(retStr, sizeof(buf), "%x.%x ( " _GREEN_("DESFire EV2") " )", major, minor);
     else if (major == 0x33 && minor == 0x00)
@@ -283,7 +284,7 @@ static nxp_cardtype_t getCardType(uint8_t major, uint8_t minor) {
     if (major == 0x12 && minor == 0x00)
         return DESFIRE_EV2;
     if (major == 0x22 && minor == 0x00)
-        return DESFIRE_EV2;
+        return DESFIRE_EV2_XL;
     if (major == 0x42 && minor == 0x00)
         return DESFIRE_EV2;
     if (major == 0x33 && minor == 0x00)
@@ -661,6 +662,8 @@ static int CmdHF14ADesInfo(const char *Cmd) {
         PrintAndLogEx(INFO, "\t1.4 - DESFire Ev1 MF3ICD21/41/81, EAL4+");
     if (major == 2 && minor == 0)
         PrintAndLogEx(INFO, "\t2.0 - DESFire Ev2, Originality check, proximity check, EAL5");
+    if (major == 2 && minor == 2)
+        PrintAndLogEx(INFO, "\t2.2 - DESFire Ev2 XL, Originality check, proximity check, EAL5");
     if (major == 3 && minor == 0)
         PrintAndLogEx(INFO, "\t3.0 - DESFire Ev3, Originality check, proximity check, badass EAL6 ?");
 
@@ -2179,24 +2182,40 @@ static int CmdHF14ADesSetConfiguration(const char *Cmd) {
     CLIParserFree(ctx);
 
     if (verbose) {
-        if (DesfireMFSelected(selectway, id))
-            PrintAndLogEx(INFO, _CYAN_("PICC") " param ID: 0x%02x param[%d]: %s", paramid, paramlen, sprint_hex(param, paramlen));
-        else
-            PrintAndLogEx(INFO, _CYAN_("%s %06x") " param ID: 0x%02x param[%d]: %s", DesfireSelectWayToStr(selectway), id, paramid, paramlen, sprint_hex(param, paramlen));
+        if (DesfireMFSelected(selectway, id)) {
+            PrintAndLogEx(INFO, _CYAN_("PICC") " param ID: 0x%02x param[%d]: %s",
+                    paramid,
+                    paramlen,
+                    sprint_hex(param, paramlen)
+                );
+        } else {
+            PrintAndLogEx(INFO, _CYAN_("%s %06x") " param ID: 0x%02x param[%d]: %s", 
+                    DesfireSelectWayToStr(selectway),
+                    id,
+                    paramid,
+                    paramlen,
+                    sprint_hex(param, paramlen)
+                );
+        }
     }
 
     res = DesfireSelectAndAuthenticateAppW(&dctx, securechann, selectway, id, false, verbose);
     if (res != PM3_SUCCESS) {
         DropField();
-        PrintAndLogEx(FAILED, "Select or authentication ( %s )" _RED_("failed") " Result [%d] %s", DesfireWayIDStr(selectway, id), res, DesfireAuthErrorToStr(res));
+        PrintAndLogEx(FAILED, "Select or authentication ( %s ) Result [%d] %s %s", 
+                DesfireWayIDStr(selectway, id),
+                res,
+                DesfireAuthErrorToStr(res),
+                _RED_("failed")
+            );
         return res;
     }
 
     res = DesfireSetConfiguration(&dctx, paramid, param, paramlen);
     if (res == PM3_SUCCESS) {
-        PrintAndLogEx(SUCCESS, "Set configuration 0x%02x ( %s )", _GREEN_("ok"), paramid);
+        PrintAndLogEx(SUCCESS, "Set configuration 0x%02x ( %s )", paramid, _GREEN_("ok"));
     } else {
-        PrintAndLogEx(FAILED, "Set configuration 0x%02x ( %s )", _RED_("failed"), paramid);
+        PrintAndLogEx(FAILED, "Set configuration 0x%02x ( %s )", paramid, _RED_("failed"));
     }
 
     DropField();
@@ -2605,7 +2624,7 @@ static int CmdHF14ADesGetUID(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mfdes getuid",
                   "Get UID from card. Get the real UID if the random UID bit is on and get the same UID as in anticollision if not. Any card's key needs to be provided. ",
-                  "hf mfdes getuid    -> execute with default factory setup\n"
+                  "hf mfdes getuid                              -> execute with default factory setup\n"
                   "hf mfdes getuid --isoid df01 -t aes -s lrp   -> for desfire lights default settings");
 
     void *argtable[] = {
