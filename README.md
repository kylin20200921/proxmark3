commit 7d151057c251533ced5cd0eccbe433537c3b2267
Author: iceman1001 <iceman@iuse.se>
Date:   Sat May 15 10:49:15 2021 +0200

    update test for mf_nonce_brute

diff --git a/tools/pm3_tests.sh b/tools/pm3_tests.sh
index 19d70625f..c820d611d 100755
--- a/tools/pm3_tests.sh
+++ b/tools/pm3_tests.sh
@@ -275,8 +275,8 @@ while true; do
     if $TESTALL || $TESTMFNONCEBRUTE; then
       echo -e "\n${C_BLUE}Testing mf_nonce_brute:${C_NC} ${MFNONCEBRUTEBIN:=./tools/mf_nonce_brute/mf_nonce_brute}"
       if ! CheckFileExist "mf_nonce_brute exists"          "$MFNONCEBRUTEBIN"; then break; fi
-#      if ! CheckExecute slow "mf_nonce_brute test"         "$MFNONCEBRUTEBIN 9c599b32 5a920d85 1011 98d76b77 d6c6e870 0000 ca7e0b63 0111 3e709c8a" "Key found \[.*ffffffffffff.*\]"; then break; fi
-      if ! CheckExecute slow "mf_nonce_brute test"         "$MFNONCEBRUTEBIN 96519578 d7e3c6ac 0011 cd311951 9da49e49 0010 2bb22e00 0100 a4f7f398" "Key found \[.*3b7e4fd575ad.*\]"; then break; fi
+      if ! CheckExecute slow "mf_nonce_brute test"         "$MFNONCEBRUTEBIN 9c599b32 5a920d85 1011 98d76b77 d6c6e870 0000 ca7e0b63 0111 3e709c8a" "Key found \[.*ffffffffffff.*\]"; then break; fi
+#      if ! CheckExecute slow "mf_nonce_brute test"         "$MFNONCEBRUTEBIN 96519578 d7e3c6ac 0011 cd311951 9da49e49 0010 2bb22e00 0100 a4f7f398" "Key found \[.*3b7e4fd575ad.*\]"; then break; fi
     fi
     # hitag2crack not yet part of "all"
     # if $TESTALL || $TESTHITAG2CRACK; then
