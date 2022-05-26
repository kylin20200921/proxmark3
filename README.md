commit 07855611e4f60dc26bb89cce3d2bef2c2d64a8fd
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Feb 24 18:03:19 2022 +0100

    unify text

diff --git a/armsrc/Standalone/hf_iceclass.c b/armsrc/Standalone/hf_iceclass.c
index cdaa4f220..7aa53dba5 100644
--- a/armsrc/Standalone/hf_iceclass.c
+++ b/armsrc/Standalone/hf_iceclass.c
@@ -252,7 +252,7 @@ static int reader_attack_mode(void) {
 
         bool success = (mac_response_len == MAC_RESPONSES_SIZE);
         uint8_t num_mac = (mac_response_len >> 4);
-        Dbprintf("%u out of %d MAC obtained [%s]", num_mac, NUM_CSNS, (success) ? _GREEN_("ok") : _RED_("fail"));
+        Dbprintf("%u out of %d MAC obtained ( %s )", num_mac, NUM_CSNS, (success) ? _GREEN_("ok") : _RED_("fail"));
 
         size_t dumplen = NUM_CSNS * 24;
 
diff --git a/armsrc/iso15693.c b/armsrc/iso15693.c
index aa3929d73..c08d6130f 100644
--- a/armsrc/iso15693.c
+++ b/armsrc/iso15693.c
@@ -1589,9 +1589,9 @@ static void DbdecodeIso15693Answer(int len, uint8_t *d) {
         }
 
         if (CheckCrc15(d, len))
-            strncat(status, "[+] crc (" _GREEN_("OK") ")", DBD15STATLEN - strlen(status));
+            strncat(status, "[+] crc ( " _GREEN_("ok") " )", DBD15STATLEN - strlen(status));
         else
-            strncat(status, "[!] crc (" _RED_("fail") ")", DBD15STATLEN - strlen(status));
+            strncat(status, "[!] crc ( " _RED_("fail") " )", DBD15STATLEN - strlen(status));
 
         if (g_dbglevel >= DBG_ERROR) Dbprintf("%s", status);
     }
