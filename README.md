commit 710c612a2c1099ff467e71e5bc6071374fdb74b5
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Sep 3 19:14:04 2021 +0200

    text

diff --git a/CHANGELOG.md b/CHANGELOG.md
index a6df54f8e..88acfff2e 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,7 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Add MIFARE Plus SL3 mode traces (@iceman1001)
  - Fix PAC/Stanley checksum calculation (@rknoll)
  - Added option --mem to `hf mf nested`, from icopyx source (@doegox)
  - Port fpga-xc3s100e and icopyx source code specificities to this repo (@doegox)
diff --git a/traces/README.md b/traces/README.md
index 6cbfb6b0d..93c741003 100644
--- a/traces/README.md
+++ b/traces/README.md
@@ -93,3 +93,6 @@
 |hf_14b_reader.trace                      |Execution of `hf 14b reader` against a card|
 |hf_14b_cryptorf_select.trace             |Sniff of libnfc select / anticollision ofa cryptoRF tag|
 |hf_15_reader.trace                       |Execution of `hf 15 reader` against a card|
+|hf_mfp_mad_sl3.trace                     |`hf mfp mad`|
+|hf_mfp_read_sc0_sl3.trace                |`hf mfp rdsc --sn 0 -k ...`|
+
