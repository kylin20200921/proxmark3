commit 0b4d584dd7b276b9efcb7b613023b7208bc48787
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Jan 6 20:49:35 2022 +0100

    cppcheck fixes

diff --git a/client/src/cmdhfmfdes.c b/client/src/cmdhfmfdes.c
index 11aa7328b..65989045c 100644
--- a/client/src/cmdhfmfdes.c
+++ b/client/src/cmdhfmfdes.c
@@ -815,7 +815,7 @@ static void DesFill2bPattern(
 
 static int AuthCheckDesfire(DesfireContext_t *dctx,
                             DesfireSecureChannel secureChannel,
-                            uint8_t *aid,
+                            const uint8_t *aid,
                             uint8_t deskeyList[MAX_KEYS_LIST_LEN][8], uint32_t deskeyListLen,
                             uint8_t aeskeyList[MAX_KEYS_LIST_LEN][16], uint32_t aeskeyListLen,
                             uint8_t k3kkeyList[MAX_KEYS_LIST_LEN][24], uint32_t k3kkeyListLen,
diff --git a/client/src/cmdhfmfhard.c b/client/src/cmdhfmfhard.c
index 310492c68..d26a4a51f 100644
--- a/client/src/cmdhfmfhard.c
+++ b/client/src/cmdhfmfhard.c
@@ -152,7 +152,7 @@ static inline void set_bit24(uint32_t *bitarray, uint32_t index) {
     bitarray[index >> 5] |= 0x80000000 >> (index & 0x0000001f);
 }
 
-static inline uint32_t test_bit24(uint32_t *bitarray, uint32_t index) {
+static inline uint32_t test_bit24(const uint32_t *bitarray, uint32_t index) {
     return bitarray[index >> 5] & (0x80000000 >> (index & 0x0000001f));
 }
 
diff --git a/client/src/cmdhfmfp.c b/client/src/cmdhfmfp.c
index 339d8fae4..5eb4f4f2f 100644
--- a/client/src/cmdhfmfp.c
+++ b/client/src/cmdhfmfp.c
@@ -1213,9 +1213,11 @@ static int CmdHFMFPChk(const char *Cmd) {
     if (dict_filenamelen) {
         uint32_t keycnt = 0;
         res = loadFileDICTIONARYEx((char *)dict_filename, keyList, sizeof(keyList), NULL, 16, &keycnt, 0, &endFilePosition, true);
-        keyListLen = keycnt;
-        if (endFilePosition)
+
+        if (res == PM3_SUCCESS && endFilePosition) {
+            keyListLen = keycnt;
             PrintAndLogEx(SUCCESS, "First part of dictionary successfully loaded.");
+        }
     }
 
     if (keyListLen == 0) {
@@ -1255,7 +1257,9 @@ static int CmdHFMFPChk(const char *Cmd) {
 
             uint32_t keycnt = 0;
             res = loadFileDICTIONARYEx((char *)dict_filename, keyList, sizeof(keyList), NULL, 16, &keycnt, endFilePosition, &endFilePosition, false);
-            keyListLen = keycnt;
+            if (res == PM3_SUCCESS && endFilePosition) {
+                keyListLen = keycnt;
+            }
             continue;
         }
         break;
diff --git a/client/src/cmdhfmfu.c b/client/src/cmdhfmfu.c
index 722f02f2b..a62e60d55 100644
--- a/client/src/cmdhfmfu.c
+++ b/client/src/cmdhfmfu.c
@@ -475,7 +475,7 @@ static int ul_print_default(uint8_t *data, uint8_t *real_uid) {
     return PM3_SUCCESS;
 }
 
-static int ndef_get_maxsize(uint8_t *data) {
+static int ndef_get_maxsize(const uint8_t *data) {
     // no NDEF message
     if (data[0] != 0xE1)
         return 0;
diff --git a/client/src/cmdhfseos.c b/client/src/cmdhfseos.c
index 6de8ad89a..a28198d2f 100644
--- a/client/src/cmdhfseos.c
+++ b/client/src/cmdhfseos.c
@@ -24,7 +24,7 @@
 
 static int CmdHelp(const char *Cmd);
 
-static uint16_t get_sw(uint8_t *d, uint8_t n) {
+static uint16_t get_sw(const uint8_t *d, uint8_t n) {
     if (n < 2)
         return 0;
 
diff --git a/client/src/cmdhfst25ta.c b/client/src/cmdhfst25ta.c
index 6695d46b4..83efcd365 100644
--- a/client/src/cmdhfst25ta.c
+++ b/client/src/cmdhfst25ta.c
@@ -103,7 +103,7 @@ static void print_st25ta_system_info(uint8_t *d, uint8_t n) {
     */
 }
 
-static uint16_t get_sw(uint8_t *d, uint8_t n) {
+static uint16_t get_sw(const uint8_t *d, uint8_t n) {
     if (n < 2)
         return 0;
 