diff --git a/client/src/cmdhf14b.c b/client/src/cmdhf14b.c
index 28f4ec9ee..baea0126a 100644
--- a/client/src/cmdhf14b.c
+++ b/client/src/cmdhf14b.c
@@ -191,7 +191,7 @@ static bool wait_cmd_14b(bool verbose, bool is_select, uint32_t timeout) {
             bool crc = check_crc(CRC_14443_B, data, len);
 
             PrintAndLogEx(SUCCESS, "received " _YELLOW_("%u") " bytes", len);
-            PrintAndLogEx(SUCCESS, "%s[%02X %02X] %s",
+            PrintAndLogEx(SUCCESS, "%s[%02X %02X] ( %s )",
                           sprint_hex(data, len - 2),
                           data[len - 2],
                           data[len - 1],
diff --git a/client/src/cmdhfcipurse.c b/client/src/cmdhfcipurse.c
index 4e91d5d69..3c2791cb9 100644
--- a/client/src/cmdhfcipurse.c
+++ b/client/src/cmdhfcipurse.c
@@ -369,7 +369,7 @@ static int SelectCommandEx(bool selectDefaultFile, bool useAID, uint8_t *aid, si
             return PM3_ESOFT;
         }
         if (verbose) {
-            PrintAndLogEx(INFO, "Cipurse select application " _CYAN_("%s ") _GREEN_("OK"), sprint_hex_inrow(aid, aidLen));
+            PrintAndLogEx(INFO, "Cipurse select application " _YELLOW_("%s ") " ( %s )", sprint_hex_inrow(aid, aidLen), _GREEN_("ok"));
         }
 
     } else if (useFID) {
@@ -377,7 +377,7 @@ static int SelectCommandEx(bool selectDefaultFile, bool useAID, uint8_t *aid, si
         res = CIPURSESelectFileEx(true, true, fileId, buf, bufSize, len, sw);
         if (res != 0 || *sw != 0x9000) {
             if (verbose) {
-                PrintAndLogEx(ERR, "Cipurse select file 0x%04x " _RED_("error"));
+                PrintAndLogEx(ERR, "Cipurse select file 0x%04x  ( %s )",  _RED_("fail"));
                  PrintAndLogEx(ERR, "Card returns 0x%04x", fileId, *sw);
             }
             return PM3_ESOFT;
@@ -396,7 +396,7 @@ static int SelectCommandEx(bool selectDefaultFile, bool useAID, uint8_t *aid, si
             return PM3_ESOFT;
         }
         if (verbose) {
-            PrintAndLogEx(INFO, "Cipurse select default file " _GREEN_("OK"));
+            PrintAndLogEx(INFO, "Cipurse select default file  ( " _GREEN_("ok") " )");
         }
 
     } else {
@@ -409,7 +409,7 @@ static int SelectCommandEx(bool selectDefaultFile, bool useAID, uint8_t *aid, si
             return PM3_ESOFT;
         }
         if (verbose) {
-            PrintAndLogEx(INFO, "Cipurse select default application " _GREEN_("OK"));
+            PrintAndLogEx(INFO, "Cipurse select default application ( " _GREEN_("ok") " )");
         }
     }
 
@@ -426,7 +426,7 @@ static int SelectCommandEx(bool selectDefaultFile, bool useAID, uint8_t *aid, si
             return PM3_ESOFT;
         }
         if (verbose) {
-            PrintAndLogEx(INFO, "Select child file " _CYAN_("0x%04x ") _GREEN_("OK"), childFileId);
+            PrintAndLogEx(INFO, "Select child file " _CYAN_("0x%04x ") " ( " _GREEN_("ok") " )", childFileId);
         }
     }
 
@@ -645,7 +645,7 @@ static int CmdHFCipurseReadFile(const char *Cmd) {
     }
 
     if (verbose) {
-        PrintAndLogEx(INFO, "Cipurse select application " _CYAN_("%s") " ( " _GREEN_("ok") " )", sprint_hex_inrow(aid, aidLen));
+        PrintAndLogEx(INFO, "Cipurse select application " _CYAN_("%s") " ( %s )", sprint_hex_inrow(aid, aidLen), _GREEN_("ok"));
         PrintAndLogEx(INFO, "File id " _YELLOW_("%x") " offset " _YELLOW_("%zu") " key id " _YELLOW_("%d") " key " _YELLOW_("%s"), fileId, offset, keyId, sprint_hex(key, CIPURSE_AES_KEY_LENGTH));
     }
 
@@ -671,7 +671,7 @@ static int CmdHFCipurseReadFile(const char *Cmd) {
     }
 
     if (verbose)
-        PrintAndLogEx(INFO, "Select file 0x%x ( " _GREEN_("ok") " )", fileId);
+        PrintAndLogEx(INFO, "Select file 0x%x ( %s )", fileId, _GREEN_("ok"));
 
     res = CIPURSEReadBinary(offset, buf, sizeof(buf), &len, &sw);
     if (res != 0 || sw != 0x9000) {
@@ -768,7 +768,7 @@ static int CmdHFCipurseWriteFile(const char *Cmd) {
     }
 
     if (verbose) {
-        PrintAndLogEx(INFO, "Cipurse select application " _CYAN_("%s") " ( " _GREEN_("ok") " )", sprint_hex_inrow(aid, aidLen));
+        PrintAndLogEx(INFO, "Cipurse select application " _CYAN_("%s") " ( %s )", sprint_hex_inrow(aid, aidLen), _GREEN_("ok"));
         PrintAndLogEx(INFO, "File id " _YELLOW_("%x") " offset " _YELLOW_("%zu") " key id " _YELLOW_("%d") " key " _YELLOW_("%s")
                       , fileId
                       , offset
@@ -800,7 +800,7 @@ static int CmdHFCipurseWriteFile(const char *Cmd) {
     }
 
     if (verbose)
-        PrintAndLogEx(INFO, "Select file 0x%x ( " _GREEN_("ok") " )", fileId);
+        PrintAndLogEx(INFO, "Select file 0x%x ( %s )", fileId, _GREEN_("ok"));
 
     res = CIPURSEUpdateBinary(offset, hdata, hdatalen, buf, sizeof(buf), &len, &sw);
     if (res != 0 || sw != 0x9000) {
@@ -816,7 +816,7 @@ static int CmdHFCipurseWriteFile(const char *Cmd) {
         sw = 0;
         res = CIPURSECommitTransaction(&sw);
         if (res != 0 || sw != 0x9000)
-            PrintAndLogEx(WARNING, "Commit " _YELLOW_("ERROR") ". Card returns 0x%04x", sw);
+            PrintAndLogEx(WARNING, "Commit ( " _YELLOW_("fail") " ) Card returns 0x%04x", sw);
 
         if (verbose)
             PrintAndLogEx(INFO, "Commit ( " _GREEN_("ok") " )");
@@ -1074,7 +1074,7 @@ static int CmdHFCipurseWriteFileAttr(const char *Cmd) {
         sw = 0;
         res = CIPURSECommitTransaction(&sw);
         if (res != 0 || sw != 0x9000)
-            PrintAndLogEx(WARNING, "Commit " _YELLOW_("ERROR") ". Card returns 0x%04x", sw);
+            PrintAndLogEx(WARNING, "Commit ( " _YELLOW_("fail") " ) Card returns 0x%04x", sw);
 
         if (verbose)
             PrintAndLogEx(INFO, "Commit ( " _GREEN_("ok") " )");
@@ -1293,10 +1293,10 @@ static int CmdHFCipurseCreateDGI(const char *Cmd) {
         sw = 0;
         res = CIPURSECommitTransaction(&sw);
         if (res != 0 || sw != 0x9000)
-            PrintAndLogEx(WARNING, "Commit " _YELLOW_("ERROR") ". Card returns 0x%04x", sw);
+            PrintAndLogEx(WARNING, "Commit ( " _YELLOW_("fail") " ) Card returns 0x%04x", sw);
 
         if (verbose)
-            PrintAndLogEx(INFO, "Commit " _GREEN_("OK"));
+            PrintAndLogEx(INFO, "Commit ( " _GREEN_("ok") " )");
     }
 
     DropField();
@@ -1437,17 +1437,17 @@ static int CmdHFCipurseDeleteFile(const char *Cmd) {
             DropField();
             return PM3_ESOFT;
         }
-        PrintAndLogEx(INFO, "Delete application " _CYAN_("%s ") _GREEN_("OK"), sprint_hex_inrow(aid, aidLen));
+        PrintAndLogEx(INFO, "Delete application " _CYAN_("%s") " ( %s )",sprint_hex_inrow(aid, aidLen),  _GREEN_("ok"));
     }
 
     if (needCommit) {
         sw = 0;
         res = CIPURSECommitTransaction(&sw);
         if (res != 0 || sw != 0x9000)
-            PrintAndLogEx(WARNING, "Commit " _YELLOW_("ERROR") ". Card returns 0x%04x", sw);
+            PrintAndLogEx(WARNING, "Commit ( " _YELLOW_("fail") " ) Card returns 0x%04x", sw);
 
         if (verbose)
-            PrintAndLogEx(INFO, "Commit " _GREEN_("OK"));
+            PrintAndLogEx(INFO, "Commit ( " _GREEN_("ok") " )");
     }
 
     DropField();
@@ -1638,10 +1638,10 @@ static int CmdHFCipurseUpdateKey(const char *Cmd) {
         sw = 0;
         res = CIPURSECommitTransaction(&sw);
         if (res != 0 || sw != 0x9000)
-            PrintAndLogEx(WARNING, "Commit " _YELLOW_("ERROR") ". Card returns 0x%04x", sw);
+            PrintAndLogEx(WARNING, "Commit ( " _YELLOW_("fail") " ) Card returns 0x%04x", sw);
 
         if (verbose)
-            PrintAndLogEx(INFO, "Commit " _GREEN_("OK"));
+            PrintAndLogEx(INFO, "Commit ( " _GREEN_("ok") " )");
     }
 
     DropField();
@@ -1786,10 +1786,10 @@ static int CmdHFCipurseUpdateKeyAttr(const char *Cmd) {
         sw = 0;
         res = CIPURSECommitTransaction(&sw);
         if (res != 0 || sw != 0x9000)
-            PrintAndLogEx(WARNING, "Commit " _YELLOW_("ERROR") ". Card returns 0x%04x", sw);
+            PrintAndLogEx(WARNING, "Commit ( " _YELLOW_("fail") " ) Card returns 0x%04x", sw);
 
         if (verbose)
-            PrintAndLogEx(INFO, "Commit " _GREEN_("OK"));
+            PrintAndLogEx(INFO, "Commit ( " _GREEN_("ok") " )");
     }
 
     DropField();
diff --git a/client/src/cmdhflegic.c b/client/src/cmdhflegic.c
index 9763292b2..da3f34a06 100644
--- a/client/src/cmdhflegic.c
+++ b/client/src/cmdhflegic.c
@@ -110,11 +110,11 @@ static int CmdLegicInfo(const char *Cmd) {
 
     PrintAndLogEx(SUCCESS, " " _CYAN_("CDF: System Area"));
     PrintAndLogEx(NORMAL, "------------------------------------------------------");
-    PrintAndLogEx(SUCCESS, "MCD: " _GREEN_("%02X") " MSN: " _GREEN_("%s") " MCC: " _GREEN_("%02X") " (%s)",
+    PrintAndLogEx(SUCCESS, "MCD: " _GREEN_("%02X") " MSN: " _GREEN_("%s") " MCC: " _GREEN_("%02X") " ( %s )",
                   data[0],
                   sprint_hex(data + 1, 3),
                   data[4],
-                  (calc_crc == crc) ? _GREEN_("OK") : _RED_("Fail")
+                  (calc_crc == crc) ? _GREEN_("ok") : _RED_("fail")
                  );
 
     // MCD = Manufacturer ID (should be list meaning something?)
@@ -265,12 +265,12 @@ static int CmdLegicInfo(const char *Cmd) {
                           (segment_flag & 0x4) >> 2,
                           (segment_flag & 0x8) >> 3
                          );
-            PrintAndLogEx(SUCCESS, "            | WRP: %02u, WRC: %02u, RD: %01u, CRC: 0x%02X (%s)",
+            PrintAndLogEx(SUCCESS, "            | WRP: %02u, WRC: %02u, RD: %01u, CRC: 0x%02X ( %s )",
                           wrp,
                           wrc,
                           ((data[i + 3] ^ crc) & 0x80) >> 7,
                           segCRC,
-                          (segCRC == segCalcCRC) ? _GREEN_("OK") : _RED_("Fail")
+                          (segCRC == segCalcCRC) ? _GREEN_("ok") : _RED_("fail")
                          );
 
             i += 5;
diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index b8275f4b2..aebdd7ef5 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -5281,7 +5281,7 @@ static int CmdHF14AMfMAD(const char *Cmd) {
         PrintAndLogEx(WARNING, "error, read sector 0. card doesn't have MAD or doesn't have MAD on default keys");
         got_first = false;
     } else {
-        PrintAndLogEx(INFO, "Authentication ( " _GREEN_("OK") " )");
+        PrintAndLogEx(INFO, "Authentication ( " _GREEN_("ok") " )");
     }
 
     // User supplied key
@@ -5290,7 +5290,7 @@ static int CmdHF14AMfMAD(const char *Cmd) {
         if (mfReadSector(MF_MAD1_SECTOR, MF_KEY_A, userkey, sector0) != PM3_SUCCESS) {
             PrintAndLogEx(ERR, "error, read sector 0. card doesn't have MAD or the custom key is wrong");
         } else {
-            PrintAndLogEx(INFO, "Authentication ( " _GREEN_("OK") " )");
+            PrintAndLogEx(INFO, "Authentication ( " _GREEN_("ok") " )");
             got_first = true;
         }
     }
@@ -5765,7 +5765,7 @@ static int CmdHf14AMfSuperCard(const char *Cmd) {
             DropField();
             return res;
         }
-        PrintAndLogEx(SUCCESS, "Super card reset [ " _GREEN_("ok") " ]");
+        PrintAndLogEx(SUCCESS, "Super card reset ( " _GREEN_("ok") " )");
         return PM3_SUCCESS;
     }
 
diff --git a/client/src/cmdhfmfdes.c b/client/src/cmdhfmfdes.c
index b589fa26c..d7f6b62e2 100644
--- a/client/src/cmdhfmfdes.c
+++ b/client/src/cmdhfmfdes.c
@@ -2188,15 +2188,15 @@ static int CmdHF14ADesSetConfiguration(const char *Cmd) {
     res = DesfireSelectAndAuthenticateAppW(&dctx, securechann, selectway, id, false, verbose);
     if (res != PM3_SUCCESS) {
         DropField();
-        PrintAndLogEx(FAILED, "Select or authentication %s " _RED_("failed") ". Result [%d] %s", DesfireWayIDStr(selectway, id), res, DesfireAuthErrorToStr(res));
+        PrintAndLogEx(FAILED, "Select or authentication ( %s )" _RED_("failed") " Result [%d] %s", DesfireWayIDStr(selectway, id), res, DesfireAuthErrorToStr(res));
         return res;
     }
 
     res = DesfireSetConfiguration(&dctx, paramid, param, paramlen);
     if (res == PM3_SUCCESS) {
-        PrintAndLogEx(SUCCESS, "Set configuration 0x%02x " _GREEN_("ok") " ", paramid);
+        PrintAndLogEx(SUCCESS, "Set configuration 0x%02x ( %s )", _GREEN_("ok"), paramid);
     } else {
-        PrintAndLogEx(FAILED, "Set configuration 0x%02x " _RED_("failed") " ", paramid);
+        PrintAndLogEx(FAILED, "Set configuration 0x%02x ( %s )", _RED_("failed"), paramid);
     }
 
     DropField();
@@ -2332,9 +2332,9 @@ static int CmdHF14ADesChangeKey(const char *Cmd) {
     DesfireSetCommMode(&dctx, DCMEncryptedPlain);
     res = DesfireChangeKey(&dctx, (DesfireMFSelected(selectway, id)) && (newkeynum == 0) && (dctx.keyNum == 0), newkeynum, newkeytype, newkeyver, newkey, oldkeytype, oldkey, true);
     if (res == PM3_SUCCESS) {
-        PrintAndLogEx(SUCCESS, "Change key " _GREEN_("ok") " ");
+        PrintAndLogEx(SUCCESS, "Change key ( " _GREEN_("ok") " )");
     } else {
-        PrintAndLogEx(FAILED, "Change key " _RED_("failed") " ");
+        PrintAndLogEx(FAILED, "Change key ( " _RED_("failed") " )");
     }
     DesfireSetCommMode(&dctx, DCMEncrypted);
 
@@ -4304,7 +4304,7 @@ static int CmdHF14ADesValueOperations(const char *Cmd) {
     res = DesfireSelectAndAuthenticateAppW(&dctx, securechann, selectway, id, noauth, verbose);
     if (res != PM3_SUCCESS) {
         DropField();
-        PrintAndLogEx(FAILED, "Select or authentication %s " _RED_("failed") ". Result [%d] %s", DesfireWayIDStr(selectway, id), res, DesfireAuthErrorToStr(res));
+        PrintAndLogEx(FAILED, "Select or authentication ( %s )" _RED_("failed") " Result [%d] %s", DesfireWayIDStr(selectway, id), res, DesfireAuthErrorToStr(res));
         return res;
     }
 
@@ -4314,12 +4314,12 @@ static int CmdHF14ADesValueOperations(const char *Cmd) {
     if (op != 0xff) {
         res = DesfireValueFileOperations(&dctx, fileid, op, &value);
         if (res != PM3_SUCCESS) {
-            PrintAndLogEx(ERR, "Desfire ValueFileOperations (0x%02x) command " _RED_("error") ". Result: %d", op, res);
+            PrintAndLogEx(ERR, "Desfire ValueFileOperations (0x%02x) command ( " _RED_("error") " ) Result: %d", op, res);
             DropField();
             return PM3_ESOFT;
         }
         if (verbose)
-            PrintAndLogEx(INFO, "Operation %s " _GREEN_("OK"), CLIGetOptionListStr(DesfireValueFileOperOpts, op));
+            PrintAndLogEx(INFO, "Operation ( %s )" _GREEN_("ok"), CLIGetOptionListStr(DesfireValueFileOperOpts, op));
 
         if (op == MFDES_GET_VALUE) {
             PrintAndLogEx(SUCCESS, "Value: " _GREEN_("%d (0x%08x)"), value, value);
@@ -4327,19 +4327,19 @@ static int CmdHF14ADesValueOperations(const char *Cmd) {
             DesfireSetCommMode(&dctx, DCMMACed);
             res = DesfireCommitTransaction(&dctx, false, 0);
             if (res != PM3_SUCCESS) {
-                PrintAndLogEx(ERR, "Desfire CommitTransaction command " _RED_("error") ". Result: %d", res);
+                PrintAndLogEx(ERR, "Desfire CommitTransaction command ( " _RED_("error") ") Result: %d", res);
                 DropField();
                 return PM3_ESOFT;
             }
             if (verbose)
-                PrintAndLogEx(INFO, "Commit " _GREEN_("OK"));
+                PrintAndLogEx(INFO, "Commit ( " _GREEN_("ok") " )");
 
             PrintAndLogEx(SUCCESS, "Value changed " _GREEN_("successfully"));
         }
     } else {
         res = DesfireValueFileOperations(&dctx, fileid, MFDES_GET_VALUE, &value);
         if (res != PM3_SUCCESS) {
-            PrintAndLogEx(ERR, "Desfire GetValue command " _RED_("error") ". Result: %d", res);
+            PrintAndLogEx(ERR, "Desfire GetValue command ( " _RED_("error") ") Result: %d", res);
             DropField();
             return PM3_ESOFT;
         }
@@ -4351,7 +4351,7 @@ static int CmdHF14ADesValueOperations(const char *Cmd) {
 
         res = DesfireGetFileSettings(&dctx, fileid, buf, &buflen);
         if (res != PM3_SUCCESS) {
-            PrintAndLogEx(ERR, "Desfire GetFileSettings command " _RED_("error") ". Result: %d", res);
+            PrintAndLogEx(ERR, "Desfire GetFileSettings command ( " _RED_("error") " ) Result: %d", res);
             DropField();
             return PM3_ESOFT;
         }
@@ -4375,7 +4375,7 @@ static int CmdHF14ADesValueOperations(const char *Cmd) {
         if (delta > 0) {
             res = DesfireValueFileOperations(&dctx, fileid, MFDES_DEBIT, &delta);
             if (res != PM3_SUCCESS) {
-                PrintAndLogEx(ERR, "Desfire Debit operation " _RED_("error") ". Result: %d", res);
+                PrintAndLogEx(ERR, "Desfire Debit operation ( " _RED_("error") " ) Result: %d", res);
                 DropField();
                 return PM3_ESOFT;
             }
@@ -4386,7 +4386,7 @@ static int CmdHF14ADesValueOperations(const char *Cmd) {
             DesfireSetCommMode(&dctx, DCMMACed);
             res = DesfireCommitTransaction(&dctx, false, 0);
             if (res != PM3_SUCCESS) {
-                PrintAndLogEx(ERR, "Desfire CommitTransaction command " _RED_("error") ". Result: %d", res);
+                PrintAndLogEx(ERR, "Desfire CommitTransaction command ( " _RED_("error") " ) Result: %d", res);
                 DropField();
                 return PM3_ESOFT;
             }
@@ -5200,7 +5200,7 @@ static int CmdHF14ADesWriteData(const char *Cmd) {
 
             readeridpushed = true;
             if (verbose)
-                PrintAndLogEx(INFO, "CommitReaderID " _GREEN_("OK"));
+                PrintAndLogEx(INFO, "CommitReaderID ( " _GREEN_("ok") " )");
         } else
             PrintAndLogEx(WARNING, "Desfire CommitReaderID command " _RED_("error") ". Result: %d", res);
     }
@@ -5287,7 +5287,7 @@ static int CmdHF14ADesWriteData(const char *Cmd) {
         if (verbose) {
             if (readeridpushed)
                 PrintAndLogEx(INFO, "TMC and TMV[%zu]: %s", resplen, sprint_hex(resp, resplen));
-            PrintAndLogEx(INFO, "Commit " _GREEN_("OK"));
+            PrintAndLogEx(INFO, "Commit ( " _GREEN_("ok") " )");
         }
 
         if (resplen == 4 + 8) {
diff --git a/client/src/cmdhfmfu.c b/client/src/cmdhfmfu.c
index d67840ac9..2821ba42d 100644
--- a/client/src/cmdhfmfu.c
+++ b/client/src/cmdhfmfu.c
@@ -3815,7 +3815,7 @@ static int CmdHF14AMfuEv1CounterTearoff(const char *Cmd) {
                     , poststr
                     , pre_tear
                     , post_tear
-                    , post_tear_check ? _GREEN_("OK") : _RED_("DETECTED")
+                    , post_tear_check ? _GREEN_("ok") : _RED_("DETECTED")
                 );
                 break;
             }
@@ -3826,7 +3826,7 @@ static int CmdHF14AMfuEv1CounterTearoff(const char *Cmd) {
                     , poststr
                     , pre_tear
                     , post_tear
-                    , post_tear_check ? _GREEN_("OK") : _RED_("DETECTED")
+                    , post_tear_check ? _GREEN_("ok") : _RED_("DETECTED")
                 );
 
 
@@ -3860,7 +3860,7 @@ static int CmdHF14AMfuEv1CounterTearoff(const char *Cmd) {
                     , poststr
                     , pre_tear
                     , post_tear
-                    , post_tear_check ? _GREEN_("OK") : _RED_("DETECTED")
+                    , post_tear_check ? _GREEN_("ok") : _RED_("DETECTED")
                 );
 
                 if ( post_tear_check  && b == initial_value) {
@@ -3908,7 +3908,7 @@ static int CmdHF14AMfuEv1CounterTearoff(const char *Cmd) {
                 , poststr
                 , pre_tear
                 , post_tear
-                , post_tear_check ? _GREEN_("OK") : _RED_("DETECTED")
+                , post_tear_check ? _GREEN_("ok") : _RED_("DETECTED")
             );
 
             if ( post_tear_check ) {
diff --git a/client/src/cmdhfthinfilm.c b/client/src/cmdhfthinfilm.c
index 89520d8e3..92c82f3f3 100644
--- a/client/src/cmdhfthinfilm.c
+++ b/client/src/cmdhfthinfilm.c
@@ -47,11 +47,11 @@ static int print_barcode(uint8_t *barcode, const size_t barcode_len, bool verbos
             compute_crc(CRC_14443_A, barcode, barcode_len - 2, &b1, &b2);
             bool isok = (barcode[barcode_len - 1] == b1 && barcode[barcode_len - 2] == b2);
 
-            PrintAndLogEx(SUCCESS, "        Checksum : "_YELLOW_("%02X %02X")" - %s", b2, b1, (isok) ? _GREEN_("OK") : _RED_("fail"));
+            PrintAndLogEx(SUCCESS, "        Checksum : "_YELLOW_("%02X %02X")" ( %s )", b2, b1, (isok) ? _GREEN_("ok") : _RED_("fail"));
         } else {
             PrintAndLogEx(SUCCESS, "        Checksum : "_YELLOW_("too few data for checksum")" - " _RED_("fail"));
         }
-        PrintAndLogEx(SUCCESS, " Data len (bits) : "_YELLOW_("%zu")" - %s", barcode_len * 8, (barcode_len == 16 || barcode_len == 32) ? _GREEN_("OK") : _YELLOW_("warning"));
+        PrintAndLogEx(SUCCESS, " Data len (bits) : "_YELLOW_("%zu")" ( %s )", barcode_len * 8, (barcode_len == 16 || barcode_len == 32) ? _GREEN_("ok") : _YELLOW_("warning"));
         PrintAndLogEx(SUCCESS, "        Raw data : "_YELLOW_("%s"), sprint_hex(barcode, barcode_len));
         if (barcode_len < 4) // too few to go to next decoding stages
             return PM3_ESOFT;
diff --git a/client/src/cmdhw.c b/client/src/cmdhw.c
index b228a5c91..c6b763c08 100644
--- a/client/src/cmdhw.c
+++ b/client/src/cmdhw.c
@@ -804,7 +804,7 @@ static int CmdPing(const char *Cmd) {
     if (WaitForResponseTimeout(CMD_PING, &resp, 1000)) {
         if (len) {
             bool error = (memcmp(data, resp.data.asBytes, len) != 0);
-            PrintAndLogEx((error) ? ERR : SUCCESS, "Ping response " _GREEN_("received") " and content is %s", error ? _RED_("NOT ok") : _GREEN_("OK"));
+            PrintAndLogEx((error) ? ERR : SUCCESS, "Ping response " _GREEN_("received") " and content () %s )", error ? _RED_("fail") : _GREEN_("ok"));
         } else {
             PrintAndLogEx(SUCCESS, "Ping response " _GREEN_("received"));
         }
