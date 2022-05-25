commit 7265b2708b6ff35c3208b73349bb3b730ad1af7a
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Dec 31 11:11:56 2021 +0100

    text

diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index ea61c460f..eaa49cd4a 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -6300,7 +6300,7 @@ static command_t CommandTable[] = {
     {"gen3uid",     CmdHf14AGen3UID,        IfPm3Iso14443a,  "Set UID without changing manufacturer block"},
     {"gen3blk",     CmdHf14AGen3Block,      IfPm3Iso14443a,  "Overwrite manufacturer block"},
     {"gen3freeze",  CmdHf14AGen3Freeze,     IfPm3Iso14443a,  "Perma lock UID changes. irreversible"},
-    {"-----------", CmdHelp,                IfPm3Iso14443a,  "----------------------- " _CYAN_("magic gen4 GTU") " -----------------------"},
+    {"-----------", CmdHelp,                IfPm3Iso14443a,  "-------------------- " _CYAN_("magic gen4 GTU") " --------------------------"},
     {"gview",       CmdHF14AGen4View,       IfPm3Iso14443a,  "View card"},
 //    {"-----------", CmdHelp,                IfPm3Iso14443a,  "----------------------- " _CYAN_("i") " -----------------------"},
 //    {"ice",         CmdHF14AMfice,          IfPm3Iso14443a,  "collect MIFARE Classic nonces to file"},
