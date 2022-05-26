commit dfcb83a15407617d73ba191f171bdbe0ef8c78d7
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Feb 24 19:36:26 2022 +0100

    make style

diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index 81de6b35e..0f7a2ccc3 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -1638,7 +1638,7 @@ static void getTagLabel(uint8_t uid0, uint8_t uid1) {
     }
 }
 
-static void get_compact_tlv(uint8_t* d, uint8_t n) {
+static void get_compact_tlv(uint8_t *d, uint8_t n) {
     d++;
     n--;
 
@@ -1646,7 +1646,7 @@ static void get_compact_tlv(uint8_t* d, uint8_t n) {
         uint8_t tag = NIBBLE_HIGH(d[0]);
         uint8_t len = NIBBLE_LOW(d[0]);
 
-        switch(tag) {
+        switch (tag) {
             case 1:
                 PrintAndLogEx(INFO, "    %1x%1x  " _YELLOW_("%s") "   Country code in (ISO 3166-1)", tag, len, sprint_hex_inrow(d + 1, len));
                 // iso3166 script in cmdlffdb.c is buggy,  Åland, Australia not showing.  getline issues
diff --git a/client/src/cmdhfcipurse.c b/client/src/cmdhfcipurse.c
index ba3f2d956..2ad4499bb 100644
--- a/client/src/cmdhfcipurse.c
+++ b/client/src/cmdhfcipurse.c
@@ -378,7 +378,7 @@ static int SelectCommandEx(bool selectDefaultFile, bool useAID, uint8_t *aid, si
         if (res != 0 || *sw != 0x9000) {
             if (verbose) {
                 PrintAndLogEx(ERR, "Cipurse select file 0x%04x  ( %s )",  _RED_("fail"));
-                 PrintAndLogEx(ERR, "Card returns 0x%04x", fileId, *sw);
+                PrintAndLogEx(ERR, "Card returns 0x%04x", fileId, *sw);
             }
             return PM3_ESOFT;
         }
@@ -1434,7 +1434,7 @@ static int CmdHFCipurseDeleteFile(const char *Cmd) {
             DropField();
             return PM3_ESOFT;
         }
-        PrintAndLogEx(INFO, "Delete application " _CYAN_("%s") " ( %s )",sprint_hex_inrow(aid, aidLen),  _GREEN_("ok"));
+        PrintAndLogEx(INFO, "Delete application " _CYAN_("%s") " ( %s )", sprint_hex_inrow(aid, aidLen),  _GREEN_("ok"));
     }
 
     if (needCommit) {
diff --git a/client/src/nfc/ndef.c b/client/src/nfc/ndef.c
index 32ffe0c79..c9a74ad75 100644
--- a/client/src/nfc/ndef.c
+++ b/client/src/nfc/ndef.c
@@ -515,7 +515,7 @@ static int ndefDecodePayloadSmartPoster(uint8_t *ndef, size_t ndeflen, bool prin
 
 
 typedef struct ndef_wifi_type_s {
-    const char* description;
+    const char *description;
     uint8_t bytes[2];
 } ndef_wifi_type_t;
 
@@ -527,9 +527,9 @@ static const ndef_wifi_type_t wifi_crypt_types[] = {
     {"AES/TKIP", {0x00, 0x0C}}
 };
 
-static const char* ndef_wifi_crypt_lookup(uint8_t *d) {
+static const char *ndef_wifi_crypt_lookup(uint8_t *d) {
     for (int i = 0; i < ARRAYLEN(wifi_crypt_types); ++i) {
-        if ( memcmp(d, wifi_crypt_types[i].bytes, 2) == 0) {
+        if (memcmp(d, wifi_crypt_types[i].bytes, 2) == 0) {
             return wifi_crypt_types[i].description;
         }
     }
@@ -546,9 +546,9 @@ static const ndef_wifi_type_t wifi_auth_types[] = {
     {"WPA/WPA2 PERSONAL", {0x00, 0x22}}
 };
 
-static const char* ndef_wifi_auth_lookup(uint8_t *d) {
+static const char *ndef_wifi_auth_lookup(uint8_t *d) {
     for (int i = 0; i < ARRAYLEN(wifi_auth_types); ++i) {
-        if ( memcmp(d, wifi_auth_types[i].bytes, 2) == 0) {
+        if (memcmp(d, wifi_auth_types[i].bytes, 2) == 0) {
             return wifi_auth_types[i].description;
         }
     }
@@ -568,16 +568,16 @@ static int ndefDecodeMime_wifi_wsc(NDEFHeader_t *ndef) {
     size_t pos = 0;
     while (n) {
 
-        if ( ndef->Payload[pos] != 0x10 ) {
+        if (ndef->Payload[pos] != 0x10) {
             n -= 1;
             pos -= 1;
             continue;
         }
 
-        // VERSION 
+        // VERSION
         if (memcmp(&ndef->Payload[pos], "\x10\x4A", 2) == 0) {
             uint8_t len = 1;
-            PrintAndLogEx(INFO, "Version......... %s", sprint_hex(&ndef->Payload[pos + 2], len) );
+            PrintAndLogEx(INFO, "Version......... %s", sprint_hex(&ndef->Payload[pos + 2], len));
             n -= 2;
             n -= len;
             pos += 2;
@@ -588,21 +588,21 @@ static int ndefDecodeMime_wifi_wsc(NDEFHeader_t *ndef) {
         if (memcmp(&ndef->Payload[pos], "\x10\x0E", 2) == 0) {
             //  10 0E 00 39
             uint8_t len = 2;
-            PrintAndLogEx(INFO, "Credential...... %s", sprint_hex(&ndef->Payload[pos + 2], len) );
+            PrintAndLogEx(INFO, "Credential...... %s", sprint_hex(&ndef->Payload[pos + 2], len));
             n -= 2;
             n -= len;
             pos += 2;
             pos += len;
         }
 
-        // AUTH_TYPE 
+        // AUTH_TYPE
         if (memcmp(&ndef->Payload[pos], "\x10\x03", 2) == 0) {
-            // 10 03 00 02 00 20 
+            // 10 03 00 02 00 20
             uint8_t len = 4;
             PrintAndLogEx(INFO, "Auth type....... %s ( " _YELLOW_("%s")" )",
-                sprint_hex(&ndef->Payload[pos + 2], len),
-                ndef_wifi_auth_lookup(&ndef->Payload[pos + 2])
-                );
+                          sprint_hex(&ndef->Payload[pos + 2], len),
+                          ndef_wifi_auth_lookup(&ndef->Payload[pos + 2])
+                         );
             n -= 2;
             n -= len;
             pos += 2;
@@ -614,9 +614,9 @@ static int ndefDecodeMime_wifi_wsc(NDEFHeader_t *ndef) {
             // 10 0F 00 02 00 04
             uint8_t len = 4;
             PrintAndLogEx(INFO, "Crypt type...... %s ( " _YELLOW_("%s")" )",
-                sprint_hex(&ndef->Payload[pos + 2], len),
-                ndef_wifi_crypt_lookup(&ndef->Payload[pos + 2])
-                );
+                          sprint_hex(&ndef->Payload[pos + 2], len),
+                          ndef_wifi_crypt_lookup(&ndef->Payload[pos + 2])
+                         );
             n -= 2;
             n -= len;
             pos += 2;
@@ -627,7 +627,7 @@ static int ndefDecodeMime_wifi_wsc(NDEFHeader_t *ndef) {
         if (memcmp(&ndef->Payload[pos], "\x10\x20", 2) == 0) {
             // 10 20 00 06 FF FF FF FF FF FF
             uint8_t len = ndef->Payload[pos + 3];
-            PrintAndLogEx(INFO, "MAC Address..... %s", sprint_hex_ascii(&ndef->Payload[pos + 4], len) );
+            PrintAndLogEx(INFO, "MAC Address..... %s", sprint_hex_ascii(&ndef->Payload[pos + 4], len));
             n -= 4;
             n -= len;
             pos += 4;
@@ -636,9 +636,9 @@ static int ndefDecodeMime_wifi_wsc(NDEFHeader_t *ndef) {
 
         // NETWORK_IDX
         if (memcmp(&ndef->Payload[pos], "\x10\x26", 2) == 0) {
-            // 10 26 00 01 01 
+            // 10 26 00 01 01
             uint8_t len = 3;
-            PrintAndLogEx(INFO, "Network Index... %s", sprint_hex(&ndef->Payload[pos + 2], len) );
+            PrintAndLogEx(INFO, "Network Index... %s", sprint_hex(&ndef->Payload[pos + 2], len));
             n -= 2;
             n -= len;
             pos += 2;
@@ -647,9 +647,9 @@ static int ndefDecodeMime_wifi_wsc(NDEFHeader_t *ndef) {
 
         // NETWORK_KEY
         if (memcmp(&ndef->Payload[pos], "\x10\x27", 2) == 0) {
-            // 10 27 00 10 74 72 69 73 74 61 6E 2D 73 70 72 69 6E 67 65 72 
+            // 10 27 00 10 74 72 69 73 74 61 6E 2D 73 70 72 69 6E 67 65 72
             uint8_t len = ndef->Payload[pos + 3];
-            PrintAndLogEx(INFO, "Network key..... %s", sprint_hex_ascii(&ndef->Payload[pos + 4], len) );
+            PrintAndLogEx(INFO, "Network key..... %s", sprint_hex_ascii(&ndef->Payload[pos + 4], len));
             n -= 4;
             n -= len;
             pos += 4;
@@ -657,9 +657,9 @@ static int ndefDecodeMime_wifi_wsc(NDEFHeader_t *ndef) {
         }
         // NETWORK_NAME
         if (memcmp(&ndef->Payload[pos], "\x10\x45", 2) == 0) {
-        // 10 45 00 06 69 63 65 73 71 6C
+            // 10 45 00 06 69 63 65 73 71 6C
             uint8_t len = ndef->Payload[pos + 3];
-            PrintAndLogEx(INFO, "Network Name.... %s", sprint_hex_ascii(&ndef->Payload[pos + 4], len) );
+            PrintAndLogEx(INFO, "Network Name.... %s", sprint_hex_ascii(&ndef->Payload[pos + 4], len));
             n -= 4;
             n -= len;
             pos += 4;
@@ -670,27 +670,27 @@ static int ndefDecodeMime_wifi_wsc(NDEFHeader_t *ndef) {
         // unknown the length.
         if (memcmp(&ndef->Payload[pos], "\x10\x2C", 2) == 0) {
             uint8_t len = 1;
-            PrintAndLogEx(INFO, "OOB Password......... %s", sprint_hex(&ndef->Payload[pos + 2], len) );
+            PrintAndLogEx(INFO, "OOB Password......... %s", sprint_hex(&ndef->Payload[pos + 2], len));
             n -= 2;
             n -= len;
             pos += 2;
             pos += len;
         }
         // VENDOR_EXT
-        // unknown the length.        
+        // unknown the length.
         if (memcmp(&ndef->Payload[pos], "\x10\x49", 2) == 0) {
             uint8_t len = 1;
-            PrintAndLogEx(INFO, "Vendor Ext......... %s", sprint_hex(&ndef->Payload[pos + 2], len) );
+            PrintAndLogEx(INFO, "Vendor Ext......... %s", sprint_hex(&ndef->Payload[pos + 2], len));
             n -= 2;
             n -= len;
             pos += 2;
             pos += len;
         }
         // VENDOR_WFA
-        // unknown the length.        
+        // unknown the length.
         if (memcmp(&ndef->Payload[pos], "\x10\x30\x2A", 3) == 0) {
             uint8_t len = 1;
-            PrintAndLogEx(INFO, "Vendor WFA......... %s", sprint_hex(&ndef->Payload[pos + 2], len) );
+            PrintAndLogEx(INFO, "Vendor WFA......... %s", sprint_hex(&ndef->Payload[pos + 2], len));
             n -= 2;
             n -= len;
             pos += 2;
@@ -699,14 +699,14 @@ static int ndefDecodeMime_wifi_wsc(NDEFHeader_t *ndef) {
     }
 
     /*
-        ap-channel   0,  6  
-+        credential
+        ap-channel   0,  6
+    +        credential
         device-name
         mac-address
         manufacturer
         model-name
         model-number
-+        oob-password
+    +        oob-password
         primary-device-type
         rf-bands
         secondary-device-type-list
@@ -714,7 +714,7 @@ static int ndefDecodeMime_wifi_wsc(NDEFHeader_t *ndef) {
         ssid
         uuid-enrollee
         uuid-registrar
-+        vendor-extension
+    +        vendor-extension
         version-1
      */
 
diff --git a/doc/commands.json b/doc/commands.json
index 829e4c93d..6fa197fd8 100644
--- a/doc/commands.json
+++ b/doc/commands.json
@@ -1899,7 +1899,7 @@
         },
         "hf cipurse help": {
             "command": "hf cipurse help",
-            "description": "help this help. test tests",
+            "description": "help this help. test regression tests",
             "notes": [],
             "offline": true,
             "options": [],
@@ -1965,11 +1965,15 @@
         },
         "hf cipurse test": {
             "command": "hf cipurse test",
-            "description": "[=] ------ cipurse tests ------ [=] kvv.............. passed [=] iso9797m2........ passed [=] smi.............. passed [=] mic.............. passed [=] auth............. passed [=] channel mac...... passed [=] channel encdec... passed [=] apdu............. passed [=] --------------------------- [+] tests [ ok ]",
-            "notes": [],
+            "description": "regression tests",
+            "notes": [
+                "hf cipurse test"
+            ],
             "offline": true,
-            "options": [],
-            "usage": ""
+            "options": [
+                "-h, --help this help"
+            ],
+            "usage": "hf cipurse test [-h]"
         },
         "hf cipurse updakey": {
             "command": "hf cipurse updakey",
@@ -2016,7 +2020,7 @@
                 "--mfd select masterfile by empty id",
                 "--newkeyn <dec> target key id",
                 "--newkey <hex 16 byte> new key",
-                "--newkeya <hex 1 byte> new key additional info. 0x00 by default",
+                "--newkeya <hex 1 byte> new key additional info (def: 0x00)",
                 "--enckeyn <dec> encrypt key id (must be equal to the key on the card)",
                 "--enckey <hex 16 byte> encrypt key (must be equal to the key on the card)",
                 "--sreq <plain|mac(default)|encode> communication reader-picc security level",
@@ -5220,7 +5224,7 @@
         },
         "hf mfdes help": {
             "command": "hf mfdes help",
-            "description": "help this help list list desfire (iso 14443a) history test test crypto",
+            "description": "help this help list list desfire (iso 14443a) history test regression crypto tests",
             "notes": [],
             "offline": true,
             "options": [],
@@ -5459,11 +5463,15 @@
         },
         "hf mfdes test": {
             "command": "hf mfdes test",
-            "description": "[=] ------ desfire tests ------ [!] no space for crc. pos: 1 [=] crc16............. passed [!] no space for crc. pos: 2 [=] crc32............. passed [=] cmac subkeys...... passed [=] an10922 aes....... passed [=] an10922 2tdea..... passed [=] an10922 3tdea..... passed [=] cmac 3tdea........ passed [=] cmac 2tdea........ passed [=] cmac des.......... passed [=] ev2 session keys.. passed [=] ev2 iv calc....... passed [=] ev2 mac calc...... passed [=] trans session key. passed [=] lrp plaintexts.... passed [=] lrp updated keys.. passed [=] lrp eval.......... passed [=] lrp inc counter... passed [=] lrp encode........ passed [=] lrp decode........ passed [=] lrp subkeys....... passed [=] lrp cmac.......... passed [=] lrp session keys.. passed [=] --------------------------- [+] tests [ ok ]",
-            "notes": [],
+            "description": "regression crypto tests",
+            "notes": [
+                "hf mfdes test"
+            ],
             "offline": true,
-            "options": [],
-            "usage": ""
+            "options": [
+                "-h, --help this help"
+            ],
+            "usage": "hf mfdes test [-h]"
         },
         "hf mfdes value": {
             "command": "hf mfdes value",
@@ -10902,6 +10910,6 @@
     "metadata": {
         "commands_extracted": 688,
         "extracted_by": "PM3Help2JSON v1.00",
-        "extracted_on": "2022-02-18T21:30:00"
+        "extracted_on": "2022-02-24T18:33:33"
     }
 }
\ No newline at end of file
diff --git a/doc/commands.md b/doc/commands.md
index 6437758bd..ff41afea3 100644
--- a/doc/commands.md
+++ b/doc/commands.md
@@ -255,7 +255,7 @@ Check column "offline" for their availability.
 |`hf cipurse updkey      `|N       |`Update key`
 |`hf cipurse updakey     `|N       |`Update key attributes`
 |`hf cipurse default     `|N       |`Set default key and file id for all the other commands`
-|`hf cipurse test        `|Y       |`Tests`
+|`hf cipurse test        `|Y       |`Regression tests`
 
 
 ### hf epa
@@ -577,7 +577,7 @@ Check column "offline" for their availability.
 |`hf mfdes write         `|N       |`Write data to standard/backup/record/value file`
 |`hf mfdes value         `|N       |`Operations with value file (get/credit/limited credit/debit/clear)`
 |`hf mfdes clearrecfile  `|N       |`Clear record File`
-|`hf mfdes test          `|Y       |`Test crypto`
+|`hf mfdes test          `|Y       |`Regression crypto tests`
 
 
 ### hf seos
