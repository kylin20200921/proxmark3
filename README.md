commit 0587845c5622e22df07c0feb72ff990450267441
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jan 23 01:32:14 2022 +0100

    added another pwdgen algo

diff --git a/CHANGELOG.md b/CHANGELOG.md
index 101cce452..79ab4059e 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,10 +3,11 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Changed `hf mfu pwdgen` - now generate xiaomi air purifier pwd/pack (@doegox)
  - Fix `hf 14a sim` - sneaky detection of user supplied UID might be empty (@iceman1001)
- - Adds support for PCSC's proprietary HID 37bit format P10004 (@bthedorff)
- - Add `nfc decode` - now NDEF vCard messages with a PHOTO in base64 format is shown (@iceman1001)
- - Remove AID limitations when using Gallagher key diversification (@DarkMatterMatt)
+ - Added support for PCSC's proprietary HID 37bit format P10004 (@bthedorff)
+ - Added `nfc decode` - now NDEF vCard messages with a PHOTO in base64 format is shown (@iceman1001)
+ - Changed - AID limitations when using Gallagher key diversification (@DarkMatterMatt)
 
 ## [Frostbit.4.14831] [2022-01-11]
  - Changed Wiegand format lookup - now case-insensitive (@iceman1001)
diff --git a/client/src/cmdhfmfu.c b/client/src/cmdhfmfu.c
index df8715e72..012cce720 100644
--- a/client/src/cmdhfmfu.c
+++ b/client/src/cmdhfmfu.c
@@ -1157,12 +1157,14 @@ static mfu_identify_t mfu_ident_table[] = {
         NULL, NULL,
         "hf mfu dump -k %08x"
     },
+    /*
     {
-        "Amiibo", "0004040201001103",
-        9, 9, "480FE0F110FFEEA500",
-        ul_ev1_pwdgenB, ul_ev1_packgenB,
+        "Xiaomi AIR Purifier", "0004040201000F03",
+        0, 0, "",
+        ul_ev1_pwdgenE, ul_ev1_packgen_def,
         "hf mfu dump -k %08x"
     },
+    */
     {NULL, NULL, 0, 0, NULL, NULL, NULL, NULL}
 };
 
@@ -3177,18 +3179,19 @@ static int CmdHF14AMfUPwdGen(const char *Cmd) {
         }
     }
 
-    PrintAndLogEx(INFO, "---------------------------------");
+    PrintAndLogEx(INFO, "------------------.---------------");
     PrintAndLogEx(INFO, " Using UID : %s", sprint_hex(uid, 7));
-    PrintAndLogEx(INFO, "---------------------------------");
-    PrintAndLogEx(INFO, " algo           | pwd      | pack");
-    PrintAndLogEx(INFO, "----------------+----------+-----");
-    PrintAndLogEx(INFO, " EV1            | %08X | %04X", ul_ev1_pwdgenA(uid), ul_ev1_packgenA(uid));
-    PrintAndLogEx(INFO, " Amiibo         | %08X | %04X", ul_ev1_pwdgenB(uid), ul_ev1_packgenB(uid));
-    PrintAndLogEx(INFO, " Lego Dimension | %08X | %04X", ul_ev1_pwdgenC(uid), ul_ev1_packgenC(uid));
-    PrintAndLogEx(INFO, " XYZ 3D printer | %08X | %04X", ul_ev1_pwdgenD(uid), ul_ev1_packgenD(uid));
-    PrintAndLogEx(INFO, "----------------+----------+-----");
+    PrintAndLogEx(INFO, "----------------------------------");
+    PrintAndLogEx(INFO, " algo            | pwd      | pack");
+    PrintAndLogEx(INFO, "-----------------+----------+-----");
+    PrintAndLogEx(INFO, " EV1             | %08X | %04X", ul_ev1_pwdgenA(uid), ul_ev1_packgenA(uid));
+    PrintAndLogEx(INFO, " Amiibo          | %08X | %04X", ul_ev1_pwdgenB(uid), ul_ev1_packgenB(uid));
+    PrintAndLogEx(INFO, " Lego Dimension  | %08X | %04X", ul_ev1_pwdgenC(uid), ul_ev1_packgenC(uid));
+    PrintAndLogEx(INFO, " XYZ 3D printer  | %08X | %04X", ul_ev1_pwdgenD(uid), ul_ev1_packgenD(uid));
+    PrintAndLogEx(INFO, " Xiaomi purifier | %08X | %04X", ul_ev1_pwdgenE(uid), ul_ev1_packgen_def(uid));
+    PrintAndLogEx(INFO, "-----------------+----------+-----");
     PrintAndLogEx(INFO, " Vingcard algo");
