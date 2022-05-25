commit ad9f0223084940c6dbea1715be57383524cf7f9f
Author: iceman1001 <iceman@iuse.se>
Date:   Wed Oct 27 23:20:36 2021 +0200

    fix hf iclass dump

diff --git a/CHANGELOG.md b/CHANGELOG.md
index b68a2cc3c..0a67fcce5 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,7 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Fixed `hf iclass dump` - now uses the right key when suppling credit key (@iceman1001)
  - Changed crc16_legic to be supported in lua (@iceman1001)
  - Changed `trace list -t 14a` - now annotate magsafe wupa (@iceman1001)
  - Added magsafe polling (thanks to @tcprst !) (@iceman1001)
diff --git a/client/src/cmdhficlass.c b/client/src/cmdhficlass.c
index 697e5e60d..8c68833ab 100644
--- a/client/src/cmdhficlass.c
+++ b/client/src/cmdhficlass.c
@@ -1592,7 +1592,7 @@ static int CmdHFiClassDump(const char *Cmd) {
         if (credit_key_nr < ICLASS_KEYS_MAX) {
             auth = true;
             have_credit_key = true;
-            memcpy(key, iClass_Key_Table[credit_key_nr], 8);
+            memcpy(credit_key, iClass_Key_Table[credit_key_nr], 8);
             PrintAndLogEx(SUCCESS, "Using AA2 (credit) key[%d] " _GREEN_("%s"), credit_key_nr, sprint_hex(iClass_Key_Table[credit_key_nr], 8));
         } else {
             PrintAndLogEx(ERR, "Key number is invalid");
