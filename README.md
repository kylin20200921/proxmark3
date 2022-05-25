commit b93de4388a442b68d43591a3119a7e97dde9ac36
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jun 20 08:41:18 2021 +0200

    textual

diff --git a/client/src/cmdhfcipurse.c b/client/src/cmdhfcipurse.c
index faeea6dab..427340ca4 100644
--- a/client/src/cmdhfcipurse.c
+++ b/client/src/cmdhfcipurse.c
@@ -67,7 +67,7 @@ static int CmdHFCipurseInfo(const char *Cmd) {
         if (sw)
             PrintAndLogEx(INFO, "Not a CIPURSE card. APDU response: %04x - %s", sw, GetAPDUCodeDescription(sw >> 8, sw & 0xff));
         else
-            PrintAndLogEx(ERR, "APDU exchange error. Card returns 0x0000.");
+            PrintAndLogEx(ERR, "APDU exchange error. Card returns 0x0000");
 
         DropField();
         return PM3_SUCCESS;
