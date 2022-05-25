commit 28efc8b09747088a0d1871aec181f49feede879f
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jan 9 13:36:29 2022 +0100

    hf mf darkside - textual adaptation to current style

diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index 13cbc248d..2d9f05fb3 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -314,7 +314,6 @@ static int CmdHF14AMfAcl(const char *Cmd) {
     return PM3_SUCCESS;
 }
 
-
 static int CmdHF14AMfDarkside(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mf darkside",
@@ -343,31 +342,32 @@ static int CmdHF14AMfDarkside(const char *Cmd) {
     CLIParserFree(ctx);
 
     uint64_t key = 0;
-
+    uint64_t t1 = msclock();
     int isOK = mfDarkside(blockno, key_type, &key);
-    PrintAndLogEx(NORMAL, "");
+    t1 = msclock() - t1;
+
     switch (isOK) {
         case -1 :
-            PrintAndLogEx(WARNING, "button pressed. Aborted.");
+            PrintAndLogEx(WARNING, "button pressed, aborted");
             return PM3_ESOFT;
         case -2 :
-            PrintAndLogEx(FAILED, "card is not vulnerable to Darkside attack (doesn't send NACK on authentication requests).");
+            PrintAndLogEx(FAILED, "card is not vulnerable to Darkside attack (doesn't send NACK on authentication requests)");
             return PM3_ESOFT;
         case -3 :
-            PrintAndLogEx(FAILED, "card is not vulnerable to Darkside attack (its random number generator is not predictable).");
+            PrintAndLogEx(FAILED, "card is not vulnerable to Darkside attack (its random number generator is not predictable)");
             return PM3_ESOFT;
         case -4 :
             PrintAndLogEx(FAILED, "card is not vulnerable to Darkside attack (its random number generator seems to be based on the wellknown");
-            PrintAndLogEx(FAILED, "generating polynomial with 16 effective bits only, but shows unexpected behaviour.");
-            return PM3_ESOFT;
-        case -5 :
-            PrintAndLogEx(WARNING, "aborted via keyboard.");
+            PrintAndLogEx(FAILED, "generating polynomial with 16 effective bits only, but shows unexpected behaviour");
             return PM3_ESOFT;
+        case PM3_EOPABORTED :
+            PrintAndLogEx(WARNING, "aborted via keyboard");
+            return PM3_EOPABORTED;
         default :
-            PrintAndLogEx(SUCCESS, "found valid key: "_YELLOW_("%012" PRIx64), key);
+            PrintAndLogEx(SUCCESS, "found valid key: "_GREEN_("%012" PRIx64), key);
             break;
     }
-    PrintAndLogEx(NORMAL, "");
+    PrintAndLogEx(SUCCESS, "time in darkside " _YELLOW_("%.0f") " seconds\n",(float)t1 / 1000.0);
     return PM3_SUCCESS;
 }
 
diff --git a/client/src/mifare/mifarehost.c b/client/src/mifare/mifarehost.c
index 860d19f0e..8c4aa87fd 100644
--- a/client/src/mifare/mifarehost.c
+++ b/client/src/mifare/mifarehost.c
@@ -44,10 +44,8 @@ int mfDarkside(uint8_t blockno, uint8_t key_type, uint64_t *key) {
     bool first_run = true;
 
     // message
-    PrintAndLogEx(INFO, "--------------------------------------------------------------------------------");
-    PrintAndLogEx(INFO, "Executing darkside attack. Expected execution time: 25sec on average");
-    PrintAndLogEx(INFO, "press pm3-button on the Proxmark3 device to abort both Proxmark3 and client");
-    PrintAndLogEx(INFO, "--------------------------------------------------------------------------------");
+    PrintAndLogEx(INFO, "Expected execution time is about 25seconds on average");
+    PrintAndLogEx(INFO, "Press pm3-button to abort");
 
     while (true) {
         clearCommandBuffer();
@@ -63,16 +61,19 @@ int mfDarkside(uint8_t blockno, uint8_t key_type, uint64_t *key) {
 
         //flush queue
         while (kbd_enter_pressed()) {
+            SendCommandNG(CMD_BREAK_LOOP, NULL, 0);
             return PM3_EOPABORTED;
         }
 
-        PrintAndLogEx(INFO, "." NOLF);
+        PrintAndLogEx(NORMAL, "");
+        PrintAndLogEx(INFO, "Running darkside " NOLF);
 
         // wait cycle
         while (true) {
             PrintAndLogEx(NORMAL, "." NOLF);
 
             if (kbd_enter_pressed()) {
+                SendCommandNG(CMD_BREAK_LOOP, NULL, 0);
                 return PM3_EOPABORTED;
             }
 
@@ -112,7 +113,7 @@ int mfDarkside(uint8_t blockno, uint8_t key_type, uint64_t *key) {
                 break;
             }
         }
-        PrintAndLogEx(NORMAL, "\n");
+        PrintAndLogEx(NORMAL, "");
 
         if (par_list == 0 && first_run == true) {
             PrintAndLogEx(SUCCESS, "Parity is all zero. Most likely this card sends NACK on every authentication.");
@@ -122,8 +123,9 @@ int mfDarkside(uint8_t blockno, uint8_t key_type, uint64_t *key) {
         uint32_t keycount = nonce2key(uid, nt, nr, ar, par_list, ks_list, &keylist);
 
         if (keycount == 0) {
-            PrintAndLogEx(FAILED, "key not found (lfsr_common_prefix list is null). Nt=%08x", nt);
-            PrintAndLogEx(FAILED, "this is expected to happen in 25%% of all cases. Trying again with a different reader nonce...");
+            PrintAndLogEx(FAILED, "Key not found (lfsr_common_prefix list is null). Nt = %08x", nt);
+            PrintAndLogEx(FAILED, "This is expected to happen in 25%% of all cases.");
+            PrintAndLogEx(FAILED, "Trying again with a different reader nonce...");
             continue;
         }
 
@@ -134,12 +136,12 @@ int mfDarkside(uint8_t blockno, uint8_t key_type, uint64_t *key) {
             if (keycount == 0) {
                 free(last_keylist);
                 last_keylist = keylist;
-                PrintAndLogEx(FAILED, "no candidates found, trying again");
+                PrintAndLogEx(FAILED, "No candidates found, trying again");
                 continue;
             }
         }
 
-        PrintAndLogEx(SUCCESS, "found " _YELLOW_("%u") " candidate key%s", keycount, (keycount > 1) ? "s." : ".");
+        PrintAndLogEx(SUCCESS, "found " _YELLOW_("%u") " candidate key%s", keycount, (keycount > 1) ? "s" : "");
 
         *key = UINT64_C(-1);
         uint8_t keyBlock[PM3_CMD_DATA_SIZE];
@@ -164,7 +166,7 @@ int mfDarkside(uint8_t blockno, uint8_t key_type, uint64_t *key) {
         if (*key != UINT64_C(-1)) {
             break;
         } else {
-            PrintAndLogEx(FAILED, "all key candidates failed. Restarting darkside attack");
+            PrintAndLogEx(FAILED, "All key candidates failed. Restarting darkside");
             free(last_keylist);
             last_keylist = keylist;
             first_run = true;
@@ -555,7 +557,7 @@ int mfnested(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_t trgBlockNo,
             free(statelists[1].head.slhead);
             num_to_bytes(key64, 6, resultKey);
 
-            PrintAndLogEx(SUCCESS, "\ntarget block: %3u key type: %c  -- found valid key [ " _GREEN_("%s") " ]",
+            PrintAndLogEx(SUCCESS, "\ntarget block %4u key type %c -- found valid key [ " _GREEN_("%s") " ]",
                           package->block,
                           package->keytype ? 'B' : 'A',
                           sprint_hex_inrow(resultKey, 6)
@@ -568,7 +570,7 @@ int mfnested(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_t trgBlockNo,
     }
 
 out:
-    PrintAndLogEx(SUCCESS, "\ntarget block: %3u key type: %c",
+    PrintAndLogEx(SUCCESS, "\ntarget block %4u key type %c",
                   package->block,
                   package->keytype ? 'B' : 'A'
                  );
