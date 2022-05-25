commit a8ccba77d63d1576bfac054eba1425e2379b28a3
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Jan 7 19:13:24 2022 +0100

    cppcheck fixes const

diff --git a/client/src/emv/emvcore.c b/client/src/emv/emvcore.c
index 3dccf4d47..5f154815c 100644
--- a/client/src/emv/emvcore.c
+++ b/client/src/emv/emvcore.c
@@ -329,7 +329,6 @@ static int EMVSelectWithRetry(Iso7816CommandChannel channel, bool ActivateField,
                     return 1;
                 }
 
-                retrycnt = 0;
                 PrintAndLogEx(FAILED, "Retry failed [%s]. Skipped...", sprint_hex_inrow(AID, AIDLen));
                 return res;
             }
diff --git a/client/src/fileutils.c b/client/src/fileutils.c
index c38613959..9b285e257 100644
--- a/client/src/fileutils.c
+++ b/client/src/fileutils.c
@@ -1263,20 +1263,18 @@ int loadFileJSONroot(const char *preferredName, void **proot, bool verbose) {
     if (root == NULL) {
         PrintAndLogEx(ERR, "ERROR: json " _YELLOW_("%s") " error on line %d: %s", preferredName, error.line, error.text);
         retval = PM3_ESOFT;
-        goto out;
     }
 
-    if (!json_is_object(root)) {
+    if (json_is_object(root) == false) {
         PrintAndLogEx(ERR, "ERROR: Invalid json " _YELLOW_("%s") " format. root must be an object.", preferredName);
         retval = PM3_ESOFT;
-        goto out;
     }
 
-    *proot = root;
-    return PM3_SUCCESS;
+    if (retval == PM3_ESOFT)
+        json_decref(root);
+    else
+        *proot = root;
 
-out:
-    json_decref(root);
     return retval;
 }
 
diff --git a/client/src/loclass/cipher.c b/client/src/loclass/cipher.c
index 9a646b9f6..943a616fb 100644
--- a/client/src/loclass/cipher.c
+++ b/client/src/loclass/cipher.c
@@ -169,7 +169,7 @@ static uint8_t _select(bool x, bool y, uint8_t r) {
 * @param s - state
 * @param k - array containing 8 bytes
 **/
-static State_t successor(uint8_t *k, State_t s, bool y) {
+static State_t successor(const uint8_t *k, State_t s, bool y) {
     bool r0 = s.r >> 7 & 0x1;
     bool r4 = s.r >> 3 & 0x1;
     bool r7 = s.r & 0x1;
@@ -227,7 +227,7 @@ static void output(uint8_t *k, State_t s, BitstreamIn_t *in,  BitstreamOut_t *ou
 * key k ∈ (F 82 ) 8 and outputs the initial cipher state s =< l, r, t, b >
 **/
 
-static State_t init(uint8_t *k) {
+static State_t init(const uint8_t *k) {
     State_t s = {
         ((k[0] ^ 0x4c) + 0xEC) & 0xFF,// l
         ((k[0] ^ 0x4c) + 0x21) & 0xFF,// r
diff --git a/client/src/loclass/elite_crack.c b/client/src/loclass/elite_crack.c
index 58f022779..89d4cfdf7 100644
--- a/client/src/loclass/elite_crack.c
+++ b/client/src/loclass/elite_crack.c
@@ -70,7 +70,7 @@
  * @param key
  * @param dest
  */
-void permutekey(uint8_t key[8], uint8_t dest[8]) {
+void permutekey(const uint8_t key[8], uint8_t dest[8]) {
     for (uint8_t i = 0 ; i < 8 ; i++) {
         dest[i] = (((key[7] & (0x80 >> i)) >> (7 - i)) << 7) |
                   (((key[6] & (0x80 >> i)) >> (7 - i)) << 6) |
@@ -88,7 +88,7 @@ void permutekey(uint8_t key[8], uint8_t dest[8]) {
  * @param key
  * @param dest
  */
-void permutekey_rev(uint8_t key[8], uint8_t dest[8]) {
+void permutekey_rev(const uint8_t key[8], uint8_t dest[8]) {
     int i;
     for (i = 0 ; i < 8 ; i++) {
         dest[7 - i] = (((key[0] & (0x80 >> i)) >> (7 - i)) << 7) |
@@ -138,7 +138,7 @@ static inline uint8_t swap(uint8_t val) {
  * @param csn the CSN used
  * @param k output
  */
-void hash1(uint8_t csn[], uint8_t k[]) {
+void hash1(const uint8_t csn[], uint8_t k[]) {
     k[0] = csn[0] ^ csn[1] ^ csn[2] ^ csn[3] ^ csn[4] ^ csn[5] ^ csn[6] ^ csn[7];
     k[1] = csn[0] + csn[1] + csn[2] + csn[3] + csn[4] + csn[5] + csn[6] + csn[7];
     k[2] = rr(swap(csn[2] + k[1]));
diff --git a/client/src/loclass/elite_crack.h b/client/src/loclass/elite_crack.h
index fca942c51..213ceb373 100644
--- a/client/src/loclass/elite_crack.h
+++ b/client/src/loclass/elite_crack.h
@@ -54,14 +54,14 @@ typedef struct {
 } loclass_dumpdata_t;
 
 
-void permutekey(uint8_t key[8], uint8_t dest[8]);
+void permutekey(const uint8_t key[8], uint8_t dest[8]);
 /**
  * Permutes  a key from iclass specific format to NIST format
  * @brief permutekey_rev
  * @param key
  * @param dest
  */
-void permutekey_rev(uint8_t key[8], uint8_t dest[8]);
+void permutekey_rev(const uint8_t key[8], uint8_t dest[8]);
 
 /**
  * Perform a bruteforce against a file which has been saved by pm3
@@ -107,7 +107,7 @@ int bruteforceItem(loclass_dumpdata_t item, uint16_t keytable[]);
  * @param csn the CSN used
  * @param k output
  */
-void hash1(uint8_t *csn, uint8_t *k);
+void hash1(const uint8_t *csn, uint8_t *k);
 void hash2(uint8_t *key64, uint8_t *outp_keytable);
 /**
  * From dismantling iclass-paper:
diff --git a/client/src/mifare/desfirecore.c b/client/src/mifare/desfirecore.c
index 8c67242fe..06e4077d9 100644
--- a/client/src/mifare/desfirecore.c
+++ b/client/src/mifare/desfirecore.c
@@ -115,7 +115,7 @@ const CLIParserOption DesfireReadFileTypeOpts[] = {
     {0,    NULL},
 };
 
-static const char *getstatus(uint16_t *sw) {
+static const char *getstatus(const uint16_t *sw) {
     if (sw == NULL) return "--> sw argument error. This should never happen !";
     if (((*sw >> 8) & 0xFF) == 0x91) {
         switch (*sw & 0xFF) {
@@ -340,7 +340,8 @@ bool DesfireMFSelected(DesfireISOSelectWay way, uint32_t id) {
     return false;
 }
 
-uint32_t DesfireAIDByteToUint(uint8_t *data) {
+// iceman todo:  use commonutil.c instead
+uint32_t DesfireAIDByteToUint(const uint8_t *data) {
     return data[0] + (data[1] << 8) + (data[2] << 16);
 }
 
@@ -544,8 +545,8 @@ static int DesfireExchangeNative(bool activate_field, DesfireContext_t *ctx, uin
     memcpy(&cdata[1], data, datalen);
     cdatalen = datalen + 1;
 
-    int res = 0;
-    size_t len = 0;
+    int res;
+    size_t len;
     // tx chaining
     size_t sentdatalen = 0;
     while (cdatalen >= sentdatalen) {
@@ -638,26 +639,28 @@ static int DesfireExchangeNative(bool activate_field, DesfireContext_t *ctx, uin
 static int DesfireExchangeISONative(bool activate_field, DesfireContext_t *ctx, uint8_t cmd, uint8_t *data, size_t datalen, uint8_t *respcode, uint8_t *resp, size_t *resplen, bool enable_chaining, size_t splitbysize) {
     if (resplen)
         *resplen = 0;
+
     if (respcode)
         *respcode = 0xff;
 
     uint16_t sw = 0;
     uint8_t *buf  = calloc(DESFIRE_BUFFER_SIZE, 1);
-    if (buf == NULL)
+    if (buf == NULL) {
         return PM3_EMALLOC;
+    }
+
     uint32_t buflen = 0;
     uint32_t pos = 0;
     uint32_t i = 1;
 
-    sAPDU_t apdu = {0};
-    apdu.CLA = MFDES_NATIVE_ISO7816_WRAP_CLA; //0x90
-    apdu.INS = cmd;
-    apdu.Lc = datalen;
-    apdu.P1 = 0;
-    apdu.P2 = 0;
-    apdu.data = data;
+    sAPDU_t apdu = {
+        .CLA = MFDES_NATIVE_ISO7816_WRAP_CLA, //0x90
+        .INS = cmd,
+        .P1 = 0,
+        .P2 = 0,
+    };
 
-    int res = 0;
+    int res;
     // tx chaining
     size_t sentdatalen = 0;
     while (datalen >= sentdatalen) {
@@ -665,6 +668,7 @@ static int DesfireExchangeISONative(bool activate_field, DesfireContext_t *ctx,
             apdu.Lc = DESFIRE_TX_FRAME_MAX_LEN;
         else
             apdu.Lc = datalen - sentdatalen;
+
         apdu.data = &data[sentdatalen];
 
         if (sentdatalen > 0)
@@ -2330,7 +2334,7 @@ void DesfireEncodeFileAcessMode(uint8_t *mode, uint8_t r, uint8_t w, uint8_t rw,
     mode[1] = (w & 0x0f) | ((r << 4) & 0xf0);
 }
 
-void DesfireDecodeFileAcessMode(uint8_t *mode, uint8_t *r, uint8_t *w, uint8_t *rw, uint8_t *ch) {
+void DesfireDecodeFileAcessMode(const uint8_t *mode, uint8_t *r, uint8_t *w, uint8_t *rw, uint8_t *ch) {
     // read
     if (r)
         *r = (mode[1] >> 4) & 0x0f; // hi 2b
@@ -2972,7 +2976,7 @@ int DesfireAnticollision(bool verbose) {
     return SelectCard14443A_4(false, verbose, NULL);
 }
 
-int DesfireSelectEx(DesfireContext_t *ctx, bool fieldon, DesfireISOSelectWay way, uint32_t id, char *dfname) {
+int DesfireSelectEx(DesfireContext_t *ctx, bool fieldon, DesfireISOSelectWay way, uint32_t id, const char *dfname) {
     uint8_t resp[250] = {0};
     size_t resplen = 0;
 
diff --git a/client/src/mifare/desfirecore.h b/client/src/mifare/desfirecore.h
index f66c83981..3b1fe4d72 100644
--- a/client/src/mifare/desfirecore.h
+++ b/client/src/mifare/desfirecore.h
@@ -153,7 +153,7 @@ extern const CLIParserOption DesfireValueFileOperOpts[];
 extern const CLIParserOption DesfireReadFileTypeOpts[];
 
 const char *DesfireGetErrorString(int res, uint16_t *sw);
-uint32_t DesfireAIDByteToUint(uint8_t *data);
+uint32_t DesfireAIDByteToUint(const uint8_t *data);
 void DesfireAIDUintToByte(uint32_t aid, uint8_t *data);
 
 void DesfirePrintContext(DesfireContext_t *ctx);
@@ -175,7 +175,7 @@ int DesfireGetCardUID(DesfireContext_t *ctx);
 const char *DesfireSelectWayToStr(DesfireISOSelectWay way);
 char *DesfireWayIDStr(DesfireISOSelectWay way, uint32_t id);
 bool DesfireMFSelected(DesfireISOSelectWay way, uint32_t id);
-int DesfireSelectEx(DesfireContext_t *ctx, bool fieldon, DesfireISOSelectWay way, uint32_t id, char *dfname);
+int DesfireSelectEx(DesfireContext_t *ctx, bool fieldon, DesfireISOSelectWay way, uint32_t id, const char *dfname);
 int DesfireSelect(DesfireContext_t *ctx, DesfireISOSelectWay way, uint32_t id, char *dfname);
 
 const char *DesfireAuthErrorToStr(int error);
@@ -230,7 +230,7 @@ const DesfireCreateFileCommands_t *GetDesfireFileCmdRec(uint8_t type);
 const char *GetDesfireAccessRightStr(uint8_t right);
 const char *GetDesfireAccessRightShortStr(uint8_t right);
 void DesfireEncodeFileAcessMode(uint8_t *mode, uint8_t r, uint8_t w, uint8_t rw, uint8_t ch);
-void DesfireDecodeFileAcessMode(uint8_t *mode, uint8_t *r, uint8_t *w, uint8_t *rw, uint8_t *ch);
+void DesfireDecodeFileAcessMode(const uint8_t *mode, uint8_t *r, uint8_t *w, uint8_t *rw, uint8_t *ch);
 void DesfirePrintAccessRight(uint8_t *data);
 void DesfirePrintFileSettings(uint8_t *data, size_t len);
 void DesfirePrintSetFileSettings(uint8_t *data, size_t len);
