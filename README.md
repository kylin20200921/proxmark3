commit 04b86073b2546c442813e3741f231eefe70971d2
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Feb 21 15:56:49 2022 +0100

    ndef simple wifi record decoder

diff --git a/client/src/nfc/ndef.c b/client/src/nfc/ndef.c
index 014e6b688..32ffe0c79 100644
--- a/client/src/nfc/ndef.c
+++ b/client/src/nfc/ndef.c
@@ -32,11 +32,12 @@
 
 #define STRBOOL(p) ((p) ? "1" : "0")
 
-#define NDEF_WIFIAPPL   "application/vnd.wfa"
-#define NDEF_BLUEAPPL   "application/vnd.bluetooth"
-#define NDEF_JSONAPPL   "application/json"
-#define NDEF_VCARDTEXT  "text/vcard"
-#define NDEF_XVCARDTEXT "text/x-vcard"
+#define NDEF_WIFIAPPL_WSC "application/vnd.wfa.wsc"
+#define NDEF_WIFIAPPL_P2P "application/vnd.wfa.p2p"
+#define NDEF_BLUEAPPL     "application/vnd.bluetooth"
+#define NDEF_JSONAPPL     "application/json"
+#define NDEF_VCARDTEXT    "text/vcard"
+#define NDEF_XVCARDTEXT   "text/x-vcard"
 
 
 static const char *TypeNameFormat_s[] = {
@@ -512,11 +513,221 @@ static int ndefDecodePayloadSmartPoster(uint8_t *ndef, size_t ndeflen, bool prin
     return PM3_SUCCESS;
 }
 
-static int ndefDecodeMime_wifi(NDEFHeader_t *ndef) {
-    PrintAndLogEx(INFO, _CYAN_("WiFi details"));
-    if (ndef->PayloadLen > 1) {
-        PrintAndLogEx(INFO, ">>> decorder, to be implemented <<<");
+
+typedef struct ndef_wifi_type_s {
+    const char* description;
+    uint8_t bytes[2];
+} ndef_wifi_type_t;
+
+static const ndef_wifi_type_t wifi_crypt_types[] = {
+    {"NONE",  {0x00, 0x01}},
+    {"WEP", {0x00, 0x02}},
+    {"TKIP", {0x00, 0x04}},
+    {"AES", {0x00, 0x08}},
+    {"AES/TKIP", {0x00, 0x0C}}
+};
+
+static const char* ndef_wifi_crypt_lookup(uint8_t *d) {
+    for (int i = 0; i < ARRAYLEN(wifi_crypt_types); ++i) {
+        if ( memcmp(d, wifi_crypt_types[i].bytes, 2) == 0) {
+            return wifi_crypt_types[i].description;
+        }
+    }
+    return "";
+}
+
+static const ndef_wifi_type_t wifi_auth_types[] = {
+    {"OPEN", {0x00, 0x01}},
+    {"WPA PERSONAL", {0x00, 0x02}},
+    {"SHARED", {0x00, 0x04}},
+    {"WPA ENTERPRISE", {0x00, 0x08}},
+    {"WPA2 ENTERPRISE", {0x00, 0x10}},
+    {"WPA2 PERSONAL", {0x00, 0x20}},
+    {"WPA/WPA2 PERSONAL", {0x00, 0x22}}
+};
+
+static const char* ndef_wifi_auth_lookup(uint8_t *d) {
+    for (int i = 0; i < ARRAYLEN(wifi_auth_types); ++i) {
+        if ( memcmp(d, wifi_auth_types[i].bytes, 2) == 0) {
+            return wifi_auth_types[i].description;
+        }
     }
+    return "";
+}
+
+static int ndefDecodeMime_wifi_wsc(NDEFHeader_t *ndef) {
+    if (ndef->PayloadLen == 0) {
+        PrintAndLogEx(INFO, "no payload");
+        return PM3_SUCCESS;
+    }
+
+    PrintAndLogEx(INFO, _CYAN_("NDEF Wifi Simple Config Record"));
+    PrintAndLogEx(INFO, "Type............ " _YELLOW_("%.*s"), (int)ndef->TypeLen, ndef->Type);
+
+    size_t n = ndef->PayloadLen;
+    size_t pos = 0;
+    while (n) {
+
+        if ( ndef->Payload[pos] != 0x10 ) {
+            n -= 1;
+            pos -= 1;
+            continue;
+        }
+
+        // VERSION 
+        if (memcmp(&ndef->Payload[pos], "\x10\x4A", 2) == 0) {
+            uint8_t len = 1;
+            PrintAndLogEx(INFO, "Version......... %s", sprint_hex(&ndef->Payload[pos + 2], len) );
+            n -= 2;
+            n -= len;
+            pos += 2;
+            pos += len;
+        }
+
+        // CREDENTIAL
+        if (memcmp(&ndef->Payload[pos], "\x10\x0E", 2) == 0) {
+            //  10 0E 00 39
+            uint8_t len = 2;
+            PrintAndLogEx(INFO, "Credential...... %s", sprint_hex(&ndef->Payload[pos + 2], len) );
+            n -= 2;
+            n -= len;
+            pos += 2;
+            pos += len;
+        }
+
+        // AUTH_TYPE 
+        if (memcmp(&ndef->Payload[pos], "\x10\x03", 2) == 0) {
+            // 10 03 00 02 00 20 
+            uint8_t len = 4;
+            PrintAndLogEx(INFO, "Auth type....... %s ( " _YELLOW_("%s")" )",
+                sprint_hex(&ndef->Payload[pos + 2], len),
+                ndef_wifi_auth_lookup(&ndef->Payload[pos + 2])
+                );
+            n -= 2;
+            n -= len;
+            pos += 2;
+            pos += len;
+        }
+
+        // CRYPT_TYPE
+        if (memcmp(&ndef->Payload[pos], "\x10\x0F", 2) == 0) {
+            // 10 0F 00 02 00 04
+            uint8_t len = 4;
+            PrintAndLogEx(INFO, "Crypt type...... %s ( " _YELLOW_("%s")" )",
+                sprint_hex(&ndef->Payload[pos + 2], len),
+                ndef_wifi_crypt_lookup(&ndef->Payload[pos + 2])
+                );
+            n -= 2;
+            n -= len;
+            pos += 2;
+            pos += len;
+        }
+
+        // MAC_ADDRESS
+        if (memcmp(&ndef->Payload[pos], "\x10\x20", 2) == 0) {
+            // 10 20 00 06 FF FF FF FF FF FF
+            uint8_t len = ndef->Payload[pos + 3];
+            PrintAndLogEx(INFO, "MAC Address..... %s", sprint_hex_ascii(&ndef->Payload[pos + 4], len) );
+            n -= 4;
+            n -= len;
+            pos += 4;
+            pos += len;
+        }
+
+        // NETWORK_IDX
+        if (memcmp(&ndef->Payload[pos], "\x10\x26", 2) == 0) {
+            // 10 26 00 01 01 
+            uint8_t len = 3;
+            PrintAndLogEx(INFO, "Network Index... %s", sprint_hex(&ndef->Payload[pos + 2], len) );
+            n -= 2;
+            n -= len;
+            pos += 2;
+            pos += len;
+        }
+
+        // NETWORK_KEY
+        if (memcmp(&ndef->Payload[pos], "\x10\x27", 2) == 0) {
+            // 10 27 00 10 74 72 69 73 74 61 6E 2D 73 70 72 69 6E 67 65 72 
+            uint8_t len = ndef->Payload[pos + 3];
+            PrintAndLogEx(INFO, "Network key..... %s", sprint_hex_ascii(&ndef->Payload[pos + 4], len) );
+            n -= 4;
+            n -= len;
+            pos += 4;
+            pos += len;
+        }
+        // NETWORK_NAME
+        if (memcmp(&ndef->Payload[pos], "\x10\x45", 2) == 0) {
+        // 10 45 00 06 69 63 65 73 71 6C
+            uint8_t len = ndef->Payload[pos + 3];
+            PrintAndLogEx(INFO, "Network Name.... %s", sprint_hex_ascii(&ndef->Payload[pos + 4], len) );
+            n -= 4;
+            n -= len;
+            pos += 4;
+            pos += len;
+        }
+
+        // OOB_PASSWORD
+        // unknown the length.
+        if (memcmp(&ndef->Payload[pos], "\x10\x2C", 2) == 0) {
+            uint8_t len = 1;
+            PrintAndLogEx(INFO, "OOB Password......... %s", sprint_hex(&ndef->Payload[pos + 2], len) );
+            n -= 2;
+            n -= len;
+            pos += 2;
+            pos += len;
+        }
+        // VENDOR_EXT
+        // unknown the length.        
+        if (memcmp(&ndef->Payload[pos], "\x10\x49", 2) == 0) {
+            uint8_t len = 1;
+            PrintAndLogEx(INFO, "Vendor Ext......... %s", sprint_hex(&ndef->Payload[pos + 2], len) );
+            n -= 2;
+            n -= len;
+            pos += 2;
+            pos += len;
+        }
+        // VENDOR_WFA
+        // unknown the length.        
+        if (memcmp(&ndef->Payload[pos], "\x10\x30\x2A", 3) == 0) {
+            uint8_t len = 1;
+            PrintAndLogEx(INFO, "Vendor WFA......... %s", sprint_hex(&ndef->Payload[pos + 2], len) );
+            n -= 2;
+            n -= len;
+            pos += 2;
+            pos += len;
+        }
+    }
+
+    /*
+        ap-channel   0,  6  
++        credential
+        device-name
+        mac-address
+        manufacturer
+        model-name
+        model-number
++        oob-password
+        primary-device-type
+        rf-bands
+        secondary-device-type-list
+        serial-number
+        ssid
+        uuid-enrollee
+        uuid-registrar
++        vendor-extension
+        version-1
+     */
+
+    return PM3_SUCCESS;
+}
+static int ndefDecodeMime_wifi_p2p(NDEFHeader_t *ndef) {
+    if (ndef->PayloadLen == 0) {
+        PrintAndLogEx(INFO, "no payload");
+        return PM3_SUCCESS;
+    }
+
+    PrintAndLogEx(INFO, _CYAN_("NDEF Wifi Peer To Peer Record"));
+    PrintAndLogEx(INFO, "Type............ " _YELLOW_("%.*s"), (int)ndef->TypeLen, ndef->Type);
     return PM3_SUCCESS;
 }
 
@@ -554,55 +765,62 @@ static int ndefDecodeMime_vcard(NDEFHeader_t *ndef) {
     }
     return PM3_SUCCESS;
 }
+
 static int ndefDecodeMime_json(NDEFHeader_t *ndef) {
-    PrintAndLogEx(INFO, _CYAN_("JSON details"));
-    if (ndef->PayloadLen > 1) {
-        PrintAndLogEx(INFO, "");
-        PrintAndLogEx(INFO, _GREEN_("%.*s"), (int)ndef->PayloadLen, ndef->Payload);
+    if (ndef->PayloadLen == 0) {
+        PrintAndLogEx(INFO, "no payload");
+        return PM3_SUCCESS;
     }
+
+    PrintAndLogEx(INFO, _CYAN_("JSON details"));
+    PrintAndLogEx(INFO, "Type............ " _YELLOW_("%.*s"), (int)ndef->TypeLen, ndef->Type);
+    PrintAndLogEx(INFO, "");
+    PrintAndLogEx(INFO, _GREEN_("%.*s"), (int)ndef->PayloadLen, ndef->Payload);
     return PM3_SUCCESS;
 }
 
 static int ndefDecodeMime_bt(NDEFHeader_t *ndef) {
+    if (ndef->PayloadLen == 0) {
+        PrintAndLogEx(INFO, "no payload");
+        return PM3_SUCCESS;
+    }
     PrintAndLogEx(INFO, "Type............ " _YELLOW_("%.*s"), (int)ndef->TypeLen, ndef->Type);
-    if (ndef->PayloadLen > 1) {
-        uint16_t ooblen = (ndef->Payload[1] << 8 | ndef->Payload[0]);
-        PrintAndLogEx(INFO, "OOB data len.... %u", ooblen);
-        PrintAndLogEx(INFO, "BT MAC.......... " _YELLOW_("%s"), sprint_hex(ndef->Payload + 2, 6));
-        // Let's check payload[8]. Tells us a bit about the UUID's. If 0x07 then it tells us a service UUID is 128bit
-        switch (ndef->Payload[8]) {
-            case 0x02:
-                PrintAndLogEx(INFO, "Optional Data... incomplete list 16-bit UUID's");
-                break;
-            case 0x03:
-                PrintAndLogEx(INFO, "Optional Data... complete list 16-bit UUID's");
-                break;
-            case 0x04:
-                PrintAndLogEx(INFO, "Optional Data... incomplete list 32-bit UUID's");
-                break;
-            case 0x05:
-                PrintAndLogEx(INFO, "Optional Data... complete list 32-bit UUID's");
-                break;
-            case 0x06:
-                PrintAndLogEx(INFO, "Optional Data... incomplete list 128-bit UUID's");
-                break;
-            case 0x07:
-                PrintAndLogEx(INFO, "Optional Data... complete list 128-bit UUID's");
-                break;
-            default:
-                PrintAndLogEx(INFO, "Optional Data... [ %02x ]", ndef->Payload[8]);
-                break;
-        }
-        // Let's check payload[9]. If 0x08 then SHORT_NAME or if 0x09 then COMPLETE_NAME
-        if (ndef->Payload[9] == 0x08) {
-            PrintAndLogEx(INFO, "Short name...... " _YELLOW_("%.*s"), (int)(ndef->PayloadLen - 10), ndef->Payload + 10);
-        } else if (ndef->Payload[9] == 0x09) {
-            PrintAndLogEx(INFO, "Complete name... " _YELLOW_("%.*s"), (int)(ndef->PayloadLen - 10), ndef->Payload + 10);
-        } else {
-            PrintAndLogEx(INFO, "[ %02x ]", ndef->Payload[9]);
-        }
-        PrintAndLogEx(NORMAL, "");
+    uint16_t ooblen = (ndef->Payload[1] << 8 | ndef->Payload[0]);
+    PrintAndLogEx(INFO, "OOB data len.... %u", ooblen);
+    PrintAndLogEx(INFO, "BT MAC.......... " _YELLOW_("%s"), sprint_hex(ndef->Payload + 2, 6));
+    // Let's check payload[8]. Tells us a bit about the UUID's. If 0x07 then it tells us a service UUID is 128bit
+    switch (ndef->Payload[8]) {
+        case 0x02:
+            PrintAndLogEx(INFO, "Optional Data... incomplete list 16-bit UUID's");
+            break;
+        case 0x03:
+            PrintAndLogEx(INFO, "Optional Data... complete list 16-bit UUID's");
+            break;
+        case 0x04:
+            PrintAndLogEx(INFO, "Optional Data... incomplete list 32-bit UUID's");
+            break;
+        case 0x05:
+            PrintAndLogEx(INFO, "Optional Data... complete list 32-bit UUID's");
+            break;
+        case 0x06:
+            PrintAndLogEx(INFO, "Optional Data... incomplete list 128-bit UUID's");
+            break;
+        case 0x07:
+            PrintAndLogEx(INFO, "Optional Data... complete list 128-bit UUID's");
+            break;
+        default:
+            PrintAndLogEx(INFO, "Optional Data... [ %02x ]", ndef->Payload[8]);
+            break;
+    }
+    // Let's check payload[9]. If 0x08 then SHORT_NAME or if 0x09 then COMPLETE_NAME
+    if (ndef->Payload[9] == 0x08) {
+        PrintAndLogEx(INFO, "Short name...... " _YELLOW_("%.*s"), (int)(ndef->PayloadLen - 10), ndef->Payload + 10);
+    } else if (ndef->Payload[9] == 0x09) {
+        PrintAndLogEx(INFO, "Complete name... " _YELLOW_("%.*s"), (int)(ndef->PayloadLen - 10), ndef->Payload + 10);
+    } else {
+        PrintAndLogEx(INFO, "[ %02x ]", ndef->Payload[9]);
     }
+    PrintAndLogEx(NORMAL, "");
     return PM3_SUCCESS;
 }
 
@@ -687,8 +905,11 @@ static int ndefDecodePayload(NDEFHeader_t *ndef) {
             memcpy(begin, ndef->Type, ndef->TypeLen);
             str_lower(begin);
 
-            if (str_startswith(begin, NDEF_WIFIAPPL)) {
-                ndefDecodeMime_wifi(ndef);
+            if (str_startswith(begin, NDEF_WIFIAPPL_WSC)) {
+                ndefDecodeMime_wifi_wsc(ndef);
+            }
+            if (str_startswith(begin, NDEF_WIFIAPPL_P2P)) {
+                ndefDecodeMime_wifi_p2p(ndef);
             }
             if (str_startswith(begin, NDEF_VCARDTEXT) || str_startswith(begin, NDEF_XVCARDTEXT)) {
                 ndefDecodeMime_vcard(ndef);
