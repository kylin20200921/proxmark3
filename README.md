commit 82fba2d9297e88eb2ce474619680ef880ffbbaca
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Apr 30 20:52:16 2022 +0200

    correct instructions

diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index 9a78fc589..30cc86e82 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -2299,14 +2299,14 @@ int infoHF14A(bool verbose, bool do_nack_test, bool do_aid_search) {
 
     if (isFUDAN) {
         PrintAndLogEx(HINT, "Hint: try " _YELLOW_("`hf 14a raw`") " - since FUDAN is different");
-        PrintAndLogEx(HINT, "  hf 14a raw -a -b 7 -p 26");
-        PrintAndLogEx(HINT, "  hf 14a raw -p -c 3000");
-        PrintAndLogEx(HINT, "  hf 14a raw -p -c 3001");
-        PrintAndLogEx(HINT, "  hf 14a raw -p -c 3002");
-        PrintAndLogEx(HINT, "  hf 14a raw -p -c 3003");
-        PrintAndLogEx(HINT, "  hf 14a raw -p -c 3004");
-        PrintAndLogEx(HINT, "  hf 14a raw -p -c 3005");
-        PrintAndLogEx(HINT, "  hf 14a raw -p -c 3006");
+        PrintAndLogEx(HINT, "  hf 14a raw -a -b 7 -k 26");
+        PrintAndLogEx(HINT, "  hf 14a raw -k -c 3000");
+        PrintAndLogEx(HINT, "  hf 14a raw -k -c 3001");
+        PrintAndLogEx(HINT, "  hf 14a raw -k -c 3002");
+        PrintAndLogEx(HINT, "  hf 14a raw -k -c 3003");
+        PrintAndLogEx(HINT, "  hf 14a raw -k -c 3004");
+        PrintAndLogEx(HINT, "  hf 14a raw -k -c 3005");
+        PrintAndLogEx(HINT, "  hf 14a raw -k -c 3006");
         PrintAndLogEx(HINT, "  hf 14a raw -c 3007");
     }
 
