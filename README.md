commit 0330234aeabfea8a025832077560747968fb185c
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Oct 14 19:49:57 2021 +0200

    Added sniffed traces of Visa Apple ECP transactions.  Thanks to @a66at for them!

diff --git a/CHANGELOG.md b/CHANGELOG.md
index c30be73e3..8dd40247c 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,7 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Added trace of VISA Apple transactions (thanks @a66at )
  - Added trace of reading a MIFARE UL-C with 3DES authentication (thanks @rad1game)
  - Added ATR fingerprinting (@iceman1001, @doegox)
  - Fix `hf mf dump/auto/csave` MFC JSON dumps - save real ATQA/SAK (@doegox)
diff --git a/traces/README.md b/traces/README.md
index 8bfda01d9..2875c0886 100644
--- a/traces/README.md
+++ b/traces/README.md
@@ -96,4 +96,7 @@
 |hf_15_reader.trace                       |Execution of `hf 15 reader` against a card|
 |hf_mfp_mad_sl3.trace                     |`hf mfp mad`|
 |hf_mfp_read_sc0_sl3.trace                |`hf mfp rdsc --sn 0 -k ...`|
+|hf_visa_apple_ecp.trace                  |Sniff of VISA Apple ECP transaction|
+|hf_visa_apple_normal.trace               |Sniff of VISA Apple normal transaction|
+|hf_visa_apple_transit_bypass.trace       |Sniff of VISA Apple transaction bypass|
 
diff --git a/traces/hf_visa_apple_ecp.trace b/traces/hf_visa_apple_ecp.trace
new file mode 100644
index 000000000..97446b594
Binary files /dev/null and b/traces/hf_visa_apple_ecp.trace differ
diff --git a/traces/hf_visa_apple_normal.trace b/traces/hf_visa_apple_normal.trace
new file mode 100644
index 000000000..8ac4f3d9a
Binary files /dev/null and b/traces/hf_visa_apple_normal.trace differ
diff --git a/traces/hf_visa_apple_transit_bypass.trace b/traces/hf_visa_apple_transit_bypass.trace
new file mode 100644
index 000000000..6e2990864
Binary files /dev/null and b/traces/hf_visa_apple_transit_bypass.trace differ
