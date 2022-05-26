commit 7310834b69197efdaa9302aa83a510fafb295e04
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Feb 19 00:15:34 2022 +0100

    added a compact tlv decoder for ATR historical bytes in 14a info

diff --git a/CHANGELOG.md b/CHANGELOG.md
index b4ec94ce5..7279766f6 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,16 +3,17 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
- - Added `hf mf value`  - decode a value block (@iceman1001)
- - Changed `hf mf nested`: removed option `--single` redundant with usage of `--tblk` (@doegox)
- - Fixed `hf mf chk` single block mode (@doegox)
- - Fixed `hf mf fchk/chk` internal logic to load keys (@doegox)
- - Changed `hf mf *` printKeyTable: now display sector trailer info too (@doegox)
- - Changed `hf mf chk` option `--blk` into `--tblk` (as for nested) (@doegox)
+ - Changed `hf 14a info` - added a ATR historical compact TLV decoder (@iceman1001)
+ - Added `hf mf value` - decode a value block (@iceman1001)
+ - Changed `hf mf nested` - removed option `--single` redundant with usage of `--tblk` (@doegox)
+ - Fixed `hf mf chk` - single block mode (@doegox)
+ - Fixed `hf mf fchk/chk` - internal logic to load keys (@doegox)
+ - Changed `hf mf *` - printKeyTable: now display sector trailer info too (@doegox)
+ - Changed `hf mf chk` - option `--blk` into `--tblk` (as for nested) (@doegox)
  - Added new tool `mfd_multi_brute` - MIFARE DESfire / UL-C key recovery (@iceman1001)
- - Fixed `hf emrtd info` segfault on some platforms (@doegox)
- - Fixed `hf emrtd info` when offline (@doegox)
- - Fixed `commands.json` generation (@doegox)
+ - Fixed `hf emrtd info` - segfault on some platforms (@doegox)
+ - Fixed `hf emrtd info` - when offline (@doegox)
+ - Fixed `commands.json` - generation (@doegox)
  - Added new standalone mode `hf_legicsim` (@uhei)
  - Changed `hf legic *` - now uses NG instead (@iceman1001)
  - Added `hf legic view` - view contents of LEGIC Prime dump files (@iceman1001)
diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index ef2c3a03d..81de6b35e 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -1638,6 +1638,78 @@ static void getTagLabel(uint8_t uid0, uint8_t uid1) {
     }
 }
 
