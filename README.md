commit 67d6ba97a5180367088756c1e4d7ab08d95ef54d
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Nov 19 00:44:24 2021 +0100

    code style

diff --git a/armsrc/hitag2.c b/armsrc/hitag2.c
index 725deb910..693a5f9d2 100644
--- a/armsrc/hitag2.c
+++ b/armsrc/hitag2.c
@@ -633,7 +633,7 @@ static bool hitag2_write_page(uint8_t *rx, const size_t rxlen, uint8_t *tx, size
                 memcpy(tx, writedata, 4);
                 writestate = WRITE_STATE_PROG;
             } else {
-                Dbprintf("hitag2_write_page: Page number was not received correctly: rxlen=%d rx=%02x%02x%02x%02x",
+                Dbprintf("hitag2_write_page: Page number was not received correctly: rxlen %d rx %02x%02x%02x%02x",
                          rxlen, rx[0], rx[1], rx[2], rx[3]);
                 bSuccessful = false;
                 return false;
@@ -648,7 +648,7 @@ static bool hitag2_write_page(uint8_t *rx, const size_t rxlen, uint8_t *tx, size
             }
             return false;
         default:
-            DbpString("hitag2_write_page: Unknown state %d");
+            Dbprintf("hitag2_write_page: Unknown state %d", writestate);
             bSuccessful = false;
             return false;
     }
@@ -660,8 +660,8 @@ static bool hitag2_password(uint8_t *rx, const size_t rxlen, uint8_t *tx, size_t
     // Reset the transmission frame length
     *txlen = 0;
 
-    if (bPwd && !bAuthenticating && write) {
-        if (!hitag2_write_page(rx, rxlen, tx, txlen)) {
+    if (bPwd && (bAuthenticating == false) && write) {
+        if (hitag2_write_page(rx, rxlen, tx, txlen) == false) {
             return false;
         }
     } else {
@@ -682,7 +682,7 @@ static bool hitag2_password(uint8_t *rx, const size_t rxlen, uint8_t *tx, size_t
             // Received UID, tag password
             case 32: {
                 // stage 1, got UID
-                if (!bPwd) {
+                if (bPwd == false) {
                     bPwd = true;
                     bAuthenticating = true;
                     memcpy(tx, password, 4);
@@ -916,7 +916,7 @@ static bool hitag2_test_auth_attempts(uint8_t *rx, const size_t rxlen, uint8_t *
 
         // Received UID, crypto tag answer, or read block response
         case 32: {
-            if (!bCrypto) {
+            if (bCrypto == false) {
                 *txlen = 64;
                 memcpy(tx, NrAr, 8);
                 bCrypto = true;
