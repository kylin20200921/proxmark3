commit d46f80e7eab9c47922cc4abb211f72a1e48509fe
Author: iceman1001 <iceman@iuse.se>
Date:   Thu May 20 06:44:48 2021 +0200

    new desfire ats, based on #PR138 in nfc-tools/libfreefare repository

diff --git a/client/src/cmdhfmfdes.c b/client/src/cmdhfmfdes.c
index 62a18e878..1f7689fb4 100644
--- a/client/src/cmdhfmfdes.c
+++ b/client/src/cmdhfmfdes.c
@@ -3615,12 +3615,23 @@ static int CmdHF14ADesInfo(const char *Cmd) {
     if (res == PM3_SUCCESS) {
         static const char STANDALONE_DESFIRE[] = { 0x75, 0x77, 0x81, 0x02};
         static const char JCOP_DESFIRE[] = { 0x75, 0xf7, 0xb1, 0x02 };
+        static const char JCOP3_DESFIRE[] = { 0x78, 0x77, 0x71, 0x02 };
 
-        if (str_startswith((const char *)card.ats + 1, STANDALONE_DESFIRE)) {
-            PrintAndLogEx(INFO, "Standalone DESFire");
-        }
-        if (str_startswith((const char *)card.ats + 1, JCOP_DESFIRE)) {
-            PrintAndLogEx(INFO, "JCOP DESFire");
+        if (card.sak == 0x20) {
+
+            if (card.ats_len >= 5) {
+                if (str_startswith((const char *)card.ats + 1, STANDALONE_DESFIRE)) {
+                    PrintAndLogEx(INFO, "Standalone DESFire");
+                }
+                if (str_startswith((const char *)card.ats + 1, JCOP_DESFIRE)) {
+                    PrintAndLogEx(INFO, "JCOP DESFire");
+                }
+            }
+            if (card.ats_len == 4) {
+                if (str_startswith((const char *)card.ats + 1, JCOP3_DESFIRE)) {
+                    PrintAndLogEx(INFO, "JCOP3 DESFire");
+                }
+            }
         }
     }
 
