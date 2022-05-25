commit 7dcaf59cdd2b504da85cbea0f998268f2bcd8a02
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jan 30 21:23:17 2022 +0100

    added a sniffed desfire trace

diff --git a/traces/README.md b/traces/README.md
index 2875c0886..49ce15d32 100644
--- a/traces/README.md
+++ b/traces/README.md
@@ -79,6 +79,7 @@
 |--------|-----------|
 |hf_sniff_14b_scl3711.pm3                 |`hf sniff 15000 2` <> `nfc-list -t 8`: PUPI: c12c8b1b AppData: 00000000 ProtInfo: 917171|
 
+
 # Demodulated acquisitions
 
 ## HF demodulated traces
@@ -99,4 +100,4 @@
 |hf_visa_apple_ecp.trace                  |Sniff of VISA Apple ECP transaction|
 |hf_visa_apple_normal.trace               |Sniff of VISA Apple normal transaction|
 |hf_visa_apple_transit_bypass.trace       |Sniff of VISA Apple transaction bypass|
-
+|hf_mfdes_sniff.trace                     |Sniff of HID reader reading a MIFARE DESFire SIO card|
