commit 0967ea19893574e269edef27cad1a85b97f60426
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jan 9 13:37:35 2022 +0100

    modified the default mfc hardcoded list to contain some more keys

diff --git a/client/src/mifare/mifaredefault.h b/client/src/mifare/mifaredefault.h
index ac78aedfa..50fa1c5ee 100644
--- a/client/src/mifare/mifaredefault.h
+++ b/client/src/mifare/mifaredefault.h
@@ -25,9 +25,8 @@ static const uint64_t g_mifare_default_keys[] = {
     0xffffffffffff, // Default key (first key used by program if no user defined key)
     0x000000000000, // Blank key
     0xa0a1a2a3a4a5, // NFCForum MAD key
+    0xd3f7d3f7d3f7, // NDEF public key
     0xb0b1b2b3b4b5,
-    0xc0c1c2c3c4c5,
-    0xd0d1d2d3d4d5,
     0xaabbccddeeff,
     0x1a2b3c4d5e6f,
     0x123456789abc,
@@ -36,7 +35,6 @@ static const uint64_t g_mifare_default_keys[] = {
     0xabcdef123456,
     0x4d3a99c351dd,
     0x1a982c7e459a,
-    0xd3f7d3f7d3f7, // NDEF public key
     0x714c5c886e97,
     0x587ee5f9350f,
     0xa0478cc39091,
@@ -44,7 +42,28 @@ static const uint64_t g_mifare_default_keys[] = {
     0x8fd0a4f256e9,
     0x0000014b5c31,
     0xb578f38a5c61,
-    0x96a301bce267
+    0x96a301bce267,
+    0xfc00018778f7,
+    0x6471a5ef2d1a, // SimonsVoss
+    0x4E3552426B32, // ID06
+    0x6A1987C40A21, // Salto
+    0xef1232ab18a0, // Schlage
+    0x3B7E4FD575AD, // 
+    0xb7bf0c13066e, // Gallagher
+    0x135b88a94b8b, // Saflock
+    0x5a7a52d5e20d, // Bosch
+    0x314B49474956, // VIGIK1 A
+    0x564c505f4d41, // VIGIK1 B
+    0x021209197591, // BTCINO
+    0x484558414354, // Intratone
+    0xEC0A9B1A9E06, // Vingcard
+    0x66b31e64ca4b, // Vingcard
+    0x97F5DA640B18, // Bangkok metro key
+    0xA8844B0BCA06, // Metro Valencia key
+    0xE4410EF8ED2D, // Armenian metro
+    0x857464D3AAD1, // HTC Eindhoven key
+    0x08B386463229, // troika
+    0xe00000000000, // icopy
 };
 
 static const uint8_t g_mifare_mad_key[] =  {0xa0, 0xa1, 0xa2, 0xa3, 0xa4, 0xa5};
