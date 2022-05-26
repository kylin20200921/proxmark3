commit 0f6061c16f072372b7d4d381911f1542afbc3a69
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Feb 11 22:12:46 2022 +0100

    remove debug message

diff --git a/common/generator.c b/common/generator.c
index b449beeac..4856327e2 100644
--- a/common/generator.c
+++ b/common/generator.c
@@ -177,7 +177,6 @@ uint32_t ul_ev1_pwdgenE(const uint8_t *uid) {
 uint32_t ul_ev1_pwdgenF(const uint8_t *uid) {
     uint8_t hash[16];
     mbedtls_md5(uid, 7, hash);
-    PrintAndLogEx(INFO, "md5 %s", sprint_hex_inrow(hash, sizeof(hash)));
     uint32_t pwd = 0;
     pwd |= hash[0] << 24;
     pwd |= hash[1] << 16;
