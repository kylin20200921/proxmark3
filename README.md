commit 3e6bad8b02b1bb3bb78c64dacc6117819a8eeef7
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Aug 22 16:05:33 2021 +0200

    textual

diff --git a/client/src/mifare/mifarehost.c b/client/src/mifare/mifarehost.c
index cba4d5ac7..df3577fda 100644
--- a/client/src/mifare/mifarehost.c
+++ b/client/src/mifare/mifarehost.c
@@ -545,7 +545,7 @@ int mfnested(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_t trgBlockNo,
             free(statelists[1].head.slhead);
             num_to_bytes(key64, 6, resultKey);
 
-            PrintAndLogEx(SUCCESS, "\ntarget block:%3u key type: %c  -- found valid key [ " _GREEN_("%s") "]",
+            PrintAndLogEx(SUCCESS, "\ntarget block:%3u key type: %c  -- found valid key [ " _GREEN_("%s") " ]",
                           package->block,
                           package->keytype ? 'B' : 'A',
                           sprint_hex_inrow(resultKey, 6)
@@ -724,7 +724,7 @@ int mfStaticNested(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_t trgBl
             num_to_bytes(key64, 6, resultKey);
 
             PrintAndLogEx(NORMAL, "");
-            PrintAndLogEx(SUCCESS, "target block:%3u key type: %c  -- found valid key [ " _GREEN_("%s") "]",
+            PrintAndLogEx(SUCCESS, "target block:%3u key type: %c  -- found valid key [ " _GREEN_("%s") " ]",
                           package->block,
                           package->keytype ? 'B' : 'A',
                           sprint_hex_inrow(resultKey, 6)
