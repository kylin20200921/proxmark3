commit 47a851df2ac62741c583a892e2821aaae02f5c31
Author: iceman1001 <iceman@iuse.se>
Date:   Tue Jun 15 11:37:32 2021 +0200

    maur keys

diff --git a/client/dictionaries/mfc_default_keys.dic b/client/dictionaries/mfc_default_keys.dic
index d22d2e7c2..d2c41cbe7 100644
--- a/client/dictionaries/mfc_default_keys.dic
+++ b/client/dictionaries/mfc_default_keys.dic
@@ -6,7 +6,8 @@
 ffffffffffff # Defaultkey(firstkeyusedbyprogramifnouserdefinedkey)
 000000000000 # Blankkey
 a0a1a2a3a4a5 # NFC Forum MADkey
-A5A4A3A2A1A0 # MAD access key (reversed) 
+A5A4A3A2A1A0 # MAD access key A (reversed) 
+89ECA97F8C2A # MAD access key B
 b0b1b2b3b4b5
 c0c1c2c3c4c5
 d0d1d2d3d4d5
@@ -289,6 +290,49 @@ c2b7ec7d4eb1
 ac70ca327a04
 eb0a8ff88ade
 #
+# Transport system Metromoney
+2803bcb0c7e1 
+9c616585e26d 
+4fa9eb49f75e
+2dade48942c5
+a160fcd5ec4c
+112233445566
+361a62f35bc9
+# 
+# Transport system Spain
+83f3cb98c258
+070d486bc555
+a9b018868cc1
+9dcdb136110c
+749934cc8ed3
+506db955f161
+f088a85e71d7
+72b458d60363
+70c714869dc7
+b32464412ee3
+f253c30568c4
+1c68315674ac
+cfe63749080a
+c1e6f8afc9ec
+dd0de3ba08a6
+3d923eb73534
+ff94f86b09a6
+d61707ffdfb1
+8223205047b6
+9951a273dee7
+c9449301af93
+66695a45c9fa
+89aa9d743812
+c41514defc07
+c52876869800
+5353b3aecb53
+2e4169a5c79d
+4bb747e48c2a
+6285a1c8eb5c
+5145c34dba19
+25352912cd8d
+81b20c274c3f
+#
 # Data from https://github.com/RadioWar/NFCGUI
 44dd5a385aaf
 21a600056cb0
diff --git a/client/dictionaries/mfdes_default_keys.dic b/client/dictionaries/mfdes_default_keys.dic
index a26d13d32..9e0a50fd0 100644
--- a/client/dictionaries/mfdes_default_keys.dic
+++ b/client/dictionaries/mfdes_default_keys.dic
@@ -9,6 +9,7 @@
 6AC292FAA1315B4D858AB3A3D7D5933A
 404142434445464748494a4b4c4d4e4f
 3112B738D8862CCD34302EB299AAB456                  # Gallagher AES (https://pastebin.com/GkbGLz8r)
+47454D5850524553534F53414D504C45                  # Gemalto 
 00112233445566778899aabbccddeeff
 2b7e151628aed2a6abf7158809cf4f3c
 fbeed618357133667c85e08f7236a8de
diff --git a/client/dictionaries/mfp_default_keys.dic b/client/dictionaries/mfp_default_keys.dic
index a063e34ad..f8fcc6972 100644
--- a/client/dictionaries/mfp_default_keys.dic
+++ b/client/dictionaries/mfp_default_keys.dic
@@ -1,3 +1,4 @@
+47454D5850524553534F53414D504C45  # Gemalto
 ffffffffffffffffffffffffffffffff
 00000000000000000000000000000000
 a0a1a2a3a4a5a6a7a0a1a2a3a4a5a6a7
