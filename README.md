commit f290a7c6300aa29ff66d9b13a682de7fef576649
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Dec 31 10:08:16 2021 +0100

    text

diff --git a/doc/clocks.md b/doc/clocks.md
index 4dfe72e82..fa7ba4f42 100644
--- a/doc/clocks.md
+++ b/doc/clocks.md
@@ -1,7 +1,10 @@
 # Notes on device side clocks
 The device side firmware uses a range of different clocks.  Here is an attempt to document the clocks in use and for what they are used.
 
+
+# Table of Contents
 - [Notes on device side clocks](#notes-on-device-side-clocks)
+- [Table of Contents](#table-of-contents)
   - [Slow clock](#slow-clock)
   - [Main Oscillator / MAINCK](#main-oscillator--mainck)
   - [PLL clock](#pll-clock)
diff --git a/doc/cloner_notes.md b/doc/cloner_notes.md
index 78f7b360e..44f7fa013 100644
--- a/doc/cloner_notes.md
+++ b/doc/cloner_notes.md
@@ -1,8 +1,14 @@
+<a id="Top"></a>
+
 # Notes on Cloner guns
 
 This document is based mostly on information posted on http://www.proxmark.org/forum/viewtopic.php?pid=39903#p39903
 
+
+# Table of Contents
+
 - [Notes on Cloner guns](#notes-on-cloner-guns)
+- [Table of Contents](#table-of-contents)
 - [Blue and black cloners](#blue-and-black-cloners)
 - [White cloner (pre 2015)](#white-cloner-pre-2015)
 - [White cloner (after 2016)](#white-cloner-after-2016)
@@ -12,6 +18,7 @@ This document is based mostly on information posted on http://www.proxmark.org/f
 
 
 # Blue and black cloners
+^[Top](#top)
 
 3 variants: 
 1. EM cloner
@@ -26,6 +33,7 @@ Standard password is normally:    51243648
 **Be sure to purchase the EM/HID version**
 
 # White cloner (pre 2015)
+^[Top](#top)
 
 Multifrequency
 Buttons light up BLUE
@@ -38,6 +46,8 @@ Standard password 13,56mHz:       individual per white cloner
 
 
 # White cloner (after 2016)
+^[Top](#top)
+
 Multifrequency
 Buttons light up  WHITE
 Data scrambled (variable per individual cloner, possibly due to prevent legal issues)
@@ -49,6 +59,8 @@ Standard password 13,56mHz:       individual per white cloner
 
 
 # White cloner (after 2016 D Quality)
+^[Top](#top)
+
 Multifrequency (it says so but it doesn't)
 Only works for EM/HID card (125kHz)
 High frequency not working
@@ -59,12 +71,16 @@ Standard password is normally (for T55xx):  AA55BBBB
 
 
 # Restore page1 data
+^[Top](#top)
+
 ```
 lf t55xx write -b 1 -d E0150A48 --pg1
 If t55xx write -b 2 -d 2D782308 --pg1
 ```
 
 # Sniffing the comms
+^[Top](#top)
+
 The T55x7 protocol uses a pwm based protocol for writing to tags.  In order to make decoding easier try the new command as seen below instead. It will try to extract the data written.
 
 ```
diff --git a/doc/magic_cards_notes.md b/doc/magic_cards_notes.md
index 07026c06e..40a6ed11f 100644
--- a/doc/magic_cards_notes.md
+++ b/doc/magic_cards_notes.md
@@ -1,3 +1,5 @@
+<a id="top"></a>
+
 # Notes on Magic Cards, aka UID changeable
 This document is based mostly on information posted on http://www.proxmark.org/forum/viewtopic.php?pid=35372#p35372
 
@@ -866,9 +868,13 @@ or (ignore errors):
 script run hf_15_magic -u E004013344556677  
 ```
 
+<a id="g4top"></a>
+
 # Multi
 
-## Ultimate Magic Card
+## Gen 4 GTU
+A.k.a ultimate magic card,  most promenent feature is shadow mode (GTU) and optional password protected backdoor commands.
+
 
 Can emulate MIFARE Classic, Ultralight/NTAG families, 14b UID & App Data
 
@@ -893,6 +899,7 @@ Can emulate MIFARE Classic, Ultralight/NTAG families, 14b UID & App Data
 
 
 ### Identify
+^[Top](#top) ^^[Gen4](#g4top)
 
 👉 **TODO** Tag doesn't get identified correctly by latest Proxmark3 client (it might get mislabeled as MFC Gen2/CUID, Gen3/APDU or NTAG21x Modifiable, depending on configured UID/ATQA/SAK/ATS)
 
@@ -902,6 +909,7 @@ hf 14a raw -s -c -t 1000 CF00000000C6
 ```
 If the card is an Ultimate Magic Card, it returns 30 bytes.
 ### Magic commands
+^[Top](#top) ^^[Gen4](#g4top)
 
 Special commands summary:
 
@@ -923,6 +931,7 @@ CF <passwd> FE <4b new_password>                 // change password
 Default `<passwd>`: `00000000`
 
 ### Characteristics
+^[Top](#top) ^^[Gen4](#g4top)
 
 * UID: 4b, 7b and 10b versions
 * ATQA/SAK: changeable
@@ -933,12 +942,13 @@ Default `<passwd>`: `00000000`
 * Backdoor password mode
 
 ### Proxmark3 commands
+^[Top](#top) ^^[Gen4](#g4top)
 
 ```
 # view contents of tag memory:
 hf mf gview
 ```
-👉 **TODO** `hf mf gview` is currently missing Ultralight memory maps and support for non-default password
+👉 **TODO** `hf mf gview` is currently missing Ultralight memory maps 
 
 Equivalent:
 
@@ -950,6 +960,8 @@ hf 14a raw -s -c -t 1000 CF00000000CE02
 ```
 
 ### Change ATQA / SAK
+^[Top](#top) ^^[Gen4](#g4top)
+
 ```
 hf 14a raw -s -c -t 1000 CF<passwd>35<2b ATQA><1b SAK>
 ```
@@ -962,6 +974,7 @@ Example: ATQA 0044 SAK 28, default pwd
 hf 14a raw -s -c -t 1000 CF0000000035440028
 ```
 ### Change ATS
+^[Top](#top) ^^[Gen4](#g4top)
 
 ```
 hf 14a raw -s -c -t 1000 CF<passwd>34<1b length><0-16b ATS>
@@ -977,6 +990,7 @@ hf 14a raw -s -c -t 1000 CF000000003406067577810280
 ```
 
 ### Set UID length (4, 7, 10)
+^[Top](#top) ^^[Gen4](#g4top)
 
 ```
 hf 14a raw -s -c -t 1000 CF<passwd>68<1b param>
@@ -991,6 +1005,7 @@ Example: set UID length to 7 bytes, default pwd
 hf 14a raw -s -c -t 1000 CF000000006801
 ```
 ### Set 14443A UID
+^[Top](#top) ^^[Gen4](#g4top)
 
 UID is configured according to block0 with a backdoor write.
 
@@ -1021,6 +1036,7 @@ Ultralight mode, 10b UID
 👉 the UID is composed only from block0
 
 ### Set 14443B UID and ATQB
+^[Top](#top) ^^[Gen4](#g4top)
 
 UID and ATQB are configured according to block0 with a (14a) backdoor write.
 
@@ -1035,6 +1051,7 @@ hf 14b reader
 => ATQB 0405060708090A
 
 ### (De)Activate Ultralight mode
+^[Top](#top) ^^[Gen4](#g4top)
 
 ```
 hf 14a raw -s -c -t 1000 CF<passwd>69<1b param>
@@ -1053,6 +1070,7 @@ In this mode, if SAK=`00` and ATQA=`0044`, it acts as an Ultralight card
 ⚠ only the first four bytes of each block will be mapped in the Ultralight memory map (so the Ultralight block numbers follow backdoor R/W block numbers).
 
 ### Select Ultralight mode
+^[Top](#top) ^^[Gen4](#g4top)
 
 ```
 hf 14a raw -s -c -t 1000 CF<passwd>6A<1b param>
@@ -1072,6 +1090,7 @@ hf 14a raw -s -c -t 1000 CF000000006A02
 ```
 Now the card supports the 3DES UL-C authentication.
 ### Set shadow mode (GTU)
+^[Top](#top) ^^[Gen4](#g4top)
 
 This mode is divided into four states: off (pre-write), on (on restore), don’t care, and high-speed read and write.
 If you use it, please enter the pre-write mode first. At this time, write the full card data.
@@ -1087,6 +1106,7 @@ hf 14a raw -s -c -t 1000 CF<passwd>32<1b param>
    * `03`: disabled, high speed R/W mode for Ultralight?
 
 ### Direct block read and write
+^[Top](#top) ^^[Gen4](#g4top)
 
 Using the backdoor command, one can read and write any area without MFC password, similarly to MFC Gen1 card. It should be noted that this command must be used to modify UID.
 
@@ -1113,6 +1133,7 @@ hf 14a raw -s -c -t 1000 CF00000000CD00112233441C000011778185BA18000000
 ```
 
 ### Change backdoor password
+^[Top](#top) ^^[Gen4](#g4top)
 
 All backdoor operations are protected by a password. If password is forgotten, the card can't be recovered. Default password is `00000000`.
 
@@ -1130,6 +1151,7 @@ hf 14a raw -s -c -t 1000 CFAABBCCDDFE00000000
 ```
 
 ### Dump configuration
+^[Top](#top) ^^[Gen4](#g4top)
 
 ```
 hf 14a raw -s -c -t 1000 CF<passwd>C6
@@ -1147,6 +1169,7 @@ Default configuration:
 ^^ cf cmd 69: Ultralight protocol
 ```
 ### Fast configuration
+^[Top](#top) ^^[Gen4](#g4top)
 
 ```
 hf 14a raw -s -c -t 1000 CF<passwd>F0<30b configuration data>
@@ -1161,6 +1184,7 @@ hf 14a raw -s -c -t 1000 CF00000000F000000000000002000978009102DABC1910101112131
 ⚠ Variant with command `F1` instead of `F0` will set and fuse permanently the configuration. Backdoor R/W will still work.
 
 ### Presets
+^[Top](#top) ^^[Gen4](#g4top)
 
 Here are some presets available in the FuseTool (but with all ATS disabled)
 
@@ -1215,6 +1239,7 @@ hf 14a raw -s -c -t 1000 CF00000000F001010000000003000978009102DABC1910101112131
 ```
 
 ### Version and Signature
+^[Top](#top) ^^[Gen4](#g4top)
 
 Ultralight EV1 and NTAG Version info and Signature are stored respectively in blocks 250-251 and 242-249.
 
