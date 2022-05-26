commit 1222d995ef1b2d5e2940d7e5d8715069bb151e5c
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Mar 12 19:21:09 2022 +0100

    remove c param

diff --git a/client/src/cmddata.c b/client/src/cmddata.c
index 0cf28dd4a..77d90fc0b 100644
--- a/client/src/cmddata.c
+++ b/client/src/cmddata.c
@@ -2893,7 +2893,7 @@ static int CmdDiff(const char *Cmd) {
                     "data diff -w 4 -a hf-mfu-01020304.bin -b hf-mfu-04030201.bin\n"
                     "data diff -a fileA -b fileB\n"
                     "data diff -a fileA --eb\n"
-                    "data diff -a fileA --cb\n"
+//                    "data diff -a fileA --cb\n"
                     "data diff --fa fileA -b fileB\n"
                     "data diff --fa fileA --fb fileB\n"
                     "data diff --ea --cb\n"
@@ -2903,7 +2903,7 @@ static int CmdDiff(const char *Cmd) {
         arg_param_begin,
         arg_str0("a",  NULL, "<fn>", "input file name A"),
         arg_str0("b",  NULL, "<fn>", "input file name B"),
-        arg_lit0(NULL, "cb", "magic gen1 <hf mf csave>"),
+//        arg_lit0(NULL, "cb", "magic gen1 <hf mf csave>"),
         arg_lit0(NULL, "eb", "emulator memory <hf mf esave>"),
         arg_str0(NULL, "fa", "<fn>", "input spiffs file A"),
         arg_str0(NULL, "fb", "<fn>", "input spiffs file B"),
@@ -2920,20 +2920,20 @@ static int CmdDiff(const char *Cmd) {
     char filenameB[FILE_PATH_SIZE] = {0};
     CLIParamStrToBuf(arg_get_str(ctx, 2), (uint8_t *)filenameB, FILE_PATH_SIZE, &fnlenB);
 
-    bool use_c = arg_get_lit(ctx, 3);
-    bool use_e = arg_get_lit(ctx, 4);
+//    bool use_c = arg_get_lit(ctx, 3);
+    bool use_e = arg_get_lit(ctx, 3);
 
     // SPIFFS filename A
     int splenA = 0;
     char spnameA[FILE_PATH_SIZE] = {0};
-    CLIParamStrToBuf(arg_get_str(ctx, 5), (uint8_t *)spnameA, FILE_PATH_SIZE, &splenA);
+    CLIParamStrToBuf(arg_get_str(ctx, 4), (uint8_t *)spnameA, FILE_PATH_SIZE, &splenA);
 
     // SPIFFS filename B
     int splenB = 0;
     char spnameB[FILE_PATH_SIZE] = {0};
-    CLIParamStrToBuf(arg_get_str(ctx, 6), (uint8_t *)spnameB, FILE_PATH_SIZE, &splenB);
+    CLIParamStrToBuf(arg_get_str(ctx, 5), (uint8_t *)spnameB, FILE_PATH_SIZE, &splenB);
 
-    int width = arg_get_int_def(ctx, 7, 16) & 0x3F;
+    int width = arg_get_int_def(ctx, 6, 16);
     CLIParserFree(ctx);
 
     // sanity check
@@ -3074,9 +3074,12 @@ static int CmdDiff(const char *Cmd) {
     }
 
     // dump magic card memory
+    /*
     if (use_c) {
-
+        PrintAndLogEx(WARNING, "not implemented yet, feel free to contribute!");
+        return PM3_ENOTIMPL;
     }
+    */
 
     size_t n = (datalenA > datalenB) ? datalenB : datalenA;
     PrintAndLogEx(INFO, "data len:  %zu   A %zu  B %zu", n, datalenA, datalenB);
