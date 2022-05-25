commit cd4ee68ccea33127fe450a4bec0d1d8b881c3ba3
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Sep 10 19:40:23 2021 +0200

    text

diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index 0db1c36c8..4090c74dd 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -363,8 +363,8 @@ static int CmdHF14AMfWrBl(const char *Cmd) {
     }
     uint8_t blockno = (uint8_t)b;
 
-    PrintAndLogEx(INFO, "--block no %d, key %c - %s", blockno, (keytype == MF_KEY_B) ? 'B' : 'A', sprint_hex_inrow(key, sizeof(key)));
-    PrintAndLogEx(INFO, "--data: %s", sprint_hex(block, sizeof(block)));
+    PrintAndLogEx(INFO, "Writing block no %d, key %c - %s", blockno, (keytype == MF_KEY_B) ? 'B' : 'A', sprint_hex_inrow(key, sizeof(key)));
+    PrintAndLogEx(INFO, "data: %s", sprint_hex(block, sizeof(block)));
 
     uint8_t data[26];
     memcpy(data, key, sizeof(key));
diff --git a/client/src/mifare/mifarehost.c b/client/src/mifare/mifarehost.c
index df3577fda..e6cb9570a 100644
--- a/client/src/mifare/mifarehost.c
+++ b/client/src/mifare/mifarehost.c
@@ -230,7 +230,7 @@ int mfCheckKeys_fast(uint8_t sectorsCnt, uint8_t firstChunk, uint8_t lastChunk,
     // time to convert the returned data.
     uint8_t curr_keys = resp.oldarg[0];
 
-    PrintAndLogEx(INFO, "Chunk: %.1fs | found %u/%u keys (%u)", (float)(t2 / 1000.0), curr_keys, (sectorsCnt << 1), size);
+    PrintAndLogEx(INFO, "Chunk %.1fs | found %u/%u keys (%u)", (float)(t2 / 1000.0), curr_keys, (sectorsCnt << 1), size);
 
     // all keys?
     if (curr_keys == sectorsCnt * 2 || lastChunk) {
@@ -306,7 +306,7 @@ int mfCheckKeys_file(uint8_t *destfn, uint64_t *key) {
 
         retry--;
         if (retry == 0) {
-            PrintAndLogEx(WARNING, "Chk keys file, timeouted");
+            PrintAndLogEx(WARNING, "Chk keys file, time out");
             SendCommandNG(CMD_BREAK_LOOP, NULL, 0);
             return PM3_ETIMEOUT;
         }
@@ -364,7 +364,7 @@ int mfKeyBrute(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint64_t *resultk
 
         // progress
         if (counter % 20 == 0)
-            PrintAndLogEx(SUCCESS, "tried : %s.. \t %u keys", sprint_hex(candidates + i, 6),  counter * KEYS_IN_BLOCK);
+            PrintAndLogEx(SUCCESS, "tried %s.. \t %u keys", sprint_hex(candidates + i, 6),  counter * KEYS_IN_BLOCK);
     }
     return found;
 }
@@ -545,7 +545,7 @@ int mfnested(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_t trgBlockNo,
             free(statelists[1].head.slhead);
             num_to_bytes(key64, 6, resultKey);
 
-            PrintAndLogEx(SUCCESS, "\ntarget block:%3u key type: %c  -- found valid key [ " _GREEN_("%s") " ]",
+            PrintAndLogEx(SUCCESS, "\ntarget block: %3u key type: %c  -- found valid key [ " _GREEN_("%s") " ]",
                           package->block,
                           package->keytype ? 'B' : 'A',
                           sprint_hex_inrow(resultKey, 6)
@@ -558,7 +558,7 @@ int mfnested(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_t trgBlockNo,
     }
 
 out:
-    PrintAndLogEx(SUCCESS, "\ntarget block:%3u key type: %c",
+    PrintAndLogEx(SUCCESS, "\ntarget block: %3u key type: %c",
                   package->block,
                   package->keytype ? 'B' : 'A'
                  );
@@ -724,7 +724,7 @@ int mfStaticNested(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_t trgBl
             num_to_bytes(key64, 6, resultKey);
 
             PrintAndLogEx(NORMAL, "");
-            PrintAndLogEx(SUCCESS, "target block:%3u key type: %c  -- found valid key [ " _GREEN_("%s") " ]",
+            PrintAndLogEx(SUCCESS, "target block: %3u key type: %c  -- found valid key [ " _GREEN_("%s") " ]",
                           package->block,
                           package->keytype ? 'B' : 'A',
                           sprint_hex_inrow(resultKey, 6)
@@ -744,7 +744,7 @@ int mfStaticNested(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_t trgBl
     free(mem);
 
 out:
-    PrintAndLogEx(SUCCESS, "\ntarget block:%3u key type: %c",
+    PrintAndLogEx(SUCCESS, "\ntarget block: %3u key type: %c",
                   package->block,
                   package->keytype ? 'B' : 'A'
                  );
