commit 1ed21a3ae8b0c4a3e5fb32f55c12f7636778774f
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jun 20 18:26:45 2021 +0200

    added more keys (@equipter)

diff --git a/CHANGELOG.md b/CHANGELOG.md
index 9d325f9d9..9c102b2a7 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,7 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Added more keys (@equipter)
  - Changed `hf nfc ndefread` - ndef parser now handles more types (@iceman1001)
  - Fix `hf desfire` changekey, GetUID, 3DES sesson key tweak. (@mwalker33)
  - Fix `hf fido` commands now works correctly (@merlokk) 
diff --git a/client/dictionaries/mfc_default_keys.dic b/client/dictionaries/mfc_default_keys.dic
index 872954ac7..4ac03904d 100644
--- a/client/dictionaries/mfc_default_keys.dic
+++ b/client/dictionaries/mfc_default_keys.dic
@@ -1413,6 +1413,7 @@ F678905568C3
 D1417E431949    
 4BF6DE347FB6
 #
+#
 3a471b2192bf
 a297ceb7d34b
 ae76242931f1
@@ -1421,3 +1422,8 @@ ae76242931f1
 124578ABFEDC
 ABFEDC124578
 4578ABFEDC12
+#
+# Data from 
+# premier inn hotel chain
+5e594208ef02    
+af9e38d36582 
