commit e930d49aa59f58062d31a10702f9271d8f82dfc2
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Sep 10 20:40:26 2021 +0200

    textual

diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index 4090c74dd..016e749f1 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -4526,7 +4526,7 @@ static int CmdHF14AMfCLoad(const char *Cmd) {
         return PM3_EFILE;
     }
 
-    PrintAndLogEx(SUCCESS, "Card loaded %d blocks from file", blockno);
+    PrintAndLogEx(SUCCESS, "Card loaded " _YELLOW_("%d") " blocks from file", blockno);
     PrintAndLogEx(INFO, "Done!");
     return PM3_SUCCESS;
 }
@@ -4757,13 +4757,13 @@ static int CmdHF14AMfCSave(const char *Cmd) {
                 g_conn.block_after_ACK = false;
             }
             if (mfEmlSetMem(dump + (i * MFBLOCK_SIZE), i, 5) != PM3_SUCCESS) {
-                PrintAndLogEx(WARNING, "Can't set emul block: %d", i);
+                PrintAndLogEx(WARNING, "Can't set emul block: " _YELLOW_("%d"), i);
             }
             PrintAndLogEx(NORMAL, "." NOLF);
             fflush(stdout);
         }
         PrintAndLogEx(NORMAL, "");
-        PrintAndLogEx(SUCCESS, "uploaded %d bytes to emulator memory", bytes);
+        PrintAndLogEx(SUCCESS, "uploaded " _YELLOW_("%d") " bytes to emulator memory", bytes);
     }
 
     // user supplied filename?
@@ -4879,7 +4879,7 @@ static int CmdHF14AMfCView(const char *Cmd) {
         }
 
         if (mfCGetBlock(i, dump + (i * MFBLOCK_SIZE), flags)) {
-            PrintAndLogEx(WARNING, "Can't get magic card block: %u", i);
+            PrintAndLogEx(WARNING, "Can't get magic card block: " _YELLOW_("%u"), i);
             PrintAndLogEx(HINT, "Verify your card size, and try again or try another tag position");
             free(dump);
             return PM3_ESOFT;
diff --git a/client/src/mifare/mifarehost.c b/client/src/mifare/mifarehost.c
index e6cb9570a..95c4966e3 100644
--- a/client/src/mifare/mifarehost.c
+++ b/client/src/mifare/mifarehost.c
@@ -306,7 +306,7 @@ int mfCheckKeys_file(uint8_t *destfn, uint64_t *key) {
 
         retry--;
         if (retry == 0) {
-            PrintAndLogEx(WARNING, "Chk keys file, time out");
+            PrintAndLogEx(WARNING, "Chk keys file, command execution time out");
             SendCommandNG(CMD_BREAK_LOOP, NULL, 0);
             return PM3_ETIMEOUT;
         }
