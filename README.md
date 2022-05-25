commit 57f9a009060aecaaa93a39bf7c760a12602122f5
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jun 19 22:15:35 2021 +0200

    ndef parser - now handles more ndef types

diff --git a/CHANGELOG.md b/CHANGELOG.md
index 158adc6ef..9d325f9d9 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,11 +3,12 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
- - FIXED 'hf desfire' changekey, GetUID, 3DES sesson key tweak. (@mwalker33)
- - Fixed `hf fido` commands now works correctly (@merlokk) 
+ - Changed `hf nfc ndefread` - ndef parser now handles more types (@iceman1001)
+ - Fix `hf desfire` changekey, GetUID, 3DES sesson key tweak. (@mwalker33)
+ - Fix `hf fido` commands now works correctly (@merlokk) 
  - Moved / renamed  `client/resource/fido2_defparams.json` ->  `client/resource/hf_fido2_defparams.json` (@merlokk)
  - Added `hf cipurse` commands to work with cipurse transport cards (@merlokk) 
- - Added '--gap' option to lf em 410x sim for more control over sim data (@mwalker)
+ - Added `--gap` option to lf em 410x sim for more control over sim data (@mwalker)
  - Changed `hf fido` - refactored load/save json objects (@iceman1001)
  - Moved / renamed  `fido2.json` ->  `client/resource/fido2_defparams.json` (@iceman1001)
  - Added openocd shikra support based on @ninjastyle82 patch to deprecated iceman fork (@iceman1001)
