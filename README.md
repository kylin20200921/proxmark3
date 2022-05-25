commit 405931d5650dc11ca316188a209e1f91d0d3b22f
Author: iceman1001 <iceman@iuse.se>
Date:   Tue Sep 14 19:52:05 2021 +0200

    textual

diff --git a/tools/hitag2crack/README.md b/tools/hitag2crack/README.md
index 72fd6baa6..f58753cb5 100644
--- a/tools/hitag2crack/README.md
+++ b/tools/hitag2crack/README.md
@@ -17,6 +17,13 @@ Automotive Remote Keyless Entry Systems by Flavio Garcia, David Oswald,
 Timo Kasper and Pierre Pavlides.  The first three attacks come from the first
 paper and the fourth attack comes from the second paper.
 
+
+_note_
+There are no Proxmark3 client implemented commands for these attacks. Only seperate executables to be compiled and run on your own system.
+No guarantees of working binaries on all systems.  Some work on linux only. 
+There is no easy way to extract the needed data from a live system and use with these tools.
+You can use the `RFIdler` device but the Proxmark3 client needs some more love.  Feel free to contribute.
+
 Attack 1
 --------
 
@@ -122,14 +129,12 @@ response pair.  The attacker needs to obtain a valid tag and then use this to
 obtain a valid encrypted nonce and challenge response pair.  This can be
 achieved by using the Proxmark3 `lf hitag sniff` command, placing the coil on the RWD and
 presenting the valid tag.  The encrypted nonce and challenge response pairs
-can then be read out.  These values can then
-be used to attack the tag with `lf hitag ht2crack <nR> <aR>`.
+can then be read out.   
+
+_note_  the Proxmark3 hitag sniff command isn't good enough yet to collect the needed data.
 
 **TODO** example
-```
-pm3 --> lf hitag sniff
-pm3 --> lf hitag ht2crack <nR> <aR>
-```
+
 
 Usage details: Attack 2
 -----------------------
@@ -139,21 +144,12 @@ The table can be generated on a disk with >1.5TB of storage, although it takes
 some time (allow a couple of days, privilege SSD). This can be
 achieved by using the Proxmark3 `lf hitag sniff` command, placing the coil on the RWD and
 presenting the valid tag.  The encrypted nonce and challenge response pairs
-can then be read out.  These values can then
-be used to attack the tag with `lf hitag ht2keystream <nR> <aR>`.
+can then be read out.  
 
-**TODO** example
-```
-./ht2crack2buildtable
-pm3 --> lf hitag sniff
-pm3 --> lf hitag ht2keystream <nR> <aR>
-```
+_note_  the Proxmark3 hitag sniff command isn't good enough yet to collect the needed data.
 
-It creates a file `Hitag2_<UID>_<nR>_<aR>_keystream.txt`.
+**TODO** example
 
-```
-./ht2crack2search Hitag2_<UID>_<nR>_<aR>_keystream.txt <UID> <nR>
-```
 
 Usage details: Attack 3
 -----------------------
@@ -164,18 +160,10 @@ to; e.g. you could potentially use any HiTag2 tag as long as the RWD starts
 the crypto handshake with it.  It requires >=136 encrypted nonce and challenge
 response pairs for the same tag UID.
 
-**TODO** will be ht2 sim or sniff with actual tag ?
-
-```
-pm3 --> lf hitag sniff
-```
+_note_  the Proxmark3 hitag sniff command isn't good enough yet to collect the needed data.
 
-It creates a file `Hitag2_<UID>_<nR>_<aR>_collection.txt`.
-Stop once you got enough pairs.
+**TODO** will be ht2 sim or sniff with actual tag ?
 
-```
-./ht2crack3 <UID> Hitag2_<UID>_<nR>_<aR>_collection.txt
-```
 
 Usage details: Attack 4
 -----------------------
@@ -183,19 +171,9 @@ Usage details: Attack 4
 Attack 4 requires the same information as attack 3, but only 16-32 encrypted
 nonce and challenge response pairs are required.
 
-```
-pm3 --> lf hitag sniff
-```
+_note_  the Proxmark3 hitag sniff command isn't good enough yet to collect the needed data.
 
-It creates a file `Hitag2_<UID>_<nR>_<aR>_collection.txt`.
-Stop once you got enough pairs.
-
-```
-./ht2crack4 -u <UID> -n Hitag2_<UID>_<nR>_<aR>_collection.txt [-N <number of nonces to use>] [-t <table size>]
-```
-
-Start with -N 16 and -t 500000.  If the attack fails to find the key, double
-the table size and try again, repeating if it still fails.
+**TODO** example
 
 Usage details: Attack 5
 -----------------------
@@ -203,14 +181,8 @@ Usage details: Attack 5
 Attack 5 requires two encrypted nonce and challenge
 response value pairs (nR, aR) for the tag's UID.
 
-```
-pm3 --> lf hitag sniff
-```
-Stop once you got two pairs.
+**TODO** example
 
-```
-$ ./ht2crack5 <UID> <nR1> <aR1> <nR2> <aR2>
-```
 
 Usage details: Attack 5gpu/5opencl
 ----------------------------------
@@ -218,20 +190,7 @@ Usage details: Attack 5gpu/5opencl
 Attacks 5gpu and 5opencl require two encrypted nonce and challenge
 response value pairs (nR, aR) for the tag's UID.
 
-```
-pm3 --> lf hitag sniff
-```
-Stop once you got two pairs.
-
-```
-$ ./ht2crack5gpu <UID> <nR1> <aR1> <nR2> <aR2>
-```
-
-or
-
-```
-$ ./ht2crack5opencl <UID> <nR1> <aR1> <nR2> <aR2>
-```
+**TODO** example
 
 5opencl supports a number of additional parameters, see [crack5opencl/README.md](/tools/hitag2crack/crack5opencl/README.md) for details.
 
