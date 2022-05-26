commit 1c6abece54f159d5fe949272fd5392a6995ae225
Merge: 21089fc34 a93225190
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Feb 25 11:42:20 2022 +0100

    Merge branch 'master' into sha

diff --cc CHANGELOG.md
index d6546b87e,7279766f6..2fda95253
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@@ -3,16 -3,17 +3,18 @@@ All notable changes to this project wil
  This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
  
  ## [unreleased][unreleased]
 + - Added detection of a possible mismatch between client and Proxmark3 image (@doegox)
-  - Changed `hf mf nested`: removed option `--single` redundant with usage of `--tblk` (@doegox)
-  - Fixed `hf mf chk` single block mode (@doegox)
-  - Fixed `hf mf fchk/chk` internal logic to load keys (@doegox)
-  - Changed `hf mf *` printKeyTable: now display sector trailer info too (@doegox)
-  - Changed `hf mf chk` option `--blk` into `--tblk` (as for nested) (@doegox)
+  - Changed `hf 14a info` - added a ATR historical compact TLV decoder (@iceman1001)
+  - Added `hf mf value` - decode a value block (@iceman1001)
+  - Changed `hf mf nested` - removed option `--single` redundant with usage of `--tblk` (@doegox)
+  - Fixed `hf mf chk` - single block mode (@doegox)
+  - Fixed `hf mf fchk/chk` - internal logic to load keys (@doegox)
+  - Changed `hf mf *` - printKeyTable: now display sector trailer info too (@doegox)
+  - Changed `hf mf chk` - option `--blk` into `--tblk` (as for nested) (@doegox)
   - Added new tool `mfd_multi_brute` - MIFARE DESfire / UL-C key recovery (@iceman1001)
-  - Fixed `hf emrtd info` segfault on some platforms (@doegox)
-  - Fixed `hf emrtd info` when offline (@doegox)
-  - Fixed `commands.json` generation (@doegox)
+  - Fixed `hf emrtd info` - segfault on some platforms (@doegox)
+  - Fixed `hf emrtd info` - when offline (@doegox)
+  - Fixed `commands.json` - generation (@doegox)
   - Added new standalone mode `hf_legicsim` (@uhei)
   - Changed `hf legic *` - now uses NG instead (@iceman1001)
   - Added `hf legic view` - view contents of LEGIC Prime dump files (@iceman1001)
@@@ -34,8 -35,9 +36,9 @@@
   - Added `nfc decode` - now NDEF vCard messages with a PHOTO in base64 format is shown (@iceman1001)
   - Changed - AID limitations when using Gallagher key diversification (@DarkMatterMatt)
   - Added new standalone mode `lf_em4100rsww` (@zabszk)
+  - Fixed `hf 15 slixdisable` wrong pass id (@r1ddl3rz)
  
--## [Frostbit.4.14831] [2022-01-11]
++## [Frostbit.4.14831][2022-01-11]
   - Changed Wiegand format lookup - now case-insensitive (@iceman1001)
   - Added new standalone mode `hf_15SNIFF` - Same as `hf_14ASNIFF` standalone mode for RDV4 - flashmem (@startrk1995)
   - Added `hf gallagher` commands for read/writing DESFire cards (@DarkMatterMatt)
