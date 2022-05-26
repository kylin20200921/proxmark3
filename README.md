commit da81c6806b5e73c6e33f127109a6a82babc50c91
Author: iceman1001 <iceman@iuse.se>
Date:   Tue Apr 19 19:01:04 2022 +0200

    style

diff --git a/client/src/mifare/desfirecore.c b/client/src/mifare/desfirecore.c
index 386f528e2..ff06ca30d 100644
--- a/client/src/mifare/desfirecore.c
+++ b/client/src/mifare/desfirecore.c
@@ -1672,14 +1672,12 @@ void DesfireCheckAuthCommands(DesfireISOSelectWay way, uint32_t appID, char *dfn
 }
 
 void DesfireCheckAuthCommandsPrint(AuthCommandsChk_t *authCmdCheck) {
-    PrintAndLogEx(NORMAL, "auth: %s auth iso: %s auth aes: %s auth ev2: %s auth iso native: %s auth lrp: %s",
-                  authCmdCheck->auth ? _GREEN_("YES") : _RED_("NO"),
-                  authCmdCheck->authISO ? _GREEN_("YES") : _RED_("NO"),
-                  authCmdCheck->authAES ? _GREEN_("YES") : _RED_("NO"),
-                  authCmdCheck->authEV2 ? _GREEN_("YES") : _RED_("NO"),
-                  authCmdCheck->authISONative ? _GREEN_("YES") : _RED_("NO"),
-                  authCmdCheck->authLRP ? _GREEN_("YES") : _RED_("NO")
-                 );
+    PrintAndLogEx(SUCCESS, "   Auth.............. %s", authCmdCheck->auth ? _GREEN_("YES") : _RED_("NO"));
+    PrintAndLogEx(SUCCESS, "   Auth ISO.......... %s", authCmdCheck->authISO ? _GREEN_("YES") : _RED_("NO"));
+    PrintAndLogEx(SUCCESS, "   Auth AES.......... %s", authCmdCheck->authAES ? _GREEN_("YES") : _RED_("NO"));
+    PrintAndLogEx(SUCCESS, "   Auth Ev2.......... %s", authCmdCheck->authEV2 ? _GREEN_("YES") : _RED_("NO"));
+    PrintAndLogEx(SUCCESS, "   Auth ISO Native... %s", authCmdCheck->authISONative ? _GREEN_("YES") : _RED_("NO"));
+    PrintAndLogEx(SUCCESS, "   Auth LRP.......... %s", authCmdCheck->authLRP ? _GREEN_("YES") : _RED_("NO"));
 }
 
 int DesfireFillPICCInfo(DesfireContext_t *dctx, PICCInfo_t *PICCInfo, bool deepmode) {
@@ -1808,7 +1806,7 @@ void DesfirePrintPICCInfo(DesfireContext_t *dctx, PICCInfo_t *PICCInfo) {
     else
         PrintAndLogEx(SUCCESS, "Applications count: " _GREEN_("%zu") " free memory " _GREEN_("%d") " bytes", PICCInfo->appCount, PICCInfo->freemem);
     if (PICCInfo->authCmdCheck.checked) {
-        PrintAndLogEx(SUCCESS, "PICC level auth commands: " NOLF);
+        PrintAndLogEx(SUCCESS, "PICC level auth commands: ");
         DesfireCheckAuthCommandsPrint(&PICCInfo->authCmdCheck);
     }
     if (PICCInfo->numberOfKeys > 0) {
