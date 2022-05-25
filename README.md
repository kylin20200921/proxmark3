commit 78b48a6ce700143b467a525567fa42894b9b944a
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Sep 4 22:13:54 2021 +0200

    change default standalone mode to LF_SAMYRUN

diff --git a/CHANGELOG.md b/CHANGELOG.md
index 88acfff2e..9913bb9f9 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,7 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Changed default standalone mode to LF_SAMYRUN (@iceman1001)
  - Add MIFARE Plus SL3 mode traces (@iceman1001)
  - Fix PAC/Stanley checksum calculation (@rknoll)
  - Added option --mem to `hf mf nested`, from icopyx source (@doegox)
diff --git a/Makefile.platform.sample b/Makefile.platform.sample
index 4ecb3b9f6..d1d95cb90 100644
--- a/Makefile.platform.sample
+++ b/Makefile.platform.sample
@@ -5,4 +5,4 @@ PLATFORM=PM3RDV4
 #PLATFORM=PM3GENERIC
 # If you want more than one PLATFORM_EXTRAS option, separate them by spaces:
 #PLATFORM_EXTRAS=BTADDON
-#STANDALONE=HF_MSDSAL
+#STANDALONE=LF_SAMYRUN
diff --git a/armsrc/Standalone/Makefile.hal b/armsrc/Standalone/Makefile.hal
index c7879987a..944fb5912 100644
--- a/armsrc/Standalone/Makefile.hal
+++ b/armsrc/Standalone/Makefile.hal
@@ -1,5 +1,5 @@
 # Default standalone if no standalone specified
-DEFAULT_STANDALONE=HF_MSDSAL
+DEFAULT_STANDALONE=LF_SAMYRUN
 HELP_EXAMPLE_STANDALONE=LF_SAMYRUN
 # (you can set explicitly STANDALONE= to disable standalone modes)
 STANDALONE?=$(DEFAULT_STANDALONE)
@@ -39,7 +39,7 @@ define KNOWN_STANDALONE_DEFINITIONS
 |                 | - Brad Antoniewicz                     |
 +----------------------------------------------------------+
 | LF_SAMYRUN      | HID26 read/clone/sim                   |
-|                 | - Samy Kamkar                          |
+| (default)       | - Samy Kamkar                          |
 +----------------------------------------------------------+
 | LF_THAREXDE     | Simulate/read EM4x50 tags              |
 | (RDV4 only)     | storing in flashmem                    |
@@ -72,7 +72,7 @@ define KNOWN_STANDALONE_DEFINITIONS
 | (RDV4 only)     | storing in flashmem - Ray Lee          |
 +----------------------------------------------------------+
 | HF_MSDSAL       | Read and emulate MSD Visa cards        |
-| (default)       | - Salvador Mendoza                     |
+|                 | - Salvador Mendoza                     |
 +----------------------------------------------------------+
 | HF_REBLAY       | 14A Relay over BT                      |
 | (RDV4 only)     |  - Salvador Mendoza                    |
diff --git a/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md b/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md
index 018ac490e..796e250f2 100644
--- a/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md
+++ b/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md
@@ -87,7 +87,7 @@ Here are the supported values you can assign to `STANDALONE` in `Makefile.platfo
 | LF_ICEHID       | LF HID collector to flashmem - Iceman1001
 | LF_NEXID        | Nexwatch credentials detection mode - jrjgjk & Zolorah
 | LF_PROXBRUTE    | HID ProxII bruteforce - Brad Antoniewicz
-| LF_SAMYRUN      | HID26 read/clone/sim - Samy Kamkar
+| LF_SAMYRUN (def)| HID26 read/clone/sim - Samy Kamkar
 | LF_SKELETON     | standalone mode skeleton - Iceman1001
 | LF_THAREXDE     | LF EM4x50 simulator/read standalone mode - tharexde
 | HF_14ASNIFF     | 14a sniff storing to flashmem - Micolous
@@ -99,13 +99,13 @@ Here are the supported values you can assign to `STANDALONE` in `Makefile.platfo
 | HF_LEGIC        | HF Legic Prime standalone - uhei
 | HF_MATTYRUN     | Mifare sniff/clone - Matías A. Ré Medina
 | HF_MFCSIM       | Simulate Mifare Classic 1k card storing in flashmem - Ray Lee
-| HF_MSDSAL  (def)| EMV Read and emulation - Salvador Mendoza
+| HF_MSDSAL       | EMV Read and emulation - Salvador Mendoza
 | HF_REBLAY       | 14A relay over BT  - Salvador Mendoza
 | HF_TCPRST       | IKEA Rothult ST25TA, Standalone Master Key Dump/Emulation - Nick Draffen
 | HF_TMUDFORD     | Read and emulate ISO15693 card UID - Tim Mudford
 | HF_YOUNG        | Mifare sniff/simulation - Craig Young
 
-By default `STANDALONE=HF_MSDSAL`.
+By default `STANDALONE=LF_SAMYRUN`.
 
 ## 256kb versions
 
