commit 15b8eb5935471f87d019bc558837bdd4eebf00db
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Mar 13 04:27:23 2022 +0100

    coverity fixes

diff --git a/client/src/cmddata.c b/client/src/cmddata.c
index fd48f2f1e..a5920ed5f 100644
--- a/client/src/cmddata.c
+++ b/client/src/cmddata.c
@@ -3061,6 +3061,7 @@ static int CmdDiff(const char *Cmd) {
             PrintAndLogEx(WARNING, "Fail, transfer from device time-out");
             free(inA);
             free(inB);
+            free(d);
             return PM3_ETIMEOUT;
         }
 
diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index 28f82ab31..9b8458adc 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -2643,6 +2643,7 @@ int CmdHF14ANdefRead(const char *Cmd) {
         DropField();
         return PM3_ESOFT;
     }
+
     uint16_t ndef_size = (response[0] << 8) + response[1];
     uint16_t offset = 2;
     uint8_t *ndef_file = calloc(ndef_size, sizeof(uint8_t));
@@ -2651,11 +2652,14 @@ int CmdHF14ANdefRead(const char *Cmd) {
         DropField();
         return PM3_EMALLOC;
     }
+
     if (ndef_size + offset > 0xFFFF) {
         PrintAndLogEx(ERR, "NDEF size abnormally large in CmdHF14ANdef(). Aborting...\n");
+        free(ndef_file);
         DropField();
         return PM3_EOVFLOW;
     }
+
     for (uint16_t i = offset; i < ndef_size + offset; i += max_rapdu_size) {
         uint16_t segment_size = max_rapdu_size < ndef_size + offset - i ? max_rapdu_size : ndef_size + offset - i;
         keep_field_on = i < ndef_size + offset - max_rapdu_size;
@@ -2664,12 +2668,14 @@ int CmdHF14ANdefRead(const char *Cmd) {
         aREAD_NDEF[2] = i >> 8;
         aREAD_NDEF[3] = i & 0xFF;
         aREAD_NDEF[4] = segment_size;
+
         res = ExchangeAPDU14a(aREAD_NDEF, aREAD_NDEF_n + 1, activate_field, keep_field_on, response, sizeof(response), &resplen);
         if (res != PM3_SUCCESS) {
             DropField();
             free(ndef_file);
             return res;
         }
+
         sw = get_sw(response, resplen);
         if (sw != 0x9000) {
             PrintAndLogEx(ERR, "reading NDEF file failed (%04x - %s).", sw, GetAPDUCodeDescription(sw >> 8, sw & 0xff));
@@ -2677,17 +2683,21 @@ int CmdHF14ANdefRead(const char *Cmd) {
             free(ndef_file);
             return PM3_ESOFT;
         }
+
         if (resplen != segment_size + 2) {
             PrintAndLogEx(ERR, "reading NDEF file failed, expected %i bytes, got %i bytes.", segment_size, resplen - 2);
             DropField();
             free(ndef_file);
             return PM3_ESOFT;
         }
+
         memcpy(ndef_file + (i - offset), response, segment_size);
     }
+
     if (fnlen != 0) {
         saveFile(filename, ".bin", ndef_file, ndef_size);
     }
+
     NDEFRecordsDecodeAndPrint(ndef_file, ndef_size);
     free(ndef_file);
     return PM3_SUCCESS;
