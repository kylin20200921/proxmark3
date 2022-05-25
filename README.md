commit 9a9d86de95fd78a8ac5c64f9060397d955de6b67
Author: iceman1001 <iceman@iuse.se>
Date:   Sat May 22 14:20:38 2021 +0200

    text

diff --git a/client/src/cmdhfmfdes.c b/client/src/cmdhfmfdes.c
index 7b6d3e26c..5626e8855 100644
--- a/client/src/cmdhfmfdes.c
+++ b/client/src/cmdhfmfdes.c
@@ -5041,7 +5041,7 @@ static int CmdHF14aDesMAD(const char *Cmd) {
 
 static command_t CommandTable[] = {
     {"help",             CmdHelp,                     AlwaysAvailable, "This help"},
-    {"-----------",      CmdHelp,                     IfPm3Iso14443a,  "----------------------- " _CYAN_("general") " -----------------------"},
+    {"-----------",      CmdHelp,                     IfPm3Iso14443a,  "---------------------- " _CYAN_("general") " ----------------------"},
     {"auth",             CmdHF14ADesAuth,             IfPm3Iso14443a,  "Tries a MIFARE DesFire Authentication"},
     {"changekey",        CmdHF14ADesChangeKey,        IfPm3Iso14443a,  "Change Key"},
     {"chk",              CmdHF14aDesChk,              IfPm3Iso14443a,  "Check keys"},
@@ -5052,7 +5052,7 @@ static command_t CommandTable[] = {
     {"list",             CmdHF14ADesList,             AlwaysAvailable, "List DESFire (ISO 14443A) history"},
 //    {"ndef",             CmdHF14aDesNDEF,             IfPm3Iso14443a,  "Prints NDEF records from card"},
 //    {"mad",             CmdHF14aDesMAD,             IfPm3Iso14443a,  "Prints MAD records from card"},
-    {"-----------",      CmdHelp,                     IfPm3Iso14443a,  "----------------------- " _CYAN_("AID") " -----------------------"},
+    {"-----------",      CmdHelp,                     IfPm3Iso14443a,  "-------------------- " _CYAN_("Applications") " -------------------"},
     {"bruteaid",         CmdHF14ADesBruteApps,        IfPm3Iso14443a,  "Recover AIDs by bruteforce"},
     {"createaid",        CmdHF14ADesCreateApp,        IfPm3Iso14443a,  "Create Application ID"},
     {"deleteaid",        CmdHF14ADesDeleteApp,        IfPm3Iso14443a,  "Delete Application ID"},
