commit 68d79527e51b4ec34ce82ce2919de23a3fb4d748
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 29 10:13:05 2022 +0100

    cppcheck unused

diff --git a/client/src/cmdhffelica.c b/client/src/cmdhffelica.c
index 715cd89c5..f6c3e8029 100644
--- a/client/src/cmdhffelica.c
+++ b/client/src/cmdhffelica.c
@@ -1702,14 +1702,11 @@ static int CmdHFFelicaRequestService(const char *Cmd) {
 
     uint8_t datalen = 13; // length (1) + CMD (1) + IDm(8) + Node Number (1) + Node Code List (2)
 
-    uint8_t flags = FELICA_APPEND_CRC;
+    uint8_t flags = (FELICA_APPEND_CRC | FELICA_RAW);
     if (custom_IDm) {
         flags |= FELICA_NO_SELECT;
     }
 
-    if (datalen > 0) {
-        flags |= FELICA_RAW;
-    }
     // Todo activate once datalen isn't hardcoded anymore...
     if (custom_IDm == false && check_last_idm(data, datalen) == false) {
         return PM3_EINVARG;
