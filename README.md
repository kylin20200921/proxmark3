commit 22c1078b858b084239536f569fb2c4afbded8867
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 29 16:57:31 2022 +0100

    added pwd generator for NTAG tools.  md5 of uid

diff --git a/client/src/cmdhfmfu.c b/client/src/cmdhfmfu.c
index 105ff4da9..ac333df6f 100644
--- a/client/src/cmdhfmfu.c
+++ b/client/src/cmdhfmfu.c
@@ -3203,6 +3203,7 @@ static int CmdHF14AMfUPwdGen(const char *Cmd) {
     PrintAndLogEx(INFO, " Lego Dimension  | %08X | %04X", ul_ev1_pwdgenC(uid), ul_ev1_packgenC(uid));
     PrintAndLogEx(INFO, " XYZ 3D printer  | %08X | %04X", ul_ev1_pwdgenD(uid), ul_ev1_packgenD(uid));
     PrintAndLogEx(INFO, " Xiaomi purifier | %08X | %04X", ul_ev1_pwdgenE(uid), ul_ev1_packgenE(uid));
+    PrintAndLogEx(INFO, " NTAG tools      | %08X | %04X", ul_ev1_pwdgenF(uid), ul_ev1_packgen_def(uid));
     PrintAndLogEx(INFO, "-----------------+----------+-----");
     PrintAndLogEx(INFO, " Vingcard algo");
     PrintAndLogEx(INFO, "----------------------------------");
diff --git a/common/generator.c b/common/generator.c
index 08ca05a75..b449beeac 100644
--- a/common/generator.c
+++ b/common/generator.c
@@ -29,6 +29,7 @@
 #include "pm3_cmd.h"
 #include "ui.h"
 #include "mbedtls/sha1.h"
+#include "mbedtls/md5.h"
 #include "crc16.h"        // crc16 ccitt
 
 // Implementation tips:
@@ -172,6 +173,19 @@ uint32_t ul_ev1_pwdgenE(const uint8_t *uid) {
     return pwd;
 }
 
+// NDEF tools format password generator
+uint32_t ul_ev1_pwdgenF(const uint8_t *uid) {
+    uint8_t hash[16];
+    mbedtls_md5(uid, 7, hash);
+    PrintAndLogEx(INFO, "md5 %s", sprint_hex_inrow(hash, sizeof(hash)));
+    uint32_t pwd = 0;
+    pwd |= hash[0] << 24;
+    pwd |= hash[1] << 16;
+    pwd |= hash[2] << 8;
+    pwd |= hash[3];
+    return pwd;
+}
+
 // pack generation for algo 1-3
 uint16_t ul_ev1_packgenA(const uint8_t *uid) {
     uint16_t pack = (uid[0] ^ uid[1] ^ uid[2]) << 8 | (uid[2] ^ 8);
@@ -527,7 +541,7 @@ int mfc_algo_touch_one(uint8_t *uid, uint8_t sector, uint8_t keytype, uint64_t *
 //------------------------------------
 int generator_selftest(void) {
 
-#define NUM_OF_TEST     6
+#define NUM_OF_TEST     8
 
     PrintAndLogEx(INFO, "PWD / KEY generator selftest");
     PrintAndLogEx(INFO, "----------------------------");
@@ -563,29 +577,32 @@ int generator_selftest(void) {
         testresult++;
     PrintAndLogEx(success ? SUCCESS : WARNING, "UID | %s | %08X - %s", sprint_hex(uid4, 7), pwd4, success ? "OK" : "->72B1EC61<--");
 
-
     uint8_t uid5[] = {0x04, 0xA0, 0x3C, 0xAA, 0x1E, 0x70, 0x80};
     uint32_t pwd5 = ul_ev1_pwdgenE(uid5);
     success = (pwd5 == 0xCD91AFCC);
-//    if (success)
-//        testresult++;
-
+    if (success)
+        testresult++;
     PrintAndLogEx(success ? SUCCESS : WARNING, "UID | %s | %08X - %s", sprint_hex(uid5, 7), pwd5, success ? "OK" : "->CD91AFCC<--");
 
-
+    uint8_t uid6[] = {0x04, 0x77, 0x42, 0xAB, 0xEF, 0x42, 0x70};
+    uint32_t pwd6 = ul_ev1_pwdgenF(uid6);
+    success = (pwd6 == 0xA9C4C3C0);
+    if (success)
+        testresult++;
+    PrintAndLogEx(success ? SUCCESS : WARNING, "UID | %s | %08X - %s", sprint_hex(uid6, 7), pwd6, success ? "OK" : "->A9C4C3C0<--");
 
 //    uint8_t uid5[] = {0x11, 0x22, 0x33, 0x44};
 //    uint64_t key1 = mfc_algo_a(uid5);
 //    success = (key1 == 0xD1E2AA68E39A);
 //    PrintAndLogEx(success ? SUCCESS : WARNING, "UID | %s | %"PRIx64" - %s", sprint_hex(uid5, 4), key1, success ? "OK" : "->D1E2AA68E39A<--");
 
-    uint8_t uid6[] = {0x74, 0x57, 0xCA, 0xA9};
-    uint64_t key6 = 0;
-    mfc_algo_sky_one(uid6, 15, 0, &key6);
-    success = (key6 == 0x82c7e64bc565);
+    uint8_t uid7[] = {0x74, 0x57, 0xCA, 0xA9};
+    uint64_t key7 = 0;
+    mfc_algo_sky_one(uid7, 15, 0, &key7);
+    success = (key7 == 0x82c7e64bc565);
     if (success)
         testresult++;
-    PrintAndLogEx(success ? SUCCESS : WARNING, "UID | %s          | %"PRIx64" - %s", sprint_hex(uid6, 4), key6, success ? "OK" : "->82C7E64BC565<--");
+    PrintAndLogEx(success ? SUCCESS : WARNING, "UID | %s          | %"PRIx64" - %s", sprint_hex(uid7, 4), key7, success ? "OK" : "->82C7E64BC565<--");
 
 
     uint32_t lf_id = lf_t55xx_white_pwdgen(0x00000080);
diff --git a/common/generator.h b/common/generator.h
index c94f311be..5fc81ba25 100644
--- a/common/generator.h
+++ b/common/generator.h
@@ -27,6 +27,7 @@ uint32_t ul_ev1_pwdgenB(const uint8_t *uid);
 uint32_t ul_ev1_pwdgenC(const uint8_t *uid);
 uint32_t ul_ev1_pwdgenD(const uint8_t *uid);
 uint32_t ul_ev1_pwdgenE(const uint8_t *uid);
+uint32_t ul_ev1_pwdgenF(const uint8_t *uid);
 
 uint16_t ul_ev1_packgen_def(const uint8_t *uid);
 uint16_t ul_ev1_packgenA(const uint8_t *uid);
