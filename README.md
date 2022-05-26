commit 9da96a92d3ebdba816b3874111b89795ae8beadf
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Feb 7 14:24:45 2022 +0100

    rearrange

diff --git a/CHANGELOG.md b/CHANGELOG.md
index a42397308..9f92d755e 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,9 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Changed `hf_mf_uidbruteforce` - added support for S70, enhance UID length management (@cactuschibre)
+ - Fixed build issues that may happen from building `mfd_aes_brute` (@linuxgemini)
+ - Added silicon data parsing logic for NXP chips in `hf mfu info` (@linuxgemini)
  - Addes luascript `hf_mf_em_util.lua` - Script for emulator configuration (@nisgola)
  - Fixes `hf mf restore` - now takes bin/eml/json as dump files (@iceman1001)
  - Fixes `script run some_python_script` segfault on armhf architecture (@doegox)
@@ -15,9 +18,6 @@ This project uses the changelog in accordance with [keepchangelog](http://keepac
  - Added support for PCSC's proprietary HID 37bit format P10004 (@bthedorff)
  - Added `nfc decode` - now NDEF vCard messages with a PHOTO in base64 format is shown (@iceman1001)
  - Changed - AID limitations when using Gallagher key diversification (@DarkMatterMatt)
- - Fixed build issues that may happen from building `mfd_aes_brute` (@linuxgemini)
- - Added silicon data parsing logic for NXP chips in `hf mfu info` (@linuxgemini)
- - Added Mifare Classic 4K, enhance UID length management and fix help in `hf_mf_uidbruteforce` (@cactuschibre)
 
 ## [Frostbit.4.14831] [2022-01-11]
  - Changed Wiegand format lookup - now case-insensitive (@iceman1001)
