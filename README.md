commit fe75de74401f3bace40ea132bb546e2321a53aae
Author: iceman1001 <iceman@iuse.se>
Date:   Wed Oct 13 19:10:17 2021 +0200

    added a trace of reading a MIFARE UL-C with 3DES authentication, thanks @rad1game!

diff --git a/CHANGELOG.md b/CHANGELOG.md
index 641853d29..c30be73e3 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,7 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Added trace of reading a MIFARE UL-C with 3DES authentication (thanks @rad1game)
  - Added ATR fingerprinting (@iceman1001, @doegox)
  - Fix `hf mf dump/auto/csave` MFC JSON dumps - save real ATQA/SAK (@doegox)
  - Added option to load raw NDEF to `nfc decode` command (@doegox)
diff --git a/traces/README.md b/traces/README.md
index 93c741003..8bfda01d9 100644
--- a/traces/README.md
+++ b/traces/README.md
@@ -89,6 +89,7 @@
 |hf_14a_reader_4b_rats.trace              |Execution of `hf 14a reader` against a 4b UID card with RATS|
 |hf_14a_reader_7b_rats.trace              |Execution of `hf 14a reader` against a 7b UID card with RATS|
 |hf_14a_mfu.trace                         |Reading of a password-protected MFU|
+|hf_14a_mfuc.trace                        |Reading of a UL-C with 3DES authentication|
 |hf_14a_mfu-sim.trace                     |Trace seen from a Proxmark3 simulating a MFU|
 |hf_14b_reader.trace                      |Execution of `hf 14b reader` against a card|
 |hf_14b_cryptorf_select.trace             |Sniff of libnfc select / anticollision ofa cryptoRF tag|
diff --git a/traces/hf_14a_mfuc.trace b/traces/hf_14a_mfuc.trace
new file mode 100644
index 000000000..d9daebd95
Binary files /dev/null and b/traces/hf_14a_mfuc.trace differ
