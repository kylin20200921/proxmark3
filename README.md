commit b9bc5f7a38eab52703caeede21fe125d87f5aa0c
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jan 23 14:38:42 2022 +0100

    fix xiaomi pack algo

diff --git a/client/src/cmdhfmfu.c b/client/src/cmdhfmfu.c
index 7dcb4e957..2512c5447 100644
--- a/client/src/cmdhfmfu.c
+++ b/client/src/cmdhfmfu.c
@@ -1167,7 +1167,7 @@ static mfu_identify_t mfu_ident_table[] = {
     {
         "Xiaomi AIR Purifier", "0004040201000F03",
         0, 0, "",
-        ul_ev1_pwdgenE, ul_ev1_packgen_def,
+        ul_ev1_pwdgenE, ul_ev1_packgenE,
         "hf mfu dump -k %08x"
     },
     */
@@ -3194,7 +3194,7 @@ static int CmdHF14AMfUPwdGen(const char *Cmd) {
     PrintAndLogEx(INFO, " Amiibo          | %08X | %04X", ul_ev1_pwdgenB(uid), ul_ev1_packgenB(uid));
     PrintAndLogEx(INFO, " Lego Dimension  | %08X | %04X", ul_ev1_pwdgenC(uid), ul_ev1_packgenC(uid));
     PrintAndLogEx(INFO, " XYZ 3D printer  | %08X | %04X", ul_ev1_pwdgenD(uid), ul_ev1_packgenD(uid));
-    PrintAndLogEx(INFO, " Xiaomi purifier | %08X | %04X", ul_ev1_pwdgenE(uid), ul_ev1_packgen_def(uid));
+    PrintAndLogEx(INFO, " Xiaomi purifier | %08X | %04X", ul_ev1_pwdgenE(uid), ul_ev1_packgenE(uid));
     PrintAndLogEx(INFO, "-----------------+----------+-----");
     PrintAndLogEx(INFO, " Vingcard algo");
     PrintAndLogEx(INFO, "----------------------------------");
diff --git a/common/generator.c b/common/generator.c
index a800d01bf..be58bc7bc 100644
--- a/common/generator.c
+++ b/common/generator.c
@@ -202,6 +202,13 @@ uint16_t ul_ev1_packgenD(const uint8_t *uid) {
     return BSWAP_16(p & 0xFFFF);
 }
 
+uint16_t ul_ev1_packgenE(const uint8_t *uid) {
+
+    uint32_t pwd = ul_ev1_pwdgenE(uid);
+    return (0xAD << 8 | ((pwd >> 24) & 0xFF) );
+}
+
+
 // default shims
 uint32_t ul_ev1_pwdgen_def(const uint8_t *uid) {
     return 0xFFFFFFFF;
diff --git a/common/generator.h b/common/generator.h
index 60da278f3..c94f311be 100644
--- a/common/generator.h
+++ b/common/generator.h
@@ -33,6 +33,7 @@ uint16_t ul_ev1_packgenA(const uint8_t *uid);
 uint16_t ul_ev1_packgenB(const uint8_t *uid);
 uint16_t ul_ev1_packgenC(const uint8_t *uid);
 uint16_t ul_ev1_packgenD(const uint8_t *uid);
+uint16_t ul_ev1_packgenE(const uint8_t *uid);
 
 int mfc_algo_ving_one(uint8_t *uid, uint8_t sector, uint8_t keytype, uint64_t *key);
 int mfc_algo_ving_all(uint8_t *uid, uint8_t *keys);