@@ -17,8 +18,8 @@ This project uses the changelog in accordance with [keepchangelog](http://keepac
  - Added `data asn1` - a command to decode ASN1 byte arrays  (@iceman1001)
  - Added `hf 15 disableprivacy` - from @g3gg0 fork *WIP* (@iceman1001)
  - Added `lf_ident_json.lua` - script to identify t55xx json dump files (@iceman1001)
- - Fixed `hf iclass chk` -  multithread concurrency issues solved (@iceman1001)
- - Fixed hf_iceclass standalone - correct null terminator filename (@metalauricle)
+ - Fix `hf iclass chk` -  multithread concurrency issues solved (@iceman1001)
+ - Fix hf_iceclass standalone - correct null terminator filename (@metalauricle)
  - Changed `trace list -t mfdes - added annotations for EV2, EV3 (@iceman1001)`
  - Changed `hf iclass lookup` - fixed swapped args (@iceman1001)
  - Changed `hf iclass decrypt` - added the possibility to decode the data as block6 if you have a cardhelper (@iceman1001)
diff --git a/client/src/nfc/ndef.c b/client/src/nfc/ndef.c
index bd1109e80..2b2dfc9c3 100644
--- a/client/src/nfc/ndef.c
+++ b/client/src/nfc/ndef.c
@@ -15,9 +15,16 @@
 #include "ui.h"
 #include "util.h" // sprint_hex...
 #include "crypto/asn1utils.h"
+#include "crypto/libpcrypto.h"
+#include "ecp.h"
+#include "commonutil.h"  // ARRAYLEN
 #include "pm3_cmd.h"
 
-#define STRBOOL(p) ((p) ? "+" : "-")
+#define STRBOOL(p) ((p) ? "1" : "0")
+
+#define NDEF_WIFIAPPL   "application/vnd.wfa"
+#define NDEF_BLUEAPPL   "application/vnd.bluetooth"
+#define NDEF_VCARDTEXT  "text/vcard"
 
 static const char *TypeNameFormat_s[] = {
     "Empty Record",
@@ -91,6 +98,9 @@ static const char *URI_s[] = {
     "urn:nfc:"                    // 0x23
 };
 
+static int ndefRecordDecodeAndPrint(uint8_t *ndefRecord, size_t ndefRecordLen);
+static int ndefDecodePayload(NDEFHeader_t *ndef);
+
 static uint16_t ndefTLVGetLength(uint8_t *data, size_t *indx) {
     uint16_t len = 0;
     if (data[0] == 0xff) {
@@ -142,20 +152,123 @@ static int ndefDecodeHeader(uint8_t *data, size_t datalen, NDEFHeader_t *header)
 }
 
 static int ndefPrintHeader(NDEFHeader_t *header) {
-    PrintAndLogEx(INFO, "Header:");
-
-    PrintAndLogEx(SUCCESS, "\tMessage Begin:    %s", STRBOOL(header->MessageBegin));
-    PrintAndLogEx(SUCCESS, "\tMessage End:      %s", STRBOOL(header->MessageEnd));
-    PrintAndLogEx(SUCCESS, "\tChunk Flag:       %s", STRBOOL(header->ChunkFlag));
-    PrintAndLogEx(SUCCESS, "\tShort Record Bit: %s", STRBOOL(header->ShortRecordBit));
-    PrintAndLogEx(SUCCESS, "\tID Len Present:   %s", STRBOOL(header->IDLenPresent));
-    PrintAndLogEx(SUCCESS, "\tType Name Format: [0x%02x] %s", header->TypeNameFormat, TypeNameFormat_s[header->TypeNameFormat]);
-
-    PrintAndLogEx(SUCCESS, "\tHeader length    : %zu", header->len);
-    PrintAndLogEx(SUCCESS, "\tType length      : %zu", header->TypeLen);
-    PrintAndLogEx(SUCCESS, "\tPayload length   : %zu", header->PayloadLen);
-    PrintAndLogEx(SUCCESS, "\tID length        : %zu", header->IDLen);
-    PrintAndLogEx(SUCCESS, "\tRecord length    : %zu", header->RecLen);
+    PrintAndLogEx(INFO, _CYAN_("Header info"));
+
+    PrintAndLogEx(SUCCESS, "  %s ....... Message begin", STRBOOL(header->MessageBegin));
+    PrintAndLogEx(SUCCESS, "   %s ...... Message end", STRBOOL(header->MessageEnd));
+    PrintAndLogEx(SUCCESS, "    %s ..... Chunk flag", STRBOOL(header->ChunkFlag));
+    PrintAndLogEx(SUCCESS, "     %s .... Short record bit", STRBOOL(header->ShortRecordBit));
+    PrintAndLogEx(SUCCESS, "      %s ... ID Len present", STRBOOL(header->IDLenPresent));
+    PrintAndLogEx(SUCCESS, "");
+
+    PrintAndLogEx(SUCCESS, " Header length...... %zu", header->len);
+    PrintAndLogEx(SUCCESS, " Type length........ %zu", header->TypeLen);
+    PrintAndLogEx(SUCCESS, " Payload length..... %zu", header->PayloadLen);
+    PrintAndLogEx(SUCCESS, " ID length.......... %zu", header->IDLen);
+    PrintAndLogEx(SUCCESS, " Record length...... %zu", header->RecLen);
+
+    PrintAndLogEx(SUCCESS, " Type name format... [ 0x%02x ] " _YELLOW_("%s"), header->TypeNameFormat, TypeNameFormat_s[header->TypeNameFormat]);
+    return PM3_SUCCESS;
+}
+
+static const char *get_curve_name(mbedtls_ecp_group_id grp_id) {
+    switch (grp_id) {
+        case MBEDTLS_ECP_DP_NONE:
+            return "";
+        case MBEDTLS_ECP_DP_SECP192R1:
+            return "SECP192R1";     // Domain parameters for the 192-bit curve defined by FIPS 186-4 and SEC1
+        case MBEDTLS_ECP_DP_SECP224R1:
+            return "SECP224R1";     // Domain parameters for the 224-bit curve defined by FIPS 186-4 and SEC1
+        case MBEDTLS_ECP_DP_SECP256R1:
+            return "SECP256R1";     // Domain parameters for the 256-bit curve defined by FIPS 186-4 and SEC1
+        case MBEDTLS_ECP_DP_SECP384R1:
+            return "SECP384R1";     // Domain parameters for the 384-bit curve defined by FIPS 186-4 and SEC1
+        case MBEDTLS_ECP_DP_SECP521R1:
+            return "SECP521R1";     // Domain parameters for the 521-bit curve defined by FIPS 186-4 and SEC1
+        case MBEDTLS_ECP_DP_BP256R1:
+            return "BP256R1";         // Domain parameters for 256-bit Brainpool curve
+        case MBEDTLS_ECP_DP_BP384R1:
+            return "BP384R1";         // Domain parameters for 384-bit Brainpool curve
+        case MBEDTLS_ECP_DP_BP512R1:
+            return "BP512R1";         // Domain parameters for 512-bit Brainpool curve
+        case MBEDTLS_ECP_DP_CURVE25519:
+            return "CURVE25519";   // Domain parameters for Curve25519
+        case MBEDTLS_ECP_DP_SECP192K1:
+            return "SECP192K1";     // Domain parameters for 192-bit "Koblitz" curve
+        case MBEDTLS_ECP_DP_SECP224K1:
+            return "SECP224K1";     // Domain parameters for 224-bit "Koblitz" curve
+        case MBEDTLS_ECP_DP_SECP256K1:
+            return "SECP256K1";     // Domain parameters for 256-bit "Koblitz" curve
+        case MBEDTLS_ECP_DP_CURVE448:
+            return "CURVE448";       // Domain parameters for Curve448
+        case MBEDTLS_ECP_DP_SECP128R1:
+            return "SECP128R1";     // Domain parameters for the 128-bit curve used for NXP originality check
+        default :
+            return "";
+    }
+    return "";
+}
+
+typedef struct {
+    mbedtls_ecp_group_id grp_id;
+    uint8_t keylen;
+    const char *desc;
+    const char *value;
+} PACKED ndef_publickey_t;
+
+static int ndef_print_signature(uint8_t *data, uint8_t data_len, uint8_t *signature, uint8_t sign_len) {
+
+    const ndef_publickey_t ndef_public_keys[] = {
+        { MBEDTLS_ECP_DP_SECP256R1, 65, "Minecraft Earth", "04760200b60315f31ff7951d0892b87930c34967dfbf57763afc775fc56a22b601f7b8fd9e47519524505322435b07d0782463f39400a39a9dbc06bab2225c082a"},
+    };
+
+    uint8_t i;
+    int reason = 0;
+    bool is_valid = false;
+    for (i = 0; i < ARRAYLEN(ndef_public_keys); i++) {
+
+        int dl = 0;
+        uint8_t key[ndef_public_keys[i].keylen];
+        param_gethex_to_eol(ndef_public_keys[i].value, 0, key, ndef_public_keys[i].keylen, &dl);
+
+        int res = ecdsa_signature_r_s_verify(ndef_public_keys[i].grp_id, key, data, data_len, signature, sign_len, false);
+        is_valid = (res == 0);
+        if (is_valid) {
+            reason = 1;
+            break;
+        }
+
+        // try with sha256
+        res = ecdsa_signature_r_s_verify(ndef_public_keys[i].grp_id, key, data, data_len, signature, sign_len, true);
+        is_valid = (res == 0);
+        if (is_valid) {
+            reason = 2;
+            break;
+        }
+    }
+
+    PrintAndLogEx(NORMAL, "");
+    PrintAndLogEx(INFO, "--- " _CYAN_("NDEF Signature"));
+    if (is_valid == false || i == ARRAYLEN(ndef_public_keys)) {
+        PrintAndLogEx(INFO, "    Elliptic curve parameters: %s", get_curve_name(ndef_public_keys[i].grp_id));
+        PrintAndLogEx(INFO, "               NDEF Signature: %s", sprint_hex_inrow(signature, 32));
+        PrintAndLogEx(SUCCESS, "       Signature verification: " _RED_("failed"));
+        return PM3_ESOFT;
+    }
+
+    PrintAndLogEx(INFO, " IC signature public key name: %s", ndef_public_keys[i].desc);
+    PrintAndLogEx(INFO, "IC signature public key value: %s", ndef_public_keys[i].value);
+    PrintAndLogEx(INFO, "    Elliptic curve parameters: %s", get_curve_name(ndef_public_keys[i].grp_id));
+    PrintAndLogEx(INFO, "               NDEF Signature: %s", sprint_hex_inrow(signature, 32));
+    PrintAndLogEx(SUCCESS, "       Signature verification: " _GREEN_("successful"));
+    switch (reason) {
+        case 1:
+            PrintAndLogEx(INFO, "                  Params used: signature, plain");
+            break;
+        case 2:
+            PrintAndLogEx(INFO, "                  Params used: signature, SHA256");
+            break;
+    }
     return PM3_SUCCESS;
 }
 
@@ -251,6 +364,8 @@ static int ndefDecodeSig2(uint8_t *sig, size_t siglen) {
                 PrintAndLogEx(SUCCESS, "\tsignature : " _GREEN_("ECDSA-%d"), slen * 8);
                 PrintAndLogEx(SUCCESS, "\t\tr: %s", sprint_hex(&sig[indx], slen));
                 PrintAndLogEx(SUCCESS, "\t\ts: %s", sprint_hex(&sig[indx + slen], slen));
+
+                ndef_print_signature(NULL, 0, NULL, 0);
             }
         } else {
             PrintAndLogEx(INFO, "\tsignature: unknown type");
@@ -301,18 +416,169 @@ static int ndefDecodeSig(uint8_t *sig, size_t siglen) {
     return PM3_ESOFT;
 }
 
+static int ndefDecodePayloadDeviceInfo(uint8_t *payload, size_t len) {
+    if (payload == NULL)
+        return PM3_EINVARG;
+    if (len < 1)
+        return PM3_EINVARG;
+
+    PrintAndLogEx(INFO, _CYAN_("Device information"));
+    uint8_t *p = payload;
+    p++;
+    uint8_t n = *(p++);
+    PrintAndLogEx(INFO, "Vendor........ " _YELLOW_("%.*s"), n, p);
+    p += n + 1;
+    n = *(p++);
+    PrintAndLogEx(INFO, "Model......... " _YELLOW_("%.*s"), n, p);
+    p += n + 1;
+    n = *(p++);
+    PrintAndLogEx(INFO, "Unique name... " _YELLOW_("%.*s"), n, p);
+    p += n + 1;
+    n = *(p++);
+    //uuid string
+    // record.uuid_string = '123e4567-e89b-12d3-a456-426655440000'
+    //  8-4-4-4-12 
+    char uuid[37] = {0};
+    sprintf(uuid, "%s-", sprint_hex_inrow(p, 4));
+    p += 4;
+    sprintf(uuid + strlen(uuid), "%s-", sprint_hex_inrow(p, 2));
+    p += 2;
+    sprintf(uuid + strlen(uuid), "%s-", sprint_hex_inrow(p, 2));
+    p += 2;
+    sprintf(uuid + strlen(uuid), "%s-", sprint_hex_inrow(p, 2));
+    p += 2;
+    sprintf(uuid + strlen(uuid), "%s", sprint_hex_inrow(p, 6));
+    p += 6;
+    PrintAndLogEx(INFO, "UUID.......... " _YELLOW_("%s"), uuid);
+    p++;
+    n = *(p++);    
+    PrintAndLogEx(INFO, "Version....... " _YELLOW_("%.*s"), n, p);
+    PrintAndLogEx(NORMAL, "");
+    return PM3_SUCCESS;
+}
+
+static int ndefDecodePayloadSmartPoster(uint8_t *ndef, size_t ndeflen, bool print, bool verbose) {
+    if (print) {
+        PrintAndLogEx(INFO, _YELLOW_("Well Known Record - Smartposter {"));
+    }
+
+    NDEFHeader_t NDEFHeader = {0};
+    int res = ndefDecodeHeader(ndef, ndeflen, &NDEFHeader);
+    if (res != PM3_SUCCESS) {
+        PrintAndLogEx(FAILED, "decode header failed..");
+        return res;
+    }
+
+    if (verbose) {
+        ndefPrintHeader(&NDEFHeader);
+    }
+
+    if (NDEFHeader.TypeLen && NDEFHeader.PayloadLen) {
+        ndefDecodePayload(&NDEFHeader);
+    }
+
+    if (NDEFHeader.TypeLen) {
+        PrintAndLogEx(INFO, "Type data");
+        print_buffer(NDEFHeader.Type, NDEFHeader.TypeLen, 1);
+    }
+    if (NDEFHeader.IDLen) {
+        PrintAndLogEx(INFO, "ID data");
+        print_buffer(NDEFHeader.ID, NDEFHeader.IDLen, 1);
+    }
+    if (NDEFHeader.PayloadLen) {
+        PrintAndLogEx(INFO, "Payload data");
+        print_buffer(NDEFHeader.Payload, NDEFHeader.PayloadLen, 1);
+    }
+    // recursive
+    if (NDEFHeader.MessageEnd == false) {
+        ndefDecodePayloadSmartPoster(ndef + NDEFHeader.RecLen, ndeflen - NDEFHeader.RecLen, false, false); 
+    }
+
+    if (print) {
+        PrintAndLogEx(INFO, _YELLOW_("}"));
+    }
+    return PM3_SUCCESS;
+}
+
+static int ndefDecodeMime_wifi(NDEFHeader_t *ndef) {
+    PrintAndLogEx(INFO, _CYAN_("WiFi details"));
+    if (ndef->PayloadLen > 1) {
+        PrintAndLogEx(INFO, ">>> decorder, to be implemented <<<");
+    }
+    return PM3_SUCCESS;
+}
+
+static int ndefDecodeMime_vcard(NDEFHeader_t *ndef) {
+    PrintAndLogEx(INFO, _CYAN_("VCARD details"));
+    if (ndef->PayloadLen > 1) {
+        PrintAndLogEx(INFO, "Data... " _YELLOW_("%.*s"), (int)ndef->PayloadLen, ndef->Payload);
+        PrintAndLogEx(INFO, ">>> decorder, to be implemented <<<");
+    }
+    return PM3_SUCCESS;
+}
+
+static int ndefDecodeMime_bt(NDEFHeader_t *ndef) {
+    PrintAndLogEx(INFO, "Type............ " _YELLOW_("%.*s"), (int)ndef->TypeLen, ndef->Type );
+    if (ndef->PayloadLen > 1) {
+        uint16_t ooblen = (ndef->Payload[1] << 8 | ndef->Payload[0]);
+        PrintAndLogEx(INFO, "OOB data len.... %u", ooblen);
+        PrintAndLogEx(INFO, "BT MAC.......... " _YELLOW_("%s"), sprint_hex(ndef->Payload + 2, 6));
+        // Let's check payload[8]. Tells us a bit about the UUID's. If 0x07 then it tells us a service UUID is 128bit  
+        switch (ndef->Payload[8]) {  
+            case 0x02:  
+                PrintAndLogEx(INFO, "Optional Data... incomplete list 16-bit UUID's");
+                break;  
+            case 0x03:  
+                PrintAndLogEx(INFO, "Optional Data... complete list 16-bit UUID's");
+                break;  
+            case 0x04:  
+                PrintAndLogEx(INFO, "Optional Data... incomplete list 32-bit UUID's");
+                break;  
+            case 0x05:  
+                PrintAndLogEx(INFO, "Optional Data... complete list 32-bit UUID's");
+                break;  
+            case 0x06:  
+                PrintAndLogEx(INFO, "Optional Data... incomplete list 128-bit UUID's");
+                break;  
+            case 0x07:  
+                PrintAndLogEx(INFO, "Optional Data... complete list 128-bit UUID's");
+                break;  
+            default:  
+                PrintAndLogEx(INFO, "Optional Data... [ %02x ]", ndef->Payload[8]);
+                break;
+        }  
+        // Let's check payload[9]. If 0x08 then SHORT_NAME or if 0x09 then COMPLETE_NAME  
+        if (ndef->Payload[9] == 0x08 ) {  
+            PrintAndLogEx(INFO, "Short name...... " _YELLOW_("%s"), ndef->Payload + 10, ndef->PayloadLen - 10);
+        } else if (ndef->Payload[9] == 0x09 ) {  
+            PrintAndLogEx(INFO, "Complete name... " _YELLOW_("%s"),   ndef->Payload + 10, ndef->PayloadLen - 10);
+        } else {  
+            PrintAndLogEx(INFO, "[ %02x ]", ndef->Payload[9]);
+        }
+        PrintAndLogEx(NORMAL, "");
+    }
+    return PM3_SUCCESS;
+}
+
 static int ndefDecodePayload(NDEFHeader_t *ndef) {
 
+    PrintAndLogEx(INFO, "");
     switch (ndef->TypeNameFormat) {
+        case tnfEmptyRecord:
+            PrintAndLogEx(INFO, "Empty Record");
+            if (ndef->TypeLen != 0 || ndef->IDLen != 0 || ndef->PayloadLen != 0) {
+                PrintAndLogEx(FAILED, "unexpected data in TNF_EMPTY record");
+                break;
+            }
+            break;
         case tnfWellKnownRecord:
-            PrintAndLogEx(INFO, "Well Known Record");
-            PrintAndLogEx(INFO, "\ttype\t: %.*s", (int)ndef->TypeLen, ndef->Type);
 
             if (!strncmp((char *)ndef->Type, "T", ndef->TypeLen)) {
+                PrintAndLogEx(INFO, _CYAN_("Text"));
                 uint8_t utf8 = (ndef->Payload[0] >> 7);
                 uint8_t lc_len = ndef->Payload[0] & 0x3F;
                 PrintAndLogEx(INFO,
-                              "\tUTF %d\t: " _GREEN_("%.*s") ", " _GREEN_("%.*s"),
+                              "    UTF %d... " _GREEN_("%.*s") ", " _GREEN_("%.*s"),
                               (utf8 == 0) ? 8 : 16,
                               lc_len,
                               ndef->Payload + 1,
@@ -322,8 +588,9 @@ static int ndefDecodePayload(NDEFHeader_t *ndef) {
             }
 
             if (!strncmp((char *)ndef->Type, "U", ndef->TypeLen)) {
+                PrintAndLogEx(INFO, _CYAN_("URL"));
                 PrintAndLogEx(INFO
-                              , "\turi\t: " _GREEN_("%s%.*s")
+                              , "    uri... " _GREEN_("%s%.*s")
                               , (ndef->Payload[0] <= 0x23 ? URI_s[ndef->Payload[0]] : "[err]")
                               , (int)(ndef->PayloadLen - 1)
                               , &ndef->Payload[1]
@@ -331,34 +598,67 @@ static int ndefDecodePayload(NDEFHeader_t *ndef) {
             }
 
             if (!strncmp((char *)ndef->Type, "Sig", ndef->TypeLen)) {
+                PrintAndLogEx(INFO, _CYAN_("Signature"));
                 ndefDecodeSig(ndef->Payload, ndef->PayloadLen);
             }
 
-            break;
-        case tnfAbsoluteURIRecord:
-            PrintAndLogEx(INFO, "Absolute URI Record");
-            PrintAndLogEx(INFO, "\ttype    : %.*s", (int)ndef->TypeLen, ndef->Type);
-            PrintAndLogEx(INFO, "\tpayload : %.*s", (int)ndef->PayloadLen, ndef->Payload);
-            break;
-        case tnfEmptyRecord:
-            PrintAndLogEx(INFO, "Empty Record");
-            PrintAndLogEx(INFO, "\t -to be impl-");
+            if (!strncmp((char *)ndef->Type, "Sp", ndef->TypeLen)) {
+                ndefDecodePayloadSmartPoster(ndef->Payload, ndef->PayloadLen, true, false);
+            }
+
+            if (!strncmp((char *)ndef->Type, "Di", ndef->TypeLen)) {
+                ndefDecodePayloadDeviceInfo(ndef->Payload, ndef->PayloadLen);
+            }
+
+            if (!strncmp((char *)ndef->Type, "Hc", ndef->TypeLen)) {
+                PrintAndLogEx(INFO, _CYAN_("Handover carrier"));
+                PrintAndLogEx(INFO, "- decoder to be impl -");
+            }
+
+            if (!strncmp((char *)ndef->Type, "Hr", ndef->TypeLen)) {
+                PrintAndLogEx(INFO, _CYAN_("Handover request"));
+                PrintAndLogEx(INFO, "- decoder to be impl -");
+            }
+
+            if (!strncmp((char *)ndef->Type, "Hs", ndef->TypeLen)) {
+                PrintAndLogEx(INFO, _CYAN_("Handover select"));
+                PrintAndLogEx(INFO, "- decoder to be impl -");
+            }
+
+            if (!strncmp((char *)ndef->Type, "ac", ndef->TypeLen)) {
+                PrintAndLogEx(INFO, _CYAN_("Alternative carrier"));
+                PrintAndLogEx(INFO, "- decoder to be impl -");
+            }
             break;
         case tnfMIMEMediaRecord:
             PrintAndLogEx(INFO, "MIME Media Record");
-            PrintAndLogEx(INFO, "\t -to be impl-");
+
+            if (str_startswith((const char *)ndef->Type, NDEF_WIFIAPPL)) {
+                ndefDecodeMime_wifi(ndef);
+            }
+            if (str_startswith((const char *)ndef->Type, NDEF_VCARDTEXT)) {
+                ndefDecodeMime_vcard(ndef);
+            }
+            if (str_startswith((const char *)ndef->Type, NDEF_BLUEAPPL)) {
+                ndefDecodeMime_bt(ndef);
+            }
+
+            break;
+        case tnfAbsoluteURIRecord:
+            PrintAndLogEx(INFO, "Absolute URI Record");
+            PrintAndLogEx(INFO, "    payload : %.*s", (int)ndef->PayloadLen, ndef->Payload);
             break;
         case tnfExternalRecord:
             PrintAndLogEx(INFO, "External Record");
-            PrintAndLogEx(INFO, "\t -to be impl-");
-            break;
-        case tnfUnchangedRecord:
-            PrintAndLogEx(INFO, "Unchanged Record");
-            PrintAndLogEx(INFO, "\t -to be impl-");
+            PrintAndLogEx(INFO, "- decoder to be impl -");
             break;
         case tnfUnknownRecord:
             PrintAndLogEx(INFO, "Unknown Record");
-            PrintAndLogEx(INFO, "\t -to be impl-");
+            PrintAndLogEx(INFO, "- decoder to be impl -");
+            break;
+        case tnfUnchangedRecord:
+            PrintAndLogEx(INFO, "Unchanged Record");
+            PrintAndLogEx(INFO, "- decoder to be impl -");
             break;
     }
     return PM3_SUCCESS;
@@ -371,19 +671,22 @@ static int ndefRecordDecodeAndPrint(uint8_t *ndefRecord, size_t ndefRecordLen) {
         return res;
 
     ndefPrintHeader(&NDEFHeader);
+    PrintAndLogEx(INFO, "");
+    PrintAndLogEx(INFO, _CYAN_("Payload info"));
 
     if (NDEFHeader.TypeLen) {
-        PrintAndLogEx(INFO, "Type data:");
+        PrintAndLogEx(INFO, "Type data");
         print_buffer(NDEFHeader.Type, NDEFHeader.TypeLen, 1);
     }
     if (NDEFHeader.IDLen) {
-        PrintAndLogEx(INFO, "ID data:");
+        PrintAndLogEx(INFO, "ID data");
         print_buffer(NDEFHeader.ID, NDEFHeader.IDLen, 1);
     }
     if (NDEFHeader.PayloadLen) {
-        PrintAndLogEx(INFO, "Payload data:");
+        PrintAndLogEx(INFO, "Payload data");
         print_buffer(NDEFHeader.Payload, NDEFHeader.PayloadLen, 1);
-        if (NDEFHeader.TypeLen)
+    }
+    if (NDEFHeader.TypeLen && NDEFHeader.PayloadLen) {
             ndefDecodePayload(&NDEFHeader);
     }
 
@@ -438,8 +741,6 @@ int NDEFDecodeAndPrint(uint8_t *ndef, size_t ndefLen, bool verbose) {
     PrintAndLogEx(NORMAL, "");
     PrintAndLogEx(INFO, "--- " _CYAN_("NDEF parsing") " ----------------");
     while (indx < ndefLen) {
-
-        PrintAndLogEx(INFO, "-----------------------------------------------------");
         switch (ndef[indx]) {
             case 0x00: {
                 indx++;
@@ -462,11 +763,11 @@ int NDEFDecodeAndPrint(uint8_t *ndef, size_t ndefLen, bool verbose) {
                     uint8_t Size = ndef[indx + 1];
                     uint8_t BytesLockedPerLockBit = (ndef[indx + 2] >> 4) & 0x0f;
                     uint8_t bytes_per_page = ndef[indx + 2] & 0x0f;
-                    PrintAndLogEx(SUCCESS, " Pages addr (number of pages) : %d", pages_addr);
-                    PrintAndLogEx(SUCCESS, "Byte offset (number of bytes) : %d", byte_offset);
-                    PrintAndLogEx(SUCCESS, "Size in bits of the lock area : %d. bytes approx: %d", Size, Size / 8);
-                    PrintAndLogEx(SUCCESS, "       Number of bytes / page : %d", bytes_per_page);
-                    PrintAndLogEx(SUCCESS, "Bytes Locked Per LockBit.");
+                    PrintAndLogEx(SUCCESS, " Pages addr (number of pages)... %d", pages_addr);
+                    PrintAndLogEx(SUCCESS, "Byte offset (number of bytes)... %d", byte_offset);
+                    PrintAndLogEx(SUCCESS, "Size in bits of the lock area %d. bytes approx %d", Size, Size / 8);
+                    PrintAndLogEx(SUCCESS, "       Number of bytes / page... %d", bytes_per_page);
+                    PrintAndLogEx(SUCCESS, "Bytes Locked Per LockBit");
                     PrintAndLogEx(SUCCESS, "   number of bytes that each dynamic lock bit is able to lock: %d", BytesLockedPerLockBit);
                 }
                 indx += len;
