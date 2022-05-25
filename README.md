commit e498b66acef848a434b418a8addcc28906b4b256
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Sep 18 12:06:59 2021 +0200

    fix #1504 - header or no header i keyblock dynamic memory.  And clear trace log

diff --git a/client/src/mifare/mifarehost.c b/client/src/mifare/mifarehost.c
index 95c4966e3..757de02f0 100644
--- a/client/src/mifare/mifarehost.c
+++ b/client/src/mifare/mifarehost.c
@@ -661,18 +661,35 @@ int mfStaticNested(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_t trgBl
     uint32_t maxkeysinblock = IfPm3Flash() ? 1000 : KEYS_IN_BLOCK;
     uint32_t max_keys_chunk = keycnt > maxkeysinblock ? maxkeysinblock : keycnt;
 
-    uint8_t *mem = calloc((maxkeysinblock * 6) + 5, sizeof(uint8_t));
-    if (mem == NULL) {
-        free(statelists[0].head.slhead);
-        return PM3_EMALLOC;
-    }
+    uint8_t *mem = NULL;
+    uint8_t *p_keyblock = NULL;    
+
+    if (IfPm3Flash()) {    
+
+        // used for mfCheckKeys_file, which needs a header
+        mem = calloc((maxkeysinblock * 6) + 5, sizeof(uint8_t));
+        if (mem == NULL) {
+            free(statelists[0].head.slhead);
+            return PM3_EMALLOC;
+        }
+
+        mem[0] = statelists[0].keyType;
+        mem[1] = statelists[0].blockNo;
+        mem[2] = 1;
+        mem[3] = ((max_keys_chunk >> 8) & 0xFF);
+        mem[4] = (max_keys_chunk & 0xFF);
 
-    uint8_t *p_keyblock = mem + 5;
-    mem[0] = statelists[0].keyType;
-    mem[1] = statelists[0].blockNo;
-    mem[2] = 1;
-    mem[3] = ((max_keys_chunk >> 8) & 0xFF);
-    mem[4] = (max_keys_chunk & 0xFF);
+        p_keyblock = mem + 5;
+    } else {
+
+        // used for mfCheckKeys, which adds its own header.
+        mem = calloc((maxkeysinblock * 6), sizeof(uint8_t));
+        if (mem == NULL) {
+            free(statelists[0].head.slhead);
+            return PM3_EMALLOC;
+        }
+        p_keyblock = mem;
+    }
 
     uint8_t destfn[32];
     strncpy((char *)destfn, "static_nested_000.bin", sizeof(destfn) - 1);
@@ -713,7 +730,7 @@ int mfStaticNested(uint8_t blockNo, uint8_t keyType, uint8_t *key, uint8_t trgBl
             }
             res = mfCheckKeys_file(destfn, &key64);
         } else {
-            res = mfCheckKeys(statelists[0].blockNo, statelists[0].keyType, false, chunk, mem, &key64);
+            res = mfCheckKeys(statelists[0].blockNo, statelists[0].keyType, true, chunk, mem, &key64);
         }
 
         if (res == PM3_SUCCESS) {
