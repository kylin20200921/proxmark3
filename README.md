commit 4f952124c5acf35cade9b97cb3440dc0db0a1e56
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Dec 25 13:54:56 2021 +0100

    text

diff --git a/CHANGELOG.md b/CHANGELOG.md
index db9860d1e..16e3d08cc 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,7 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Changed `hf 14a sim -t 3` - anticollision for DESFire simulation now uses different RATS (@mosci)
  - Fixed `hf mfdes` - works with dynamic apdu and encode/decode buffers (@merlokk)
  - Added luascript `hf_ntag_bruteforce.lua` - ntag password bruteforce with the option to do NFC Tools MD5 versions of passwords (@keldnorman)
  - Added option `--crc-ht` to `lf cmdread` to compute and add CRC-8/HITAG (@doegox)
