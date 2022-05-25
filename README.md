commit 92d4cea9193bc19603625cda87c883d954ddabca
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Jan 3 22:13:26 2022 +0100

    text..  plus.. the deprecated code is way behind

diff --git a/CHANGELOG.md b/CHANGELOG.md
index 7066c0d73..0d7a3bff2 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,10 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Changed `trace list -h` - textual change (@iceman1001)
+ - Fixed `hf iclass config` - not get stuck when trying to make a keyroll config card (@iceman1001)
+ - Changed textual output for iclass (@iceman1001)
+ - Changed `hf iclass reader` to use NG (@iceman1001)
  - Changed various notes to have ToC and top links (@iceman1001)
  - Added option `lf gprox demod --raw` - now supports decoding of raw bytes (@iceman1001)
  - Added option `hf mf gview --pwd` - now supports user supplied password (@iceman1001)
diff --git a/tools/deprecated-hid-flasher/flasher/usb_cmd.h b/tools/deprecated-hid-flasher/flasher/usb_cmd.h
index 75136c1f4..90a76a1be 100644
--- a/tools/deprecated-hid-flasher/flasher/usb_cmd.h
+++ b/tools/deprecated-hid-flasher/flasher/usb_cmd.h
@@ -223,7 +223,7 @@ typedef struct {
 #define FLAG_NR_AR_ATTACK       0x20
 
 //Iclass reader flags
-#define FLAG_ICLASS_READER_ONLY_ONCE    0x01
+//#define FLAG_ICLASS_READER_ONLY_ONCE    0x01
 #define FLAG_ICLASS_READER_CC           0x02
 #define FLAG_ICLASS_READER_CSN          0x04
 #define FLAG_ICLASS_READER_CONF         0x08
