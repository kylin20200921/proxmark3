commit e854c4176070a77045db72647c93fa65adab0688
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Sep 5 10:44:13 2021 +0200

    android cmake file does not match pm3 client

diff --git a/client/android/CMakeLists.txt b/client/android/CMakeLists.txt
index 262403be7..bd5a9d59b 100644
--- a/client/android/CMakeLists.txt
+++ b/client/android/CMakeLists.txt
@@ -79,22 +79,30 @@ add_library(pm3rrg_rdv4 SHARED
         ${PM3_ROOT}/client/src/fido/fidocore.c
         ${PM3_ROOT}/client/src/iso7816/apduinfo.c
         ${PM3_ROOT}/client/src/iso7816/iso7816core.c
+        ${PM3_ROOT}/client/src/cipurse/cipursecrypto.c
+        ${PM3_ROOT}/client/src/cipurse/cipursecore.c
+        ${PM3_ROOT}/client/src/cipurse/cipursetest.c
         ${PM3_ROOT}/client/src/loclass/cipher.c
         ${PM3_ROOT}/client/src/loclass/cipherutils.c
         ${PM3_ROOT}/client/src/loclass/elite_crack.c
         ${PM3_ROOT}/client/src/loclass/hash1_brute.c
         ${PM3_ROOT}/client/src/loclass/ikeys.c
         ${PM3_ROOT}/client/src/mifare/mad.c
+        ${PM3_ROOT}/client/src/mifare/aiddesfire.c
         ${PM3_ROOT}/client/src/mifare/mfkey.c
         ${PM3_ROOT}/client/src/mifare/mifare4.c
         ${PM3_ROOT}/client/src/mifare/mifaredefault.c
         ${PM3_ROOT}/client/src/mifare/mifarehost.c
         ${PM3_ROOT}/client/src/nfc/ndef.c
-        ${PM3_ROOT}/client/src/mifare/desfire_crypto.c
+        ${PM3_ROOT}/client/src/mifare/lrpcrypto.c
+        ${PM3_ROOT}/client/src/mifare/desfirecrypto.c
+        ${PM3_ROOT}/client/src/mifare/desfiresecurechan.c
+        ${PM3_ROOT}/client/src/mifare/desfirecore.c
+        ${PM3_ROOT}/client/src/mifare/desfiretest.c
         ${PM3_ROOT}/client/src/uart/uart_posix.c
         ${PM3_ROOT}/client/src/uart/uart_win32.c
         ${PM3_ROOT}/client/src/ui/overlays.ui
-        ${PM3_ROOT}/client/src/aiddesfire.c
+        ${PM3_ROOT}/client/src/ui/image.ui
         ${PM3_ROOT}/client/src/aidsearch.c
         ${PM3_ROOT}/client/src/cmdanalyse.c
         ${PM3_ROOT}/client/src/cmdcrc.c
@@ -110,6 +118,7 @@ add_library(pm3rrg_rdv4 SHARED
         ${PM3_ROOT}/client/src/cmdhfepa.c
         ${PM3_ROOT}/client/src/cmdhffelica.c
         ${PM3_ROOT}/client/src/cmdhffido.c
+        ${PM3_ROOT}/client/src/cmdhfcipurse.c
         ${PM3_ROOT}/client/src/cmdhficlass.c
         ${PM3_ROOT}/client/src/cmdhfjooki.c
         ${PM3_ROOT}/client/src/cmdhflegic.c
@@ -120,6 +129,7 @@ add_library(pm3rrg_rdv4 SHARED
         ${PM3_ROOT}/client/src/cmdhfmfhard.c
         ${PM3_ROOT}/client/src/cmdhfmfp.c
         ${PM3_ROOT}/client/src/cmdhfmfu.c
+        ${PM3_ROOT}/client/src/cmdhfseos.c
         ${PM3_ROOT}/client/src/cmdhfst.c
         ${PM3_ROOT}/client/src/cmdhfst25ta.c
         ${PM3_ROOT}/client/src/cmdhfthinfilm.c
@@ -195,7 +205,10 @@ target_include_directories(pm3rrg_rdv4 PRIVATE
         ${PM3_ROOT}/include/
         ${PM3_ROOT}/common
         ${PM3_ROOT}/common_fpga
-        ${PM3_ROOT}/client/src)
+        ${PM3_ROOT}/include
+        ${PM3_ROOT}/client/src
+        ${PM3_ROOT}/client/include
+
 
 target_link_libraries(pm3rrg_rdv4
         ${BZIP2_LIBRARIES}
