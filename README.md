commit d9f1b977525631a9b92506916c2d645bd7a4267e
Author: iceman1001 <iceman@iuse.se>
Date:   Mon May 24 14:02:37 2021 +0200

    comment out header

diff --git a/client/src/cmdhfmfu.c b/client/src/cmdhfmfu.c
index 85222000c..56f1c53f2 100644
--- a/client/src/cmdhfmfu.c
+++ b/client/src/cmdhfmfu.c
@@ -4025,7 +4025,7 @@ static command_t CommandTable[] = {
     {"---------", CmdHelp,                 IfPm3Iso14443a,  "----------------------- " _CYAN_("magic") " ----------------------------"},
     {"setpwd",  CmdHF14AMfUCSetPwd,        IfPm3Iso14443a,  "Set 3DES key - Ultralight-C"},
     {"setuid",  CmdHF14AMfUCSetUid,        IfPm3Iso14443a,  "Set UID - MAGIC tags only"},
-    {"---------", CmdHelp,                 IfPm3Iso14443a,  "----------------------- " _CYAN_("amiibo") " ----------------------------"},
+//    {"---------", CmdHelp,                 IfPm3Iso14443a,  "----------------------- " _CYAN_("amiibo") " ----------------------------"},
 //    {"decrypt",  CmdHF14AMfUCDecryptAmiibo, IfPm3Iso14443a, "Decrypt a amiibo tag"},
     {NULL, NULL, NULL, NULL}
 };
