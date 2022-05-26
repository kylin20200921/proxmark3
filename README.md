commit 76da953bcb183dbdc7a62d04110277620c8f2f7e
Author: iceman1001 <iceman@iuse.se>
Date:   Mon May 16 19:46:31 2022 +0200

    text

diff --git a/client/src/cmdhfmfp.c b/client/src/cmdhfmfp.c
index fa8bfbd9e..dcd73fb35 100644
--- a/client/src/cmdhfmfp.c
+++ b/client/src/cmdhfmfp.c
@@ -171,7 +171,7 @@ static int plus_print_signature(uint8_t *uid, uint8_t uidlen, uint8_t *signature
 #define PUBLIC_PLUS_ECDA_KEYLEN 57
     const ecdsa_publickey_t nxp_plus_public_keys[] = {
         {"MIFARE Plus EV1",  "044409ADC42F91A8394066BA83D872FB1D16803734E911170412DDF8BAD1A4DADFD0416291AFE1C748253925DA39A5F39A1C557FFACD34C62E"},
-        {"MIFARE Pluc Ev_x", "04BB49AE4447E6B1B6D21C098C1538B594A11A4A1DBF3D5E673DEACDEB3CC512D1C08AFA1A2768CE20A200BACD2DC7804CD7523A0131ABF607"}
+        {"MIFARE Plus Ev_x", "04BB49AE4447E6B1B6D21C098C1538B594A11A4A1DBF3D5E673DEACDEB3CC512D1C08AFA1A2768CE20A200BACD2DC7804CD7523A0131ABF607"}
     };
 
     uint8_t i;
