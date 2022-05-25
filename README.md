commit 04fe021a5f2c8bc445df00be3e181593f9f8037d
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Jan 6 20:07:03 2022 +0100

    missing files

diff --git a/client/android/CMakeLists.txt b/client/android/CMakeLists.txt
index 9bc0f59cd..b66fe4e24 100644
--- a/client/android/CMakeLists.txt
+++ b/client/android/CMakeLists.txt
@@ -79,6 +79,7 @@ add_library(pm3rrg_rdv4 SHARED
         ${PM3_ROOT}/client/src/fido/fidocore.c
         ${PM3_ROOT}/client/src/iso7816/apduinfo.c
         ${PM3_ROOT}/client/src/iso7816/iso7816core.c
+        ${PM3_ROOT}/client/src/ksx6924/ksx6924core.c
         ${PM3_ROOT}/client/src/cipurse/cipursecrypto.c
         ${PM3_ROOT}/client/src/cipurse/cipursecore.c
         ${PM3_ROOT}/client/src/cipurse/cipursetest.c
@@ -124,6 +125,7 @@ add_library(pm3rrg_rdv4 SHARED
         ${PM3_ROOT}/client/src/cmdhfcipurse.c
         ${PM3_ROOT}/client/src/cmdhficlass.c
         ${PM3_ROOT}/client/src/cmdhfjooki.c
+        ${PM3_ROOT}/client/src/cmdhfksx6924.c
         ${PM3_ROOT}/client/src/cmdhflegic.c
         ${PM3_ROOT}/client/src/cmdhflist.c
         ${PM3_ROOT}/client/src/cmdhflto.c
@@ -172,6 +174,7 @@ add_library(pm3rrg_rdv4 SHARED
         ${PM3_ROOT}/client/src/cmdlfti.c
         ${PM3_ROOT}/client/src/cmdlfviking.c
         ${PM3_ROOT}/client/src/cmdlfvisa2000.c
+        ${PM3_ROOT}/client/src/cmdlfzx8211.c
         ${PM3_ROOT}/client/src/cmdmain.c
         ${PM3_ROOT}/client/src/cmdnfc.c
         ${PM3_ROOT}/client/src/cmdparser.c
@@ -196,6 +199,7 @@ add_library(pm3rrg_rdv4 SHARED
         ${PM3_ROOT}/client/src/util.c
         ${PM3_ROOT}/client/src/wiegand_formats.c
         ${PM3_ROOT}/client/src/wiegand_formatutils.c
+        ${CMAKE_BINARY_DIR}/version_pm3.c
         # android resources
         jni_tools.c
         pm3_main.c
diff --git a/client/experimental_lib/CMakeLists.txt b/client/experimental_lib/CMakeLists.txt
index c4ab858f7..a693a37e6 100644
--- a/client/experimental_lib/CMakeLists.txt
+++ b/client/experimental_lib/CMakeLists.txt
@@ -213,6 +213,7 @@ set (TARGET_SOURCES
         ${PM3_ROOT}/client/src/fido/fidocore.c
         ${PM3_ROOT}/client/src/iso7816/apduinfo.c
         ${PM3_ROOT}/client/src/iso7816/iso7816core.c
+        ${PM3_ROOT}/client/src/ksx6924/ksx6924core.c
         ${PM3_ROOT}/client/src/cipurse/cipursecrypto.c
         ${PM3_ROOT}/client/src/cipurse/cipursecore.c
         ${PM3_ROOT}/client/src/cipurse/cipursetest.c
@@ -258,6 +259,7 @@ set (TARGET_SOURCES
         ${PM3_ROOT}/client/src/cmdhfcipurse.c
         ${PM3_ROOT}/client/src/cmdhficlass.c
         ${PM3_ROOT}/client/src/cmdhfjooki.c
+        ${PM3_ROOT}/client/src/cmdhfksx6924.c
         ${PM3_ROOT}/client/src/cmdhflegic.c
         ${PM3_ROOT}/client/src/cmdhflist.c
         ${PM3_ROOT}/client/src/cmdhflto.c
@@ -306,6 +308,7 @@ set (TARGET_SOURCES
         ${PM3_ROOT}/client/src/cmdlfti.c
         ${PM3_ROOT}/client/src/cmdlfviking.c
         ${PM3_ROOT}/client/src/cmdlfvisa2000.c
+        ${PM3_ROOT}/client/src/cmdlfzx8211.c
         ${PM3_ROOT}/client/src/cmdmain.c
         ${PM3_ROOT}/client/src/cmdnfc.c
         ${PM3_ROOT}/client/src/cmdparser.c
