commit caf9f07c7ab397837d4eb2d73b63120f33bfb837
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Jan 10 22:41:02 2022 +0100

    fix lf hid brute param for format

diff --git a/client/src/cmdlfhid.c b/client/src/cmdlfhid.c
index e673f1829..cd86cd9fb 100644
--- a/client/src/cmdlfhid.c
+++ b/client/src/cmdlfhid.c
@@ -469,16 +469,6 @@ static int CmdHIDClone(const char *Cmd) {
 */
 
 static int CmdHIDBrute(const char *Cmd) {
-
-    uint32_t delay = 1000;
-    int format_idx = -1;
-    int direction = 0;
-    uint8_t format[16] = {0};
-    int formatLen;
-
-    wiegand_card_t cn_hi, cn_low;
-    memset(&cn_hi, 0, sizeof(wiegand_card_t));
-
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "lf hid brute",
                   "Enables bruteforce of HID readers with specified facility code.\n"
@@ -506,22 +496,29 @@ static int CmdHIDBrute(const char *Cmd) {
     CLIExecWithReturn(ctx, Cmd, argtable, false);
 
     bool verbose = arg_get_lit(ctx, 1);
-    formatLen = sizeof(format);
-    CLIGetStrWithReturn(ctx, 2, format, &formatLen);
 
-    format_idx = HIDFindCardFormat(format);
+    char format[16] = {0};
+    int format_len = 0;
+    CLIParamStrToBuf(arg_get_str(ctx, 2), (uint8_t *)format, sizeof(format), &format_len);
+
+    int format_idx = HIDFindCardFormat(format);
     if (format_idx == -1) {
         PrintAndLogEx(WARNING, "Unknown format: " _YELLOW_("%s"), format);
         CLIParserFree(ctx);
         return PM3_EINVARG;
     }
 
+    wiegand_card_t cn_hi, cn_low;
+    memset(&cn_hi, 0, sizeof(wiegand_card_t));
+
     cn_hi.FacilityCode = arg_get_u32_def(ctx, 3, 0);
     cn_hi.CardNumber = arg_get_u32_def(ctx, 4, 0);
     cn_hi.IssueLevel = arg_get_u32_def(ctx, 5, 0);
     cn_hi.OEM = arg_get_u32_def(ctx, 6, 0);
-    delay = arg_get_u32_def(ctx, 7, 1000);
 
+    uint32_t delay = arg_get_u32_def(ctx, 7, 1000);
+
+    int direction = 0;
     if (arg_get_lit(ctx, 8) && arg_get_lit(ctx, 9)) {
         direction = 0;
     } else if (arg_get_lit(ctx, 8)) {
