commit c9a1ff6e60cdc8573dba6ea740bbc2e5c2929bcc
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jan 16 19:03:17 2022 +0100

    skeleton for seos annotations

diff --git a/client/src/cmdhflist.c b/client/src/cmdhflist.c
index 1d7bda5b7..721b0949d 100644
--- a/client/src/cmdhflist.c
+++ b/client/src/cmdhflist.c
@@ -174,16 +174,16 @@ uint8_t iclass_CRC_check(bool isResponse, uint8_t *d, uint8_t n) {
 
 int applyIso14443a(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize, bool is_response) {
 
-    if (! is_response) {
+    if (is_response == false) {
         if ((gs_ntag_i2c_state == 1) && (cmdsize == 6) && (memcmp(cmd + 1, "\x00\x00\x00", 3) == 0)) {
             snprintf(exp, size, "SECTOR(%d)", cmd[0]);
             gs_ntag_i2c_state = 0;
-            return 1;
+            return PM3_SUCCESS;
         }
 
         if (cmdsize > 10 && (memcmp(cmd, "\x6a\x02\xC8\x01\x00\x03\x00\x02\x79", 9) == 0)) {
             snprintf(exp, size, "ECP");
-            return 1;
+            return PM3_SUCCESS;
         }
 
         gs_ntag_i2c_state = 0;
@@ -263,7 +263,7 @@ int applyIso14443a(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize, bool i
                 break;
             case MIFARE_CMD_RESTORE:
 
-                if (cmdsize == 4)
+                if (cmdsize == 4) {
                     // cmd0 == 0xC2 and cmd1 == 0xFF
                     // high probability its SELECT SECTOR COMMAND:
                     if (cmd[1] == 0xFF) {
@@ -271,8 +271,11 @@ int applyIso14443a(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize, bool i
                         gs_ntag_i2c_state = 1;
                     } else {
                         snprintf(exp, size, "RESTORE(%d)", cmd[1]);
-                    } else
-                    return 0;
+                    } 
+                } else {
+                    return PM3_ESOFT;
+                }
+
                 break;
             case MIFARE_CMD_TRANSFER:
                 snprintf(exp, size, "TRANSFER(%d)", cmd[1]);
@@ -318,7 +321,7 @@ int applyIso14443a(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize, bool i
                     }
                     gs_mfuc_state = 3;
                 } else {
-                    return 0;
+                    return PM3_ESOFT;
                 }
                 break;
             case MIFARE_ULEV1_AUTH:
@@ -386,7 +389,7 @@ int applyIso14443a(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize, bool i
 
                 break;
             default:
-                return 0;
+                return PM3_ESOFT;
         }
     } else {
         if (gs_mfuc_state == 1) {
@@ -405,14 +408,14 @@ int applyIso14443a(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize, bool i
                 if (trace_mfuc_try_default_3des_keys(&gs_mfuc_key, gs_mfuc_state, gs_mfuc_authdata) == PM3_SUCCESS) {
                     snprintf(exp, size, "AUTH-2 ANSW OK");
                     gs_mfuc_state = 0;
-                    return 1;
+                    return PM3_SUCCESS;
                 }
             }
             gs_mfuc_state = 0;
         }
-        return 0;
+        return PM3_ESOFT;
     }
-    return 1;
+    return PM3_SUCCESS;
 }
 
 void annotateIso14443a(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize, bool is_response) {
@@ -829,7 +832,7 @@ void annotateIso7816(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize) {
 void annotateMfDesfire(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize) {
 
     // it's basically a ISO14443a tag, so try annotation from there
-    if (applyIso14443a(exp, size, cmd, cmdsize, false) == 0) {
+    if (applyIso14443a(exp, size, cmd, cmdsize, false) != PM3_SUCCESS ) {
 
         // S-block 11xxx010
         if ((cmd[0] & 0xC0) && (cmdsize == 3)) {
@@ -1173,6 +1176,22 @@ void annotateCryptoRF(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize) {
     }
 }
 
+void annotateSeos(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize) {
+
+    // it's basically a ISO14443a tag, so try annotation from there
+    if (applyIso14443a(exp, size, cmd, cmdsize, false) != PM3_SUCCESS) {
+
+//        switch (cmd[0]) {
+//            default: 
+//                break;
+//        };
+
+          // apply ISO7816 annotations?
+//        if (annotateIso7816(exp, size, cmd, cmdsize) == 0) {
+//        }
+          // apply SEOS annotations?
+    }
+}
 
 // LEGIC
 // 1 = read
diff --git a/client/src/cmdhflist.h b/client/src/cmdhflist.h
index d42cdeba9..6499938eb 100644
--- a/client/src/cmdhflist.h
+++ b/client/src/cmdhflist.h
@@ -61,6 +61,8 @@ void annotateMifare(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize,
 void annotateLTO(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize);
 void annotateCryptoRF(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize);
 
+void annotateSeos(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize);
+
 bool DecodeMifareData(uint8_t *cmd, uint8_t cmdsize, uint8_t *parity, bool isResponse, uint8_t *mfData, size_t *mfDataLen, const uint64_t *dicKeys, uint32_t dicKeysCount);
 bool NTParityChk(AuthData_t *ad, uint32_t ntx);
 bool NestedCheckKey(uint64_t key, AuthData_t *ad, uint8_t *cmd, uint8_t cmdsize, uint8_t *parity);
diff --git a/client/src/cmdtrace.c b/client/src/cmdtrace.c
index c06d0cfe6..2e6a04ad9 100644
--- a/client/src/cmdtrace.c
+++ b/client/src/cmdtrace.c
@@ -206,6 +206,7 @@ static uint16_t printTraceLine(uint16_t tracepos, uint16_t traceLen, uint8_t *tr
             case ISO_14443A:
             case MFDES:
             case LTO:
+            case SEOS:
                 crcStatus = iso14443A_CRC_check(hdr->isResponse, frame, data_len);
                 break;
             case ISO_7816_4:
@@ -265,7 +266,7 @@ static uint16_t printTraceLine(uint16_t tracepos, uint16_t traceLen, uint8_t *tr
                 && protocol != FELICA
                 && protocol != LTO
                 && protocol != PROTO_CRYPTORF
-                && (hdr->isResponse || protocol == ISO_14443A || protocol == PROTO_MIFARE)
+                && (hdr->isResponse || protocol == ISO_14443A || protocol == PROTO_MIFARE || protocol == SEOS)
                 && (oddparity8(frame[j]) != ((parityBits >> (7 - (j & 0x0007))) & 0x01))) {
 
             snprintf(line[j / 18] + ((j % 18) * 4), 120, "%02x! ", frame[j]);
@@ -418,6 +419,9 @@ static uint16_t printTraceLine(uint16_t tracepos, uint16_t traceLen, uint8_t *tr
             case PROTO_CRYPTORF:
                 annotateCryptoRF(explanation, sizeof(explanation), frame, data_len);
                 break;
+            case SEOS:
+                annotateSeos(explanation, sizeof(explanation), frame, data_len);            
+                break;
             default:
                 break;
         }
@@ -703,22 +707,25 @@ int CmdTraceList(const char *Cmd) {
                   "Annotate trace buffer with selected protocol data\n"
                   "You can load a trace from file (see `trace load -h`) or it be downloaded from device by default\n",
                   "trace list -t raw      -> just show raw data without annotations\n"
+                  "\n"
                   "trace list -t 14a      -> interpret as " _YELLOW_("ISO14443-A") "\n"
-                  "trace list -t thinfilm -> interpret as " _YELLOW_("Thinfilm") "\n"
-                  "trace list -t topaz    -> interpret as " _YELLOW_("Topaz") "\n"
-                  "trace list -t mf       -> interpret as " _YELLOW_("MIFARE Classic") " and decrypt crypto1 stream\n"
-                  "trace list -t des      -> interpret as " _YELLOW_("MIFARE DESFire") "\n"
                   "trace list -t 14b      -> interpret as " _YELLOW_("ISO14443-B") "\n"
-                  "trace list -t 7816     -> interpret as " _YELLOW_("ISO7816-4") "\n"
                   "trace list -t 15       -> interpret as " _YELLOW_("ISO15693") "\n"
-                  "trace list -t iclass   -> interpret as " _YELLOW_("iCLASS") "\n"
-                  "trace list -t legic    -> interpret as " _YELLOW_("LEGIC") "\n"
+                  "trace list -t 7816     -> interpret as " _YELLOW_("ISO7816-4") "\n"
+                  "trace list -t cryptorf -> interpret as " _YELLOW_("CryptoRF") "\n\n"
+                  "trace list -t des      -> interpret as " _YELLOW_("MIFARE DESFire") "\n"
                   "trace list -t felica   -> interpret as " _YELLOW_("ISO18092 / FeliCa") "\n"
                   "trace list -t hitag1   -> interpret as " _YELLOW_("Hitag1") "\n"
                   "trace list -t hitag2   -> interpret as " _YELLOW_("Hitag2") "\n"
                   "trace list -t hitags   -> interpret as " _YELLOW_("HitagS") "\n"
+                  "trace list -t iclass   -> interpret as " _YELLOW_("iCLASS") "\n"
+                  "trace list -t legic    -> interpret as " _YELLOW_("LEGIC") "\n"
                   "trace list -t lto      -> interpret as " _YELLOW_("LTO-CM") "\n"
-                  "trace list -t cryptorf -> interpret as " _YELLOW_("CryptoRF") "\n\n"
+                  "trace list -t mf       -> interpret as " _YELLOW_("MIFARE Classic") " and decrypt crypto1 stream\n"
+                  "trace list -t seos     -> interpret as " _YELLOW_("SEOS") "\n"
+                  "trace list -t thinfilm -> interpret as " _YELLOW_("Thinfilm") "\n"
+                  "trace list -t topaz    -> interpret as " _YELLOW_("Topaz") "\n"                  
+                  "\n"
                   "trace list -t mf --dict <mfc_default_keys>    -> use dictionary keys file\n"
                   "trace list -t 14a -f                          -> show frame delay times\n"
                   "trace list -t 14a -1                          -> use trace buffer "
@@ -766,23 +773,24 @@ int CmdTraceList(const char *Cmd) {
     uint8_t protocol = -1;
 
     // validate type of output
-    if (strcmp(type,      "iclass") == 0)   protocol = ICLASS;
-    else if (strcmp(type, "14a") == 0)      protocol = ISO_14443A;
+    if      (strcmp(type, "14a") == 0)      protocol = ISO_14443A;
     else if (strcmp(type, "14b") == 0)      protocol = ISO_14443B;
-    else if (strcmp(type, "topaz") == 0)    protocol = TOPAZ;
+    else if (strcmp(type, "15") == 0)       protocol = ISO_15693;
     else if (strcmp(type, "7816") == 0)     protocol = ISO_7816_4;
+    else if (strcmp(type, "cryptorf") == 0) protocol = PROTO_CRYPTORF;
     else if (strcmp(type, "des") == 0)      protocol = MFDES;
-    else if (strcmp(type, "legic") == 0)    protocol = LEGIC;
-    else if (strcmp(type, "15") == 0)       protocol = ISO_15693;
     else if (strcmp(type, "felica") == 0)   protocol = FELICA;
-    else if (strcmp(type, "mf") == 0)       protocol = PROTO_MIFARE;
     else if (strcmp(type, "hitag1") == 0)   protocol = PROTO_HITAG1;
     else if (strcmp(type, "hitag2") == 0)   protocol = PROTO_HITAG2;
     else if (strcmp(type, "hitags") == 0)   protocol = PROTO_HITAGS;
-    else if (strcmp(type, "thinfilm") == 0) protocol = THINFILM;
+    else if (strcmp(type, "iclass") == 0)   protocol = ICLASS;
+    else if (strcmp(type, "legic") == 0)    protocol = LEGIC;
     else if (strcmp(type, "lto") == 0)      protocol = LTO;
-    else if (strcmp(type, "cryptorf") == 0) protocol = PROTO_CRYPTORF;
+    else if (strcmp(type, "mf") == 0)       protocol = PROTO_MIFARE;
     else if (strcmp(type, "raw") == 0)      protocol = -1;
+    else if (strcmp(type, "seos") == 0)     protocol = SEOS;
+    else if (strcmp(type, "thinfilm") == 0) protocol = THINFILM;
+    else if (strcmp(type, "topaz") == 0)    protocol = TOPAZ;
     else if (strcmp(type, "") == 0)         protocol = -1;
     else {
         PrintAndLogEx(FAILED, "Unknown protocol \"%s\"", type);
diff --git a/include/protocols.h b/include/protocols.h
index d2aa4c628..b13de57ec 100644
--- a/include/protocols.h
+++ b/include/protocols.h
@@ -393,6 +393,7 @@ ISO 7816-4 Basic interindustry commands. For command APDU's.
 #define PROTO_HITAG2    13
 #define PROTO_HITAGS    14
 #define PROTO_CRYPTORF  15
+#define SEOS            16
 
 // Picopass fuses
 #define FUSE_FPERS   0x80
