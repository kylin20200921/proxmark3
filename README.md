commit 77520ce489f5c75701b648f55c573463dbede698
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Apr 10 13:17:21 2022 +0200

    fix lf sniff input values to be atleast a bit limited

diff --git a/client/src/cmdlf.c b/client/src/cmdlf.c
index a2d7e2450..9e8927668 100644
--- a/client/src/cmdlf.c
+++ b/client/src/cmdlf.c
@@ -746,11 +746,10 @@ int lf_sniff(bool verbose, uint32_t samples) {
     struct p {
         uint32_t samples : 31;
         bool     verbose : 1;
-    } PACKED;
+    } PACKED payload;
 
-    struct p payload;
+    payload.samples = (samples & 0xFFFF);
     payload.verbose = verbose;
-    payload.samples = samples;
 
     clearCommandBuffer();
     SendCommandNG(CMD_LF_SNIFF_RAW_ADC, (uint8_t *)&payload, sizeof(payload));
@@ -758,7 +757,7 @@ int lf_sniff(bool verbose, uint32_t samples) {
     if (gs_lf_threshold_set) {
         WaitForResponse(CMD_LF_SNIFF_RAW_ADC, &resp);
     } else {
-        if (!WaitForResponseTimeout(CMD_LF_SNIFF_RAW_ADC, &resp, 2500)) {
+        if (WaitForResponseTimeout(CMD_LF_SNIFF_RAW_ADC, &resp, 2500) == false) {
             PrintAndLogEx(WARNING, "(lf_read) command execution time out");
             return PM3_ETIMEOUT;
         }
@@ -791,7 +790,7 @@ int CmdLFSniff(const char *Cmd) {
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
-    uint32_t samples = arg_get_u32_def(ctx, 1, 0);
+    uint32_t samples = (arg_get_u32_def(ctx, 1, 0) & 0xFFFF);
     bool verbose = arg_get_lit(ctx, 2);
     bool cm = arg_get_lit(ctx, 3);
     CLIParserFree(ctx);
