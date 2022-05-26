commit 8a95a62b00a34c927117dfe412b8308f8112afa4
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Apr 2 14:07:34 2022 +0200

    textual

diff --git a/CHANGELOG.md b/CHANGELOG.md
index 93cec3998..ab167c103 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,7 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Fixed `hf 14a apdu` - now don't skip first P2 iteration (@iceman1001)
  - Added `hf ntag424` - skeleton with SDM (@iceman1001)
  - Updated hf_14a_i2crevive.lua - fixed broken apdus (@Equipter)
  - Use 128 bit slices whenever ARM NEON is available to speed up hardnested (@Doridian)
diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index 44fbc4e82..2e9a7b8f3 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -2403,7 +2403,7 @@ static int CmdHf14AFindapdu(const char *Cmd) {
     PrintAndLogEx(NORMAL, "");
     PrintAndLogEx(SUCCESS, "Starting the APDU finder [ CLA " _GREEN_("%02X") " INS " _GREEN_("%02X") " P1 " _GREEN_("%02X") " P2 " _GREEN_("%02X") " ]", cla, ins, p1, p2);
 
-    bool inc_p1 = true;
+    bool inc_p1 = false;
     bool skip_ins = false;
     uint64_t all_sw[256][256] = { { 0 } };
     uint64_t sw_occurrences = 0;
diff --git a/client/src/cmdhfmfu.c b/client/src/cmdhfmfu.c
index 2821ba42d..b48cae3e8 100644
--- a/client/src/cmdhfmfu.c
+++ b/client/src/cmdhfmfu.c
@@ -25,7 +25,6 @@
 #include "cmdhfmf.h"
 #include "cmdhf14a.h"
 #include "comms.h"
-#include "fileutils.h"
 #include "protocols.h"
 #include "generator.h"
 #include "nfc/ndef.h"
@@ -4200,7 +4199,7 @@ static int CmdHF14AMfuView(const char *Cmd) {
             res = loadFileEML_safe(filename, (void **)&dump, &bytes_read);
             break;
         case DICTIONARY: {
-            PrintAndLogEx(ERR, "Error: Only BIN/JSON formats allowed");
+            PrintAndLogEx(ERR, "Error: Only BIN/EML/JSON formats allowed");
             free(dump);
             return PM3_EINVARG;
         }
