commit 2879b23a1ca353a0c319fbd0be16cbe1cf4a5ce2
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Feb 24 17:38:01 2022 +0100

    unify text

diff --git a/client/src/cmdhfcipurse.c b/client/src/cmdhfcipurse.c
index eb3453eec..4e91d5d69 100644
--- a/client/src/cmdhfcipurse.c
+++ b/client/src/cmdhfcipurse.c
@@ -377,12 +377,13 @@ static int SelectCommandEx(bool selectDefaultFile, bool useAID, uint8_t *aid, si
         res = CIPURSESelectFileEx(true, true, fileId, buf, bufSize, len, sw);
         if (res != 0 || *sw != 0x9000) {
             if (verbose) {
-                PrintAndLogEx(ERR, "Cipurse select file 0x%04x " _RED_("error") ". Card returns 0x%04x", fileId, *sw);
+                PrintAndLogEx(ERR, "Cipurse select file 0x%04x " _RED_("error"));
+                 PrintAndLogEx(ERR, "Card returns 0x%04x", fileId, *sw);
             }
             return PM3_ESOFT;
         }
         if (verbose) {
-            PrintAndLogEx(INFO, "Cipurse select file " _CYAN_("0x%04x ") _GREEN_("OK"), fileId);
+            PrintAndLogEx(INFO, "Cipurse select file " _YELLOW_("0x%04X ") " ( " _GREEN_("ok") " )", fileId);
         }
 
     } else if (selectDefaultFile) {
diff --git a/client/src/cmdlfem4x50.c b/client/src/cmdlfem4x50.c
index 15c6bf783..3c0bee80b 100644
--- a/client/src/cmdlfem4x50.c
+++ b/client/src/cmdlfem4x50.c
@@ -359,9 +359,9 @@ int CmdEM4x50Login(const char *Cmd) {
 
     // print response
     if (resp.status == PM3_SUCCESS)
-        PrintAndLogEx(SUCCESS, "Login " _GREEN_("ok"));
+        PrintAndLogEx(SUCCESS, "Login ( " _GREEN_("ok") " )");
     else
-        PrintAndLogEx(FAILED, "Login " _RED_("failed"));
+        PrintAndLogEx(FAILED, "Login ( " _RED_("failed") " )");
 
     return resp.status;
 }
@@ -966,7 +966,7 @@ int CmdEM4x50WritePwd(const char *Cmd) {
         return PM3_EFAILED;
     }
 
-    PrintAndLogEx(SUCCESS, "Writing new password %s (%s)"
+    PrintAndLogEx(SUCCESS, "Writing new password %s ( %s )"
                   , sprint_hex_inrow(npwd, sizeof(npwd))
                   , _GREEN_("ok")
                  );
@@ -1015,9 +1015,9 @@ int CmdEM4x50Wipe(const char *Cmd) {
     }
 
     if (resp.status == PM3_SUCCESS) {
-        PrintAndLogEx(SUCCESS, "Resetting password to 00000000 (" _GREEN_("ok") ")");
+        PrintAndLogEx(SUCCESS, "Resetting password to 00000000 ( " _GREEN_("ok") " )");
     } else {
-        PrintAndLogEx(FAILED, "Resetting password " _RED_("failed"));
+        PrintAndLogEx(FAILED, "Resetting password ( " _RED_("failed") " )");
         return PM3_ESOFT;
     }
 
diff --git a/client/src/cmdlffdxb.c b/client/src/cmdlffdxb.c
index 495c867a3..1d406ea20 100644
--- a/client/src/cmdlffdxb.c
+++ b/client/src/cmdlffdxb.c
@@ -581,7 +581,7 @@ int demodFDXB(bool verbose) {
 
     uint8_t c[] = {0, 0};
     compute_crc(CRC_11784, raw, sizeof(raw), &c[0], &c[1]);
-    PrintAndLogEx(SUCCESS, "CRC-16             0x%04X (%s)", crc, (crc == (c[1] << 8 | c[0])) ? _GREEN_("ok") : _RED_("fail"));
+    PrintAndLogEx(SUCCESS, "CRC-16             0x%04X ( %s )", crc, (crc == (c[1] << 8 | c[0])) ? _GREEN_("ok") : _RED_("fail"));
     // iceman: crc doesn't protect the extended data?
     PrintAndLogEx(SUCCESS, "Raw                " _GREEN_("%s"), sprint_hex(raw, 8));
 
diff --git a/client/src/cmdlfio.c b/client/src/cmdlfio.c
index 924608dcb..429b1ef8e 100644
--- a/client/src/cmdlfio.c
+++ b/client/src/cmdlfio.c
@@ -140,9 +140,9 @@ int demodIOProx(bool verbose) {
     char crc_str[36] = {0};
 
     if (crc == calccrc) {
-        snprintf(crc_str, sizeof(crc_str), "(" _GREEN_("ok") ")");
+        snprintf(crc_str, sizeof(crc_str), "( " _GREEN_("ok") " )");
     } else {
-        snprintf(crc_str, sizeof(crc_str), "(" _RED_("fail") ") 0x%02X != 0x%02X", crc, calccrc);
+        snprintf(crc_str, sizeof(crc_str), "( " _RED_("fail") " ) 0x%02X != 0x%02X", crc, calccrc);
         retval = PM3_ESOFT;
     }
 
diff --git a/client/src/cmdlfjablotron.c b/client/src/cmdlfjablotron.c
index 0e886cf53..cac3a1329 100644
--- a/client/src/cmdlfjablotron.c
+++ b/client/src/cmdlfjablotron.c
@@ -100,7 +100,7 @@ int demodJablotron(bool verbose) {
     uint8_t chksum = raw2 & 0xFF;
     bool isok = (chksum == jablontron_chksum(g_DemodBuffer));
 
-    PrintAndLogEx(DEBUG, "Checksum: %02X (%s)", chksum, isok ? _GREEN_("ok") : _RED_("Fail"));
+    PrintAndLogEx(DEBUG, "Checksum: %02X ( %s )", chksum, isok ? _GREEN_("ok") : _RED_("Fail"));
 
     id = DEC2BCD(id);
     // Printed format: 1410-nn-nnnn-nnnn
diff --git a/client/src/cmdlfnedap.c b/client/src/cmdlfnedap.c
index fb990be72..acb68c406 100644
--- a/client/src/cmdlfnedap.c
+++ b/client/src/cmdlfnedap.c
@@ -151,7 +151,7 @@ int demodNedap(bool verbose) {
                       , customerCode
                       , sprint_hex_inrow(data, size / 8)
                      );
-        PrintAndLogEx(DEBUG, "Checksum (%s) 0x%04X",  _GREEN_("ok"), checksum);
+        PrintAndLogEx(DEBUG, "Checksum ( %s ) 0x%04X",  _GREEN_("ok"), checksum);
 
     } else {
         PrintAndLogEx(ERR, "Invalid idx (1:%02x - 2:%02x - 3:%02x - 4:%02x - 5:%02x)", idxC1, idxC2, idxC3, idxC4, idxC5);
diff --git a/client/src/cmdlfnexwatch.c b/client/src/cmdlfnexwatch.c
index ccf98961b..9f24a87b4 100644
--- a/client/src/cmdlfnexwatch.c
+++ b/client/src/cmdlfnexwatch.c
@@ -237,12 +237,12 @@ int demodNexWatch(bool verbose) {
 
 
     if (parity == calc_parity) {
-        PrintAndLogEx(DEBUG, "          parity : %s (0x%X)", _GREEN_("ok"), parity);
+        PrintAndLogEx(DEBUG, "          parity : ( %s ) 0x%X", _GREEN_("ok"), parity);
     } else {
-        PrintAndLogEx(DEBUG, "          parity : %s (0x%X != 0x%X)", _RED_("fail"), parity, calc_parity);
+        PrintAndLogEx(DEBUG, "          parity : ( %s ) 0x%X != 0x%X", _RED_("fail"), parity, calc_parity);
     }
 
-    PrintAndLogEx(DEBUG, "        checksum : %s (0x%02X)", (m_idx < ARRAYLEN(items)) ? _GREEN_("ok") : _RED_("fail"), chk);
+    PrintAndLogEx(DEBUG, "        checksum : ( %s ) 0x%02X", (m_idx < ARRAYLEN(items)) ? _GREEN_("ok") : _RED_("fail"), chk);
 
     PrintAndLogEx(INFO, " Raw : " _YELLOW_("%08"PRIX32"%08"PRIX32"%08"PRIX32), raw1, raw2, raw3);
     return PM3_SUCCESS;
diff --git a/client/src/cmdlfpyramid.c b/client/src/cmdlfpyramid.c
index bad1afaf0..8f9e5fccb 100644
--- a/client/src/cmdlfpyramid.c
+++ b/client/src/cmdlfpyramid.c
@@ -170,7 +170,7 @@ int demodPyramid(bool verbose) {
         PrintAndLogEx(SUCCESS, "Pyramid - len: " _GREEN_("%d") " -unknown- Card: " _GREEN_("%d") ", Raw: %08x%08x%08x%08x", fmtLen, cardnum, rawHi3, rawHi2, rawHi, rawLo);
     }
 
-    PrintAndLogEx(DEBUG, "DEBUG: Pyramid: checksum : 0x%02X - 0x%02X - %s"
+    PrintAndLogEx(DEBUG, "DEBUG: Pyramid: checksum : 0x%02X - 0x%02X ( %s )"
                   , checksum
                   , checkCS
                   , (checksum == checkCS) ? _GREEN_("ok") : _RED_("fail")
diff --git a/client/src/cmdlfsecurakey.c b/client/src/cmdlfsecurakey.c
index aae0a7f73..5a109e2d9 100644
--- a/client/src/cmdlfsecurakey.c
+++ b/client/src/cmdlfsecurakey.c
@@ -112,7 +112,7 @@ int demodSecurakey(bool verbose) {
 
     PrintAndLogEx(SUCCESS, "Securakey - len: " _GREEN_("%u") " FC: " _GREEN_("0x%X")" Card: " _GREEN_("%u") ", Raw: %08X%08X%08X", bitLen, fc, cardid, raw1, raw2, raw3);
     if (bitLen <= 32)
-        PrintAndLogEx(SUCCESS, "Wiegand: " _GREEN_("%08X") " parity (%s)", (lWiegand << (bitLen / 2)) | rWiegand, parity ? _GREEN_("ok") : _RED_("fail"));
+        PrintAndLogEx(SUCCESS, "Wiegand: " _GREEN_("%08X") " parity ( %s )", (lWiegand << (bitLen / 2)) | rWiegand, parity ? _GREEN_("ok") : _RED_("fail"));
 
     if (verbose) {
         PrintAndLogEx(INFO, "\nHow the FC translates to printed FC is unknown");
diff --git a/client/src/cmdlfti.c b/client/src/cmdlfti.c
index cd339b087..beb0d4ae4 100644
--- a/client/src/cmdlfti.c
+++ b/client/src/cmdlfti.c
@@ -264,7 +264,7 @@ int demodTI(bool verbose) {
         init_table(CRC_KERMIT);
         uint16_t calccrc = crc16_kermit(raw, sizeof(raw));
         const char *crc_str = (calccrc == (shift2 & 0xFFFF)) ? _GREEN_("ok") : _RED_("fail");
-        PrintAndLogEx(INFO, "Tag data = %08X%08X  [%04X] (%s)", shift1, shift0, calccrc, crc_str);
+        PrintAndLogEx(INFO, "Tag data = %08X%08X  [%04X] ( %s )", shift1, shift0, calccrc, crc_str);
 
         if (calccrc != (shift2 & 0xFFFF))
             PrintAndLogEx(WARNING, "Warning: CRC mismatch, calculated %04X, got %04X", calccrc, shift2 & 0xFFFF);
diff --git a/client/src/emv/emvcore.c b/client/src/emv/emvcore.c
index 324ffe097..63e0c14dc 100644
--- a/client/src/emv/emvcore.c
+++ b/client/src/emv/emvcore.c
@@ -652,7 +652,7 @@ int trSDA(struct tlvdb *tlv) {
     struct tlvdb *dac_db = emv_pki_recover_dac(issuer_pk, tlv, sda_tlv);
     if (dac_db) {
         const struct tlv *dac_tlv = tlvdb_get(dac_db, 0x9f45, NULL);
-        PrintAndLogEx(INFO, "SDA verified (%s) (Data Authentication Code: %02hhx:%02hhx)", _GREEN_("ok"), dac_tlv->value[0], dac_tlv->value[1]);
+        PrintAndLogEx(INFO, "SDA verified ( %s ) (Data Authentication Code: %02hhx:%02hhx)", _GREEN_("ok"), dac_tlv->value[0], dac_tlv->value[1]);
         tlvdb_add(tlv, dac_db);
     } else {
         emv_pk_free(issuer_pk);
@@ -771,7 +771,7 @@ int trDDA(Iso7816CommandChannel channel, bool decodeTLV, struct tlvdb *tlv) {
         struct tlvdb *dac_db = emv_pki_recover_dac(issuer_pk, tlv, sda_tlv);
         if (dac_db) {
             const struct tlv *dac_tlv = tlvdb_get(dac_db, 0x9f45, NULL);
-            PrintAndLogEx(INFO, "SDAD verified (%s) (Data Authentication Code: %02hhx:%02hhx)\n", _GREEN_("ok"), dac_tlv->value[0], dac_tlv->value[1]);
+            PrintAndLogEx(INFO, "SDAD verified ( %s ) (Data Authentication Code: %02hhx:%02hhx)\n", _GREEN_("ok"), dac_tlv->value[0], dac_tlv->value[1]);
             tlvdb_add(tlv, dac_db);
         } else {
             PrintAndLogEx(ERR, "Error: SSAD verify error");
@@ -930,7 +930,7 @@ int trCDA(struct tlvdb *tlv, struct tlvdb *ac_tlv, struct tlv *pdol_data_tlv, st
         struct tlvdb *dac_db = emv_pki_recover_dac(issuer_pk, tlv, sda_tlv);
         if (dac_db) {
             const struct tlv *dac_tlv = tlvdb_get(dac_db, 0x9f45, NULL);
-            PrintAndLogEx(SUCCESS, "Signed Static Application Data (SSAD) verified (%s) (%02hhx:%02hhx)", _GREEN_("ok"), dac_tlv->value[0], dac_tlv->value[1]);
+            PrintAndLogEx(SUCCESS, "Signed Static Application Data (SSAD) verified ( %s ) (%02hhx:%02hhx)", _GREEN_("ok"), dac_tlv->value[0], dac_tlv->value[1]);
             tlvdb_add(tlv, dac_db);
         } else {
             PrintAndLogEx(ERR, "Error: Signed Static Application Data (SSAD) verify error");
@@ -950,7 +950,7 @@ int trCDA(struct tlvdb *tlv, struct tlvdb *ac_tlv, struct tlv *pdol_data_tlv, st
     if (idn_db) {
         const struct tlv *idn_tlv = tlvdb_get(idn_db, 0x9f4c, NULL);
         PrintAndLogEx(INFO, "IDN (ICC Dynamic Number) [%zu] %s", idn_tlv->len, sprint_hex_inrow(idn_tlv->value, idn_tlv->len));
-        PrintAndLogEx(SUCCESS, "CDA verified (%s)", _GREEN_("ok"));
+        PrintAndLogEx(SUCCESS, "CDA verified ( %s )", _GREEN_("ok"));
         tlvdb_add(tlv, idn_db);
     } else {
         PrintAndLogEx(ERR, "ERROR: CDA verify error");
diff --git a/client/src/emv/emvjson.c b/client/src/emv/emvjson.c
index 072473a31..0b4b74297 100644
--- a/client/src/emv/emvjson.c
+++ b/client/src/emv/emvjson.c
@@ -331,7 +331,7 @@ bool ParamLoadFromJson(struct tlvdb *tlv) {
         return false;
     }
 
-    PrintAndLogEx(SUCCESS, "Load params: json(%zu) (%s)", json_array_size(root), _GREEN_("OK"));
+    PrintAndLogEx(SUCCESS, "Load params: json(%zu) ( %s )", json_array_size(root), _GREEN_("ok"));
 
     for (int i = 0; i < json_array_size(root); i++) {
         json_t *data, *jtag, *jlength, *jvalue;
diff --git a/client/src/flash.c b/client/src/flash.c
index 5bc6fbcab..df115b7d0 100644
--- a/client/src/flash.c
+++ b/client/src/flash.c
@@ -608,7 +608,7 @@ int flash_write(flash_file_t *ctx) {
             }
             fflush(stdout);
         }
-        PrintAndLogEx(NORMAL, " " _GREEN_("OK"));
+        PrintAndLogEx(NORMAL, " " _GREEN_("ok"));
         fflush(stdout);
     }
     return PM3_SUCCESS;
diff --git a/tools/pm3_tests.sh b/tools/pm3_tests.sh
index d0c77a465..ee1794bde 100755
--- a/tools/pm3_tests.sh
+++ b/tools/pm3_tests.sh
@@ -445,7 +445,7 @@ while true; do
                                                                      "Indala (len 224)  Raw: 80000001b23523a6c2e31eba3cbee4afb3c6ad1fcf649393928c14e5"; then break; fi
       if ! CheckExecute slow "lf T55 io test"                    "$CLIENTBIN -c 'data load -f traces/lf_ATA5577_io.pm3; lf search -1'" "IO Prox ID found"; then break; fi
       if ! CheckExecute slow "lf T55 io test2"                   "$CLIENTBIN -c 'data load -f traces/lf_ATA5577_io.pm3; lf io demod'" \
-                                                                     "IO Prox - XSF(01)01:01337, Raw: 007840603059cf3f (ok)"; then break; fi
+                                                                     "IO Prox - XSF(01)01:01337, Raw: 007840603059cf3f ( ok )"; then break; fi
       if ! CheckExecute slow "lf T55 jablotron test"             "$CLIENTBIN -c 'data load -f traces/lf_ATA5577_jablotron.pm3; lf search -1'" "Jablotron ID found"; then break; fi
       if ! CheckExecute slow "lf T55 jablotron test2"            "$CLIENTBIN -c 'data load -f traces/lf_ATA5577_jablotron.pm3; lf jablotron demod'" \
                                                                      "Printed: 1410-00-0011-2233"; then break; fi
