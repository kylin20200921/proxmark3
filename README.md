commit a0d6a82752ccc2beba82195230d06524d2f75da9
Author: iceman1001 <iceman@iuse.se>
Date:   Wed Jan 26 06:15:43 2022 +0100

    added --raw to clone/sim

diff --git a/client/src/cmdlfpyramid.c b/client/src/cmdlfpyramid.c
index cb9477251..bd986d68c 100644
--- a/client/src/cmdlfpyramid.c
+++ b/client/src/cmdlfpyramid.c
@@ -233,27 +233,39 @@ static int CmdPyramidClone(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "lf pyramid clone",
                   "clone a Farpointe/Pyramid tag to a T55x7, Q5/T5555 or EM4305/4469 tag.\n"
-                  "The facility-code is 8-bit and the card number is 16-bit.  Larger values are truncated.\n"
+                  "The facility-code is 8-bit and the card number is 16-bit. Larger values are truncated.\n"
                   "Currently only works on 26bit",
                   "lf pyramid clone --fc 123 --cn 11223\n"
+                  "lf pyramid clone --raw 0001010101010101010440013223921c\n"                  
                   "lf pyramid clone --fc 123 --cn 11223 --q5  -> encode for Q5/T5555 tag\n"
-                  "lf pyramid clone --fc 123 --cn 11223 --em  -> encode for EM4305/4469"
+                  "lf pyramid clone --fc 123 --cn 11223 --em  -> encode for EM4305/4469\n"
                  );
 
     void *argtable[] = {
         arg_param_begin,
-        arg_u64_1(NULL, "fc", "<dec>", "8-bit value facility code"),
-        arg_u64_1(NULL, "cn", "<dec>", "16-bit value card number"),
+        arg_u64_0(NULL, "fc", "<dec>", "8-bit value facility code"),
+        arg_u64_0(NULL, "cn", "<dec>", "16-bit value card number"),
         arg_lit0(NULL, "q5", "optional - specify writing to Q5/T5555 tag"),
         arg_lit0(NULL, "em", "optional - specify writing to EM4305/4469 tag"),
+        arg_str0("r", "raw", "<hex>", "raw hex data. 16 bytes"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
 
-    uint32_t fc = arg_get_u32_def(ctx, 1, 0);
-    uint32_t cn = arg_get_u32_def(ctx, 2, 0);
+    uint32_t fc = arg_get_u32_def(ctx, 1, -1);
+    uint32_t cn = arg_get_u32_def(ctx, 2, -1);
     bool q5 = arg_get_lit(ctx, 3);
     bool em = arg_get_lit(ctx, 4);
+
+    int raw_len = 0;
+    // skip first block,  4*4 = 16 bytes left
+    uint8_t raw[16] = {0};
+    int res = CLIParamHexToBuf(arg_get_str(ctx, 5), raw, sizeof raw, &raw_len);
+    if (res) {
+        CLIParserFree(ctx);
+        return PM3_EINVARG;
+    }
+
     CLIParserFree(ctx);
 
     if (q5 && em) {
@@ -261,18 +273,47 @@ static int CmdPyramidClone(const char *Cmd) {
         return PM3_EINVARG;
     }
 
-    uint32_t blocks[5];
-    uint8_t *bs = calloc(128, sizeof(uint8_t));
-    if (bs == NULL) {
-        return PM3_EMALLOC;
+    bool use_raw = (raw_len > 0);
+
+    if (fc == -1 && cn == -1) {
+        if (use_raw == false) {
+            PrintAndLogEx(FAILED, "Must specify either raw data to clone, or fc/cn");
+            return PM3_EINVARG;
+        }
+    } else {
+        // --raw and --fc/cn are mutually exclusive
+        if (use_raw) {
+            PrintAndLogEx(FAILED, "Can't specify both raw and fc/cn at the same time");
+            return PM3_EINVARG;
+        }  
     }
 
-    uint32_t facilitycode = (fc & 0x000000FF);
-    uint32_t cardnumber = (cn & 0x0000FFFF);
+    uint32_t blocks[5];
+    if (use_raw) {
+        for (uint8_t i = 1; i < ARRAYLEN(blocks); i++) {
+            blocks[i] = bytes_to_num(raw + ((i - 1) * 4), sizeof(uint32_t));
+        }
+    } else {
 
-    if (getPyramidBits(facilitycode, cardnumber, bs) != PM3_SUCCESS) {
-        PrintAndLogEx(ERR, "Error with tag bitstream generation.");
-        return PM3_ESOFT;
+        uint8_t *bs = calloc(128, sizeof(uint8_t));
+        if (bs == NULL) {
+            return PM3_EMALLOC;
+        }
+
+        uint32_t facilitycode = (fc & 0x000000FF);
+        uint32_t cardnumber = (cn & 0x0000FFFF);
+
+        if (getPyramidBits(facilitycode, cardnumber, bs) != PM3_SUCCESS) {
+            PrintAndLogEx(ERR, "Error with tag bitstream generation.");
+            return PM3_ESOFT;
+        }
+
+        blocks[1] = bytebits_to_byte(bs, 32);
+        blocks[2] = bytebits_to_byte(bs + 32, 32);
+        blocks[3] = bytebits_to_byte(bs + 64, 32);
+        blocks[4] = bytebits_to_byte(bs + 96, 32);
+
+        free(bs);
     }
 
     //Pyramid - compat mode, FSK2a, data rate 50, 4 data blocks
@@ -290,17 +331,12 @@ static int CmdPyramidClone(const char *Cmd) {
         snprintf(cardtype, sizeof(cardtype), "EM4305/4469");
     }
 
-    blocks[1] = bytebits_to_byte(bs, 32);
-    blocks[2] = bytebits_to_byte(bs + 32, 32);
-    blocks[3] = bytebits_to_byte(bs + 64, 32);
-    blocks[4] = bytebits_to_byte(bs + 96, 32);
-
-    free(bs);
-
-    PrintAndLogEx(INFO, "Preparing to clone Farpointe/Pyramid to " _YELLOW_("%s") "  with Facility Code: %u, Card Number: %u", cardtype, facilitycode, cardnumber);
+    PrintAndLogEx(INFO, "Preparing to clone Farpointe/Pyramid to " _YELLOW_("%s") " from %s.",
+                cardtype,
+                use_raw ? "raw hex" : "specified data"
+                );
     print_blocks(blocks,  ARRAYLEN(blocks));
 
-    int res;
     if (em) {
         res = em4x05_clone_tag(blocks, ARRAYLEN(blocks), 0, false);
     } else {
@@ -316,35 +352,68 @@ static int CmdPyramidSim(const char *Cmd) {
     CLIParserInit(&ctx, "lf pyramid sim",
                   "Enables simulation of Farpointe/Pyramid card with specified card number.\n"
                   "Simulation runs until the button is pressed or another USB command is issued.\n"
-                  "The facility-code is 8-bit and the card number is 16-bit.  Larger values are truncated.\n"
+                  "The facility-code is 8-bit and the card number is 16-bit. Larger values are truncated.\n"
                   "Currently work only on 26bit",
-                  "lf pyramid sim --fc 123 --cn 1337"
+                  "lf pyramid sim --fc 123 --cn 1337\n"
+                  "lf pyramid clone --raw 0001010101010101010440013223921c"
                  );
 
     void *argtable[] = {
         arg_param_begin,
-        arg_u64_1(NULL, "fc", "<dec>", "8-bit value facility code"),
-        arg_u64_1(NULL, "cn", "<dec>", "16-bit value card number"),
+        arg_u64_0(NULL, "fc", "<dec>", "8-bit value facility code"),
+        arg_u64_0(NULL, "cn", "<dec>", "16-bit value card number"),
+        arg_str0("r", "raw", "<hex>", "raw hex data. 16 bytes"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
-    uint32_t fc = arg_get_u32_def(ctx, 1, 0);
-    uint32_t cn = arg_get_u32_def(ctx, 2, 0);
+    uint32_t fc = arg_get_u32_def(ctx, 1, -1);
+    uint32_t cn = arg_get_u32_def(ctx, 2, -1);
+
+    int raw_len = 0;
+    // skip first block,  4*4 = 16 bytes left
+    uint8_t raw[16] = {0};
+    int res = CLIParamHexToBuf(arg_get_str(ctx, 5), raw, sizeof raw, &raw_len);
+    if (res) {
+        CLIParserFree(ctx);
+        return PM3_EINVARG;
+    }
+
     CLIParserFree(ctx);
 
-    uint32_t facilitycode = (fc & 0x000000FF);
-    uint32_t cardnumber = (cn & 0x0000FFFF);
+    bool use_raw = (raw_len > 0);
 
+    if (fc == -1 && cn == -1) {
+        if (use_raw == false) {
+            PrintAndLogEx(FAILED, "Must specify either raw data to clone, or fc/cn");
+            return PM3_EINVARG;
+        }
+    } else {
+        if (use_raw) {
+            // --raw and --fc/cn are mutually exclusive
+            if (use_raw) {
+                PrintAndLogEx(FAILED, "Can't specify both raw and fc/cn at the same time");
+                return PM3_EINVARG;
+            }  
+        }
+    }
 
-    uint8_t bs[128];
+    uint8_t bs[sizeof(raw) * 8];
     memset(bs, 0x00, sizeof(bs));
-    if (getPyramidBits(facilitycode, cardnumber, bs) != PM3_SUCCESS) {
-        PrintAndLogEx(ERR, "Error with tag bitstream generation.");
-        return PM3_ESOFT;
+    
+    if (use_raw == false) {
+        uint32_t facilitycode = (fc & 0x000000FF);
+        uint32_t cardnumber = (cn & 0x0000FFFF);
+
+        if (getPyramidBits(facilitycode, cardnumber, bs) != PM3_SUCCESS) {
+            PrintAndLogEx(ERR, "Error with tag bitstream generation.");
+            return PM3_ESOFT;
+        }
+        PrintAndLogEx(SUCCESS, "Simulating Farpointe/Pyramid - Facility Code: " _YELLOW_("%u") ", CardNumber: " _YELLOW_("%u"), facilitycode, cardnumber);
+    } else {
+        PrintAndLogEx(SUCCESS, "Simulating Farpointe/Pyramid - raw " _YELLOW_("%s"), sprint_hex_inrow(raw, sizeof(raw)));
+        bytes_to_bytebits(raw, sizeof(raw), bs);
     }
 
-    PrintAndLogEx(SUCCESS, "Simulating Farpointe/Pyramid - Facility Code: " _YELLOW_("%u") ", CardNumber: " _YELLOW_("%u"), facilitycode, cardnumber);
-
     // Pyramid uses:  fcHigh: 10, fcLow: 8, clk: 50, invert: 0
     lf_fsksim_t *payload = calloc(1, sizeof(lf_fsksim_t) + sizeof(bs));
     payload->fchigh = 10;
@@ -357,13 +426,7 @@ static int CmdPyramidSim(const char *Cmd) {
     SendCommandNG(CMD_LF_FSK_SIMULATE, (uint8_t *)payload,  sizeof(lf_fsksim_t) + sizeof(bs));
     free(payload);
 
-    PacketResponseNG resp;
-    WaitForResponse(CMD_LF_FSK_SIMULATE, &resp);
-
-    PrintAndLogEx(INFO, "Done");
-    if (resp.status != PM3_EOPABORTED)
-        return resp.status;
-    return PM3_SUCCESS;
+    return lfsim_wait_check(CMD_LF_FSK_SIMULATE);
 }
 
 static command_t CommandTable[] = {
