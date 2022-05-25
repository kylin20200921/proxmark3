commit b00506ed4fb3d206934ad215c4d726d39a98ab93
Author: iceman1001 <iceman@iuse.se>
Date:   Tue Sep 21 19:59:09 2021 +0200

    adapt hf 14a info to detect different types of FUDAN. see http://www.proxmark.org/forum/viewtopic.php?pid=41195#p41195

diff --git a/CHANGELOG.md b/CHANGELOG.md
index 5364c2b2b..2c32d4c3e 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,8 +3,9 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Changed `hf 14a info` - different FUDAN clone detection (@iceman1001)
  - Added `CROSS_CC` and similar args for maintainers, see Maintainers.md (@doegox)
- - crack5opencl: fix deadlock in wu_queue_destroy() + minor changes on threads.c (@matrix)
+ - Fix crack5opencl: fix deadlock in wu_queue_destroy() + minor changes on threads.c (@matrix)
 
 ## [crimson.4.14434][2021-09-18]
  - Fix `hf mf staticnested` - flashmem / non loop now works (@horrordash)
diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index 983bd5799..21541ba13 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -1746,7 +1746,12 @@ int infoHF14A(bool verbose, bool do_nack_test, bool do_aid_search) {
                         break;
                     }
                     case 0x0A: {
-                        printTag("FM11RF005SH (Shanghai Metro)");
+                        if (card.atqa[0] == 0x03)
+                            // Uses MIFARE Crypto-1 algo
+                            printTag("FM11RF005M (FUDAN MIFARE Classic clone)");
+                        else if (card.atqa[0] == 0x05)
+                            // Uses Shanghai algo
+                            printTag("FM11RF005SH (FUDAN Shanghai Metro)");
                         break;
                     }
                     case 0x20: {
