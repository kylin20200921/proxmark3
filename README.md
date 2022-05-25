commit b974a077366a4ef1f66c7fdcd9600e91821594f7
Author: iceman1001 <iceman@iuse.se>
Date:   Thu May 20 10:31:00 2021 +0200

    text

diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index 22022d3c6..aab5f6c17 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -397,7 +397,7 @@ int Hf14443_4aGetCardData(iso14a_card_select_t *card) {
 static int CmdHF14AReader(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf 14a reader",
-                  "Reader for ISO 14443A based tags",
+                  "Act as a ISO-14443a reader to identify tag. Look for ISO-14443a tags until Enter or the pm3 button is pressed",
                   "hf 14a reader -@   -> Continuous mode");
 
     void *argtable[] = {
diff --git a/client/src/cmdhffelica.c b/client/src/cmdhffelica.c
index 02efb2e30..70999787a 100644
--- a/client/src/cmdhffelica.c
+++ b/client/src/cmdhffelica.c
@@ -119,14 +119,13 @@ static void print_service_code_list_constraints(void) {
 /*
 static int usage_hf_felica_sim(void) {
     PrintAndLogEx(INFO, "\n Emulating ISO/18092 FeliCa tag \n");
-    PrintAndLogEx(INFO, "Usage: hf felica sim [h] t <type> [v]");
+    PrintAndLogEx(INFO, "Usage: hf felica sim -t <type> [-v]");
     PrintAndLogEx(INFO, "Options:");
-    PrintAndLogEx(INFO, "    h     : This help");
     PrintAndLogEx(INFO, "    t     : 1 = FeliCa");
     PrintAndLogEx(INFO, "          : 2 = FeliCaLiteS");
     PrintAndLogEx(INFO, "    v     : (Optional) Verbose");
     PrintAndLogEx(INFO, "Examples:");
-    PrintAndLogEx(INFO, "          hf felica sim t 1 ");
+    PrintAndLogEx(INFO, "          hf felica sim -t 1");
     return PM3_SUCCESS;
 }
 */
@@ -259,7 +258,7 @@ int read_felica_uid(bool loop, bool verbose) {
 static int CmdHFFelicaReader(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf felica reader",
-                  "Reader for FeliCa based tags",
+                  "Act as a ISO 18092 / FeliCa reader. Look for FeliCa tags until Enter or the pm3 button is pressed",
                   "hf felica reader -@    -> Continuous mode");
 
     void *argtable[] = {
