commit e1bcbd81590d4e169b8b97db81d72689870e93e4
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Jan 3 22:09:42 2022 +0100

    select card before trying to send apdu, makes the pm3 not get stuck

diff --git a/common/cardhelper.c b/common/cardhelper.c
index db62c49f6..e337010e6 100644
--- a/common/cardhelper.c
+++ b/common/cardhelper.c
@@ -53,7 +53,7 @@ static bool executeCrypto(uint8_t ins, uint8_t *src, uint8_t *dest) {
 
     int resp_len = 0;
     uint8_t dec[11] = {0};
-    ExchangeAPDUSC(false, cmd, sizeof(cmd), false, true, dec, sizeof(dec), &resp_len);
+    ExchangeAPDUSC(false, cmd, sizeof(cmd), true, true, dec, sizeof(dec), &resp_len);
     if (resp_len == 10) {
         memcpy(dest, dec, 8);
         return true;