+static void get_compact_tlv(uint8_t* d, uint8_t n) {
+    d++;
+    n--;
+
+    while (n > 0) {
+        uint8_t tag = NIBBLE_HIGH(d[0]);
+        uint8_t len = NIBBLE_LOW(d[0]);
+
+        switch(tag) {
+            case 1:
+                PrintAndLogEx(INFO, "    %1x%1x  " _YELLOW_("%s") "   Country code in (ISO 3166-1)", tag, len, sprint_hex_inrow(d + 1, len));
+                // iso3166 script in cmdlffdb.c is buggy,  Åland, Australia not showing.  getline issues
+                break;
+            case 2:
+                PrintAndLogEx(INFO, "    %1x%1x  " _YELLOW_("%s") "   Issuer identification number (ISO 7812-1)", tag, len, sprint_hex_inrow(d + 1, len));
+                break;
+            case 3:
+                PrintAndLogEx(INFO, "    %1x%1x  " _YELLOW_("%s") "   Card service data byte", tag, len, sprint_hex_inrow(d + 1, len));
+                PrintAndLogEx(INFO, "    %c.......    Application selection: by full DF name", (d[1] & 0x80) ? '1' : '0');
+                PrintAndLogEx(INFO, "    .%c......    Application selection: by partial DF name", (d[1] & 0x40) ? '1' : '0');
+                PrintAndLogEx(INFO, "    ..%c.....    BER-TLV data objects available in EF.DIR", (d[1] & 0x20) ? '1' : '0');
+                PrintAndLogEx(INFO, "    ...%c....    BER-TLV data objects available in EF.ATR", (d[1] & 0x10) ? '1' : '0');
+                PrintAndLogEx(INFO, "    ....%c...    EF.DIR and EF.ATR access services: by READ BINARY command", (d[1] & 0x08) ? '1' : '0');
+                PrintAndLogEx(INFO, "    .....%c..    EF.DIR and EF.ATR access services: by GET DATA command", (d[1] & 0x04) ? '1' : '0');
+                PrintAndLogEx(INFO, "    ......%c.    EF.DIR and EF.ATR access services: by GET RECORD(s) command", (d[1] & 0x02) ? '1' : '0');
+                PrintAndLogEx(INFO, "    .......%c    EF.DIR and EF.ATR access services: RFU", (d[1] & 0x01) ? '1' : '0');
+                break;
+            case 4:
+                PrintAndLogEx(INFO, "    %1x%1x  " _YELLOW_("%s") "   Initial access data", tag, len, sprint_hex_inrow(d + 1, len));
+                break;
+            case 5:
+                PrintAndLogEx(INFO, "    %1x%1x  " _YELLOW_("%s") "   Card issuer data", tag, len, sprint_hex_inrow(d + 1, len));
+                break;
+            case 6:
+                PrintAndLogEx(INFO, "    %1x%1x  " _YELLOW_("%s") "   Pre-issuing data", tag, len, sprint_hex_inrow(d + 1, len));
+                break;
+            case 7:
+                PrintAndLogEx(INFO, "    %1x%1x " _YELLOW_("%s") "   Card capabilities", tag, len, sprint_hex_inrow(d + 1, len));
+
+                PrintAndLogEx(INFO, "    " _YELLOW_("%02X") " - Selection methods", d[1]);
+                PrintAndLogEx(INFO, "    %c.......    DF selection by full DF name", (d[1] & 0x80) ? '1' : '0');
+                PrintAndLogEx(INFO, "    .%c......    DF selection by partial DF name", (d[1] & 0x40) ? '1' : '0');
+                PrintAndLogEx(INFO, "    ..%c.....    DF selection by path", (d[1] & 0x20) ? '1' : '0');
+                PrintAndLogEx(INFO, "    ...%c....    DF selection by file identifier", (d[1] & 0x10) ? '1' : '0');
+                PrintAndLogEx(INFO, "    ....%c...    Implicit DF selection", (d[1] & 0x08) ? '1' : '0');
+                PrintAndLogEx(INFO, "    .....%c..    Short EF identifier supported", (d[1] & 0x04) ? '1' : '0');
+                PrintAndLogEx(INFO, "    ......%c.    Record number supported", (d[1] & 0x02) ? '1' : '0');
+                PrintAndLogEx(INFO, "    .......%c    Record identifier supported", (d[1] & 0x01) ? '1' : '0');
+
+                if (len > 1) {
+                    PrintAndLogEx(INFO, "    " _YELLOW_("%02X") " - Data coding byte", d[2]);
+                }
+                if (len > 2) {
+                    PrintAndLogEx(INFO, "    " _YELLOW_("%02X") " - Command chaining, length fields and logical channels", d[3]);
+                }
+                break;
+            case 8:
+                PrintAndLogEx(INFO, "    %1x%1x ... " _YELLOW_("%s") "   Status indicator", tag, len, sprint_hex_inrow(d + 1, len));
+                break;
+            case 0xE:
+                PrintAndLogEx(INFO, "    %1x%1x ... " _YELLOW_("%s") "   Application identifier", tag, len, sprint_hex_inrow(d + 1, len));
+                break;
+        }
+
+        if (len > n)
+            break;
+
+        n -= (1 + len);
+        d += (1 + len);
+    }
+}
+
 int infoHF14A(bool verbose, bool do_nack_test, bool do_aid_search) {
     clearCommandBuffer();
     SendCommandMIX(CMD_HF_ISO14443A_READER, ISO14A_CONNECT | ISO14A_NO_DISCONNECT, 0, 0, NULL, 0);
@@ -2034,10 +2106,12 @@ int infoHF14A(bool verbose, bool do_nack_test, bool do_aid_search) {
                 }
             } else {
 
-                if (card.ats[pos] == 0x80)
-                    PrintAndLogEx(SUCCESS, "   %s  (compact TLV data object)", sprint_hex_inrow(card.ats + pos, calen));
-                else
-                    PrintAndLogEx(SUCCESS, "   %s", sprint_hex_inrow(card.ats + pos, calen));
+                if (card.ats[pos] == 0x80 || card.ats[pos] == 0x00) {
+                    PrintAndLogEx(SUCCESS, "  %s  (compact TLV data object)", sprint_hex_inrow(&card.ats[pos], calen));
+                    get_compact_tlv(card.ats + pos, calen);
+                } else {
+                    PrintAndLogEx(SUCCESS, "  %s", sprint_hex_inrow(card.ats + pos, calen));
+                }
 
                 PrintAndLogEx(NORMAL, "");
             }
diff --git a/include/common.h b/include/common.h
index 314b67b57..2e245da43 100644
--- a/include/common.h
+++ b/include/common.h
@@ -158,11 +158,11 @@ extern bool g_tearoff_enabled;
 
 // Nibble logic
 #ifndef NIBBLE_HIGH
-# define NIBBLE_HIGH(b) ( (b & 0xF0) >> 4 )
+# define NIBBLE_HIGH(b) ( ((b) & 0xF0) >> 4 )
 #endif
 
 #ifndef NIBBLE_LOW
-# define NIBBLE_LOW(b)  ( b & 0x0F )
+# define NIBBLE_LOW(b)  ((b) & 0x0F )
 #endif
 
 #ifndef CRUMB
