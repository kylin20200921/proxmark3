commit 0e7b61ed5542b0c95a73b2447581c0b136ebd37c
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Dec 31 10:48:53 2021 +0100

    text

diff --git a/doc/magic_cards_notes.md b/doc/magic_cards_notes.md
index c9a36b733..5aaee0260 100644
--- a/doc/magic_cards_notes.md
+++ b/doc/magic_cards_notes.md
@@ -45,6 +45,7 @@ Useful docs:
 # ISO14443A
 
 ## Identifying broken ISO14443A magic
+^[Top](#top)
 
 When a magic card configuration is really messed up and the card is not labeled, it may be hard to find out which type of card it is.
 
@@ -68,10 +69,12 @@ To restore anticollision config of the Proxmark3:
 hf 14a config --std
 ```
 # MIFARE Classic
+^[Top](#top)
 
 Referred as M1, S50 (1k), S70 (4k)
 
 ## MIFARE Classic block0
+^[Top](#top)
 
 UID 4b: (actually NUID as there are no more "unique" IDs on 4b)
 
@@ -101,8 +104,10 @@ UID 7b:
 ```
 
 ## MIFARE Classic Gen1A aka UID
+^[Top](#top)
 
 ### Identify
+^[Top](#top)
 
 ```
 hf 14a info
@@ -111,12 +116,14 @@ hf 14a info
 ```
 
 ### Magic commands
+^[Top](#top)
 
 * Wipe: `40(7)`, `41` (use 2000ms timeout)
 * Read: `40(7)`, `43`, `30xx`+crc
 * Write: `40(7)`, `43`, `A0xx`+crc, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`+crc
 
 ### Characteristics
+^[Top](#top)
 
 * UID: Only 4b versions
 * ATQA:
@@ -131,48 +138,56 @@ hf 14a info
   * no card with ATS
 
 #### MIFARE Classic Gen1A flavour 1
+^[Top](#top)
 
 * SAK: play blindly the block0 SAK byte, beware!
 * PRNG: static 01200145
 * Wipe: filled with 0xFF
 
 #### MIFARE Classic Gen1A flavour 2
+^[Top](#top)
 
 * SAK: play blindly the block0 SAK byte, beware!
 * PRNG: static 01200145
 * Wipe: filled with 0x00
 
 #### MIFARE Classic Gen1A flavour 3
+^[Top](#top)
 
 * SAK: 08
 * PRNG: static 01200145
 * Wipe: filled with 0xFF
 
 #### MIFARE Classic Gen1A flavour 4
+^[Top](#top)
 
 * SAK: 08
 * PRNG: weak
 * Wipe: timeout, no wipe
 
 #### MIFARE Classic Gen1A flavour 5
+^[Top](#top)
 
 * SAK: 08
 * PRNG: weak
 * Wipe: reply ok but no wipe performed
 
 #### MIFARE Classic Gen1A flavour 6
+^[Top](#top)
 
 * SAK: 08 or 88 if block0_SAK most significant bit is set
 * PRNG: weak
 * Wipe: timeout, no wipe
 
 #### MIFARE Classic Gen1A flavour 7
+^[Top](#top)
 
 * SAK: 08 or 88 if block0_SAK most significant bit is set
 * PRNG: weak
 * Wipe: filled with 0x00
 
 ### Proxmark3 commands
+^[Top](#top)
 
 ```
 hf mf csetuid
@@ -219,6 +234,7 @@ hf 14a raw -t 1000          41
 ```
 
 ### libnfc commands
+^[Top](#top)
 
 ```
 nfc-mfsetuid
@@ -227,10 +243,12 @@ nfc-mfclassic W a u mydump
 ```
 
 ## MIFARE Classic Gen1B
+^[Top](#top)
 
 Similar to Gen1A, but supports directly read/write after command 40
 
 ### Identify
+^[Top](#top)
 
 ```
 hf 14a info
@@ -239,15 +257,18 @@ hf 14a info
 ```
 
 ### Magic commands
+^[Top](#top)
 
 * Read: `40(7)`, `30xx`
 * Write: `40(7)`, `A0xx`+crc, `xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`+crc
 
 ## MIFARE Classic DirectWrite aka Gen2 aka CUID
+^[Top](#top)
 
 (also referred as MCT compatible by some sellers)
 
 ### Identify
+^[Top](#top)
 
 ```
 hf 14a info
@@ -260,12 +281,14 @@ Not all Gen2 cards can be identified with `hf 14a info`, only those replying to
 To identify the other ones, you've to try to write to block0 and see if it works...
 
 ### Magic commands
+^[Top](#top)
 
 Android compatible
 
 * issue regular write to block0
 
 ### Characteristics
+^[Top](#top)
 
 * UID: 4b and 7b versions
 * ATQA:
@@ -282,6 +305,7 @@ Android compatible
   * some reply with an ATS
 
 #### MIFARE Classic DirectWrite flavour 1
+^[Top](#top)
 
 * UID 4b
 * ATQA: play blindly the block0 ATQA bytes, beware!
@@ -291,6 +315,7 @@ Android compatible
 * PRNG: weak
 
 #### MIFARE Classic DirectWrite flavour 2
+^[Top](#top)
 
 * UID 4b
 * ATQA: fixed
@@ -300,6 +325,7 @@ Android compatible
 * PRNG: weak
 
 #### MIFARE Classic DirectWrite flavour 3
+^[Top](#top)
 
 * UID 4b
 * ATQA: play blindly the block0 ATQA bytes, beware!
@@ -309,6 +335,7 @@ Android compatible
 * PRNG: weak
 
 #### MIFARE Classic DirectWrite flavour 4
+^[Top](#top)
 
 * UID 7b
 * ATQA: fixed
@@ -318,6 +345,7 @@ Android compatible
 * PRNG: static 00000000
 
 #### MIFARE Classic DirectWrite flavour 5
+^[Top](#top)
 
 * UID 4b
 * ATQA: fixed
@@ -327,6 +355,7 @@ Android compatible
 * PRNG: weak
 
 #### MIFARE Classic DirectWrite flavour 6
+^[Top](#top)
 
 **TODO** need more info
 
@@ -334,6 +363,7 @@ Android compatible
 * ATS: 0D780071028849A13020150608563D
 
 ### Proxmark3 commands
+^[Top](#top)
 
 ```
 hf mf wrbl --blk 0 -k FFFFFFFFFFFF -d 11223344440804006263646566676869
@@ -367,12 +397,14 @@ hf 14a config --std
 hf 14a reader
 ```
 ## MIFARE Classic DirectWrite, FUID version aka 1-write
+^[Top](#top)
 
 Same as MIFARE Classic DirectWrite, but block0 can be written only once.
 
 Initial UID is AA55C396
 
 ### Identify
+^[Top](#top)
 
 Only possible before personalization.
 
@@ -383,14 +415,17 @@ hf 14a info
 ```
 
 ## MIFARE Classic DirectWrite, UFUID version
+^[Top](#top)
 
 Same as MIFARE Classic DirectWrite, but block0 can be locked with special command.
 
 ### Identify
+^[Top](#top)
 
 **TODO**
 
 ### Proxmark3 commands
+^[Top](#top)
 
 To lock definitively block0:
 ```
@@ -401,6 +436,7 @@ hf 14a raw       -c   85000000000000000000000000000008
 ```
 
 ## MIFARE Classic, other versions
+^[Top](#top)
 
 **TODO**
 
@@ -408,8 +444,10 @@ hf 14a raw       -c   85000000000000000000000000000008
 * Some cards exhibit a specific SAK=28 ??
 
 ## MIFARE Classic Gen3 aka APDU
+^[Top](#top)
 
 ### Identify
+^[Top](#top)
 
 ```
 hf 14a info
@@ -418,6 +456,7 @@ hf 14a info
 ```
 
 ### Magic commands
+^[Top](#top)
 
 Android compatible
 
@@ -442,6 +481,7 @@ Writing to block 0 has some side-effects:
 * On 4-byte UID cards, BCC byte is automatically corrected.
 
 ### Characteristics
+^[Top](#top)
 
 * UID: 4b and 7b versions
 * ATQA/SAK: fixed
@@ -449,6 +489,7 @@ Writing to block 0 has some side-effects:
 * ATS: none
 
 ### Proxmark3 commands
+^[Top](#top)
 
 ```
 # change just UID:
@@ -476,6 +517,7 @@ hf 14a raw -s -c 90FD111100
 ```
 
 ## MIFARE Classic Super
+^[Top](#top)
 
 It behaves like DirectWrite but records reader auth attempts.
 
@@ -487,6 +529,7 @@ To do reader-only attack: at least two versions exist.
 * type 2: https://github.com/netscylla/super-card/blob/master/libnfc-1.7.1/utils/nfc-super.c for ??
 
 ### Identify
+^[Top](#top)
 
 Only type 1 at the moment:
 
@@ -497,8 +540,10 @@ hf 14a info
 ```
 
 # MIFARE Ultralight
+^[Top](#top)
 
 ## MIFARE Ultralight blocks 0..2
+^[Top](#top)
 
 ```
 SN0  SN1  SN2  BCC0
@@ -518,6 +563,7 @@ Anticol shortcut (CL1/3000) is supported for UL, ULC, NTAG except NTAG I2C
 
 
 ## MIFARE Ultralight Gen1A
+^[Top](#top)
 
 ### Identify
 
@@ -538,6 +584,7 @@ Only 7b versions
 **TODO** need more tests
 
 ### Proxmark3 commands
+^[Top](#top)
 
 ```
 script run hf_mfu_setuid -h
@@ -551,8 +598,10 @@ script run hf_mf_magicrevive -u
 ```
 
 ## MIFARE Ultralight DirectWrite
+^[Top](#top)
 
 ### Identify
+^[Top](#top)
 
 ```
 hf 14a info
@@ -563,10 +612,12 @@ hf 14a info
 It seems so far that all MFUL DW have an ATS.
 
 ### Magic commands
+^[Top](#top)
 
 Issue three regular MFU write commands in a row to write first three blocks.
 
 ### Characteristics
+^[Top](#top)
 
 * UID: Only 7b versions
 * ATQA:
@@ -580,18 +631,21 @@ Issue three regular MFU write commands in a row to write first three blocks.
   * all cards reply with an ATS
 
 #### MIFARE Ultralight DirectWrite flavour 1
+^[Top](#top)
 
 * BCC: computed
 * ATS: 0A78008102DBA0C119402AB5
 * Anticol shortcut (CL1/3000): fails
 
 #### MIFARE Ultralight DirectWrite flavour 2
+^[Top](#top)
 
 * BCC: play blindly the block0 BCC0 and block2 BCC1 bytes, beware!
 * ATS: 850000A00A000AB00000000000000000184D
 * Anticol shortcut (CL1/3000): succeeds
 
 ### Proxmark3 commands
+^[Top](#top)
 
 ```
 hf mfu setuid -h
@@ -620,6 +674,7 @@ hf 14a reader
 ```
 
 ### libnfc commands
+^[Top](#top)
 
 ```
 nfc-mfultralight -h
@@ -627,14 +682,17 @@ nfc-mfultralight -h
 See `--uid` and `--full`
 
 ### Android
+^[Top](#top)
 
 * MIFARE++ Ultralight
 
 ## MIFARE Ultralight EV1 DirectWrite
+^[Top](#top)
 
 Similar to MFUL DirectWrite
 
 ### Identify
+^[Top](#top)
 
 ```
 hf 14a info
@@ -643,6 +701,7 @@ hf 14a info
 ```
 
 ### Characteristics
+^[Top](#top)
 
 * UID: Only 7b versions
 * ATQA:
@@ -655,24 +714,29 @@ hf 14a info
   * all cards reply with an ATS
 
 #### MIFARE Ultralight EV1 DirectWrite flavour 1
+^[Top](#top)
 
 * BCC: play blindly the block0 BCC0 and block2 BCC1 bytes, beware!
 * ATS: 850000A000000AC30004030101000B0341DF
 
 #### MIFARE Ultralight EV1 DirectWrite flavour 2
+^[Top](#top)
 
 * BCC: play blindly the block0 BCC0 and block2 BCC1 bytes, beware!
 * ATS: 850000A00A000AC30004030101000B0316D7
 
 ## MIFARE Ultralight C Gen1A
+^[Top](#top)
 
 Similar to MFUL Gen1A
 
 ## MIFARE Ultralight C DirectWrite
+^[Top](#top)
 
 Similar to MFUL DirectWrite
 
 ### Identify
+^[Top](#top)
 
 ```
 hf 14a info
@@ -681,6 +745,7 @@ hf 14a info
 ```
 
 ### Characteristics
+^[Top](#top)
 
 * UID: Only 7b versions
 * ATQA:
@@ -693,18 +758,22 @@ hf 14a info
   * all cards reply with an ATS
 
 #### MIFARE Ultralight C DirectWrite flavour 1
+^[Top](#top)
 
 * BCC: computed
 * ATS: 0A78008102DBA0C119402AB5
 * Anticol shortcut (CL1/3000): fails
 
 # NTAG
+^[Top](#top)
 
 ## NTAG213 DirectWrite
+^[Top](#top)
 
 Similar to MFUL DirectWrite
 
 ### Identify
+^[Top](#top)
 
 ```
 hf 14a info
@@ -713,6 +782,7 @@ hf 14a info
 ```
 
 ### Characteristics
+^[Top](#top)
 
 * UID: Only 7b versions
 * ATQA:
@@ -725,14 +795,17 @@ hf 14a info
   * all cards reply with an ATS
 
 #### NTAG213 DirectWrite flavour 1
+^[Top](#top)
 
 * BCC: play blindly the block0 BCC0 and block2 BCC1 bytes, beware!
 * ATS: 0A78008102DBA0C119402AB5
 * Anticol shortcut (CL1/3000): succeeds
 
 ## NTAG21x
+^[Top](#top)
 
 ### Identify
+^[Top](#top)
 
 ```
 hf 14a info
@@ -741,6 +814,7 @@ hf 14a info
 ```
 
 ### Characteristics
+^[Top](#top)
 
 Emulates fully NTAG213, 213F, 215, 216, 216F
 
@@ -749,12 +823,14 @@ Emulates partially  UL EV1 48k/128k, NTAG210, NTAG212, NTAGI2C 1K/2K, NTAGI2C 1K
 Anticol shortcut (CL1/3000): fails
 
 ### Proxmark3 commands
+^[Top](#top)
 
 ```
 script run hf_mfu_magicwrite -h
 ```
 
 # DESFire
+^[Top](#top)
 
 ## "DESFire" APDU, 7b UID
 
@@ -769,6 +845,7 @@ Android compatible
 * issue special APDUs
 
 ### Characteristics
+^[Top](#top)
 
 * ATQA: 0344
 * SAK: 20
@@ -777,6 +854,7 @@ Android compatible
 Only mimics DESFire anticollision (but wrong ATS), no further DESFire support
 
 ### Proxmark commands
+^[Top](#top)
 
 UID 04112233445566
 ```
@@ -788,6 +866,7 @@ hf 14a apdu -s 00ab00000704112233445566
 ```
 
 ### libnfc commands
+^[Top](#top)
 
 ```
 pn53x-tamashell
@@ -795,14 +874,17 @@ pn53x-tamashell
 420200ab00000704112233445566
 ```
 ## "DESFire" APDU, 4b UID
+^[Top](#top)
 
 ### Magic commands
+^[Top](#top)
 
 Android compatible
 
 * issue special APDUs
 
 ### Characteristics
+^[Top](#top)
 
 * ATQA: 0008 ??? This is not DESFire, 0008/20 doesn't match anything
 * SAK: 20
@@ -811,6 +893,7 @@ Android compatible
 Only mimics DESFire anticollision (but wrong ATS), no further DESFire support
 
 ### Proxmark commands
+^[Top](#top)
 
 UID 04112233445566
 ```
@@ -824,12 +907,14 @@ hf 14a apdu -s 00ab00000411223344
 It accepts longer UID but that doesn't affect BCC/ATQA/SAK
 
 ### pn53x-tamashell commands
+^[Top](#top)
 ```
 4a0100
 420200ab00000411223344
 ```
 
 ### Remarks
+^[Top](#top)
 
 The same effect (with better ATQA!) can be obtained with a MFC Gen1A that uses SAK defined in block0:
 
@@ -844,22 +929,27 @@ hf 14a info
 ```
 
 # ISO14443B
+^[Top](#top)
 
 ## ISO14443B magic
+^[Top](#top)
 
 No such card is available.
 
 Some vendor allow to specify an ID (PUPI) when ordering a card.
 
 # ISO15693
+^[Top](#top)
 
 ## ISO15693 magic
+^[Top](#top)
 
 ### Identify
 
 **TODO**
 
 ### Proxmark3 commands
+^[Top](#top)
 
 Always set a UID starting with `E0`.
 
@@ -874,8 +964,11 @@ script run hf_15_magic -u E004013344556677
 <a id="g4top"></a>
 
 # Multi
+^[Top](#top)
 
 ## Gen 4 GTU
+^[Top](#top)
+
 A.k.a ultimate magic card,  most promenent feature is shadow mode (GTU) and optional password protected backdoor commands.
 
 
