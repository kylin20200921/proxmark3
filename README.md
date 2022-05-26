commit 178f9bb1429401ea30bb2d039f5bb9d9485cdfaa
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Mar 20 09:35:37 2022 +0100

    pcs param is sometimes NULL here

diff --git a/armsrc/mifareutil.c b/armsrc/mifareutil.c
index ab69dc0a8..4a3832d71 100644
--- a/armsrc/mifareutil.c
+++ b/armsrc/mifareutil.c
@@ -117,7 +117,7 @@ uint16_t mifare_sendcmd_short(struct Crypto1State *pcs, uint8_t crypted, uint8_t
 
     if (answer_parity) *answer_parity = par[0];
 
-    if (crypted == CRYPT_ALL) {
+    if (pcs && (crypted == CRYPT_ALL)) {
         if (len == 1) {
             uint16_t res = 0;
             res |= (crypto1_bit(pcs, 0, 0) ^ BIT(answer[0], 0)) << 0;
