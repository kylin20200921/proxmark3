commit 1b6b16d91a7546ce01339f02664005555a42807a
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Apr 30 21:38:57 2022 +0200

    more default keys

diff --git a/CHANGELOG.md b/CHANGELOG.md
index 432f69ddb..3c07428f2 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,8 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Added more default keys (@CONIGUERO)
+ - Change `hf 14a info` - it now detects FUDAN (@iceman1001)  Thanks to @secit-pl
  - Fixed support to clone Pyramic, Paradox and Awid on EM4x05 (@doegox)
  - Added support to clone HID, Viking and EM410x on EM4x05 (@doegox)
  - Fixed `lf config --reset` - averaging is set to 1 rather than 0 (@wh201906)
diff --git a/client/dictionaries/mfc_default_keys.dic b/client/dictionaries/mfc_default_keys.dic
index b5d6c9843..b769c9808 100644
--- a/client/dictionaries/mfc_default_keys.dic
+++ b/client/dictionaries/mfc_default_keys.dic
@@ -1479,6 +1479,7 @@ D3F3B958B8A3
 2196FAD8115B
 7C469FE86855
 CE99FBC8BD26
+#
 # keys from Eurothermes group (Switzerland)
 d66d91829013
 75b691829013
@@ -1495,7 +1496,7 @@ fed791829013
 668091829013
 00008627c10a
 #
-#keys from NSP Manchester University UK Accomodation Staff and students 
+# keys from NSP Manchester University UK Accomodation Staff and students 
 199404281970
 199404281998
 #
@@ -1506,4 +1507,64 @@ fed791829013
 562E6EF73DB6
 F53E9F4114A9
 AD38C17DE7D2
-
+#
+# SUBE cards keys (new)
+#
+2DEB57A3EA8F
+32C1BB023F87
+70E3AD3F2D29
+202ECDCCC642
+3686192D813F
+24501C422387
+2C7813A721C3
+FFE04BE3D995
+D28F090677A1
+DE2D83E2DCCC
+A66A478712EA
+643232ADB2D5
+C7F4A4478415
+95C013B70D99
+3C383889362A
+3C6D9C4A90FA
+51BEDBA005E5
+74BF7363F354
+53B09DB89111
+E98075318085
+2F904641D75F
+7F60AEF68136
+F5C1B3F62FDA
+3E6E5713BA10
+8B75A29D4AB2
+7E6545076619
+#
+# SUBE cards keys (old)
+#
+7B296F353C6B
+4C5A766DFE3A
+32C6768847F5
+F68930789631
+8B42B6D64B02
+B627A3CB13F8
+3FA7217EC575
+562A4FB8260B
+88DDC24E1671
+91CB7802A559
+7A3E0F5B63FC
+8CA2C9DC8292
+5CCC6D50EAAC
+DE4F5AA9A7F3
+52D0145E1AF5
+C10F92A4E57E
+7D6E7AF43C97
+DE1E7D5F6DF1
+F4CB751B031A
+C54474936B59
+2A1F900D4533
+6303CDCBB233
+F115E91357B3
+BFE25035B0C8
+62FF943EB069
+7C82EF592001
+D5C172325DD3
+992B152E834A
+CE75D7EADEAF