-    PrintAndLogEx(INFO, "---------------------------------");
+    PrintAndLogEx(INFO, "----------------------------------");
     return PM3_SUCCESS;
 }
 
diff --git a/common/generator.c b/common/generator.c
index 9ff79727f..a800d01bf 100644
--- a/common/generator.c
+++ b/common/generator.c
@@ -160,6 +160,18 @@ uint32_t ul_ev1_pwdgenD(const uint8_t *uid) {
     return BSWAP_32(pwd);
 }
 
+// AIR purifier Xiaomi
+uint32_t ul_ev1_pwdgenE(const uint8_t *uid) {
+    uint8_t hash[20];
+    mbedtls_sha1(uid, 7, hash);
+    uint32_t pwd = 0;
+    pwd |= ( hash[ hash[0] % 20 ] ) << 24 ;
+    pwd |= ( hash[ (hash[0] + 5 ) % 20 ] ) << 16;
+    pwd |= ( hash[ (hash[0] + 13) % 20 ] ) << 8;
+    pwd |= ( hash[ (hash[0] + 17) % 20 ] );
+    return pwd;
+}
+
 // pack generation for algo 1-3
 uint16_t ul_ev1_packgenA(const uint8_t *uid) {
     uint16_t pack = (uid[0] ^ uid[1] ^ uid[2]) << 8 | (uid[2] ^ 8);
@@ -544,6 +556,17 @@ int generator_selftest(void) {
         testresult++;
     PrintAndLogEx(success ? SUCCESS : WARNING, "UID | %s | %08X - %s", sprint_hex(uid4, 7), pwd4, success ? "OK" : "->72B1EC61<--");
 
+
+    uint8_t uid5[] = {0x04, 0xA0, 0x3C, 0xAA, 0x1E, 0x70, 0x80};
+    uint32_t pwd5 = ul_ev1_pwdgenE(uid5);
+    success = (pwd5 == 0xCD91AFCC);
+//    if (success)
+//        testresult++;
+
+    PrintAndLogEx(success ? SUCCESS : WARNING, "UID | %s | %08X - %s", sprint_hex(uid5, 7), pwd5, success ? "OK" : "->CD91AFCC<--");
+
+
+
 //    uint8_t uid5[] = {0x11, 0x22, 0x33, 0x44};
 //    uint64_t key1 = mfc_algo_a(uid5);
 //    success = (key1 == 0xD1E2AA68E39A);
diff --git a/common/generator.h b/common/generator.h
index 12c63412e..60da278f3 100644
--- a/common/generator.h
+++ b/common/generator.h
@@ -26,6 +26,7 @@ uint32_t ul_ev1_pwdgenA(const uint8_t *uid);
 uint32_t ul_ev1_pwdgenB(const uint8_t *uid);
 uint32_t ul_ev1_pwdgenC(const uint8_t *uid);
 uint32_t ul_ev1_pwdgenD(const uint8_t *uid);
+uint32_t ul_ev1_pwdgenE(const uint8_t *uid);
 
 uint16_t ul_ev1_packgen_def(const uint8_t *uid);
 uint16_t ul_ev1_packgenA(const uint8_t *uid);
