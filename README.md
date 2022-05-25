commit 9d02c2e94a4632edf66bfea44095d373fa9ffab4
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jun 20 16:37:51 2021 +0200

    renamed 'hf mfdes readdata, writedata'  to 'read/write'

diff --git a/client/src/cmdhfmfdes.c b/client/src/cmdhfmfdes.c
index 48cd15b5c..4a59c780a 100644
--- a/client/src/cmdhfmfdes.c
+++ b/client/src/cmdhfmfdes.c
@@ -3005,11 +3005,11 @@ static int CmdHF14ADesGetValueData(const char *Cmd) {
 
 static int CmdHF14ADesReadData(const char *Cmd) {
     CLIParserContext *ctx;
-    CLIParserInit(&ctx, "hf mfdes readdata",
+    CLIParserInit(&ctx, "hf mfdes read",
                   "Read data from File\n"
                   "Make sure to select aid or authenticate aid before running this command.",
-                  "hf mfdes readdata -n 01 -t 0 -o 000000 -l 000000 -a 123456\n"
-                  "hf mfdes readdata -n 01 -t 0                 --> Read all data from standard file, fileno 01"
+                  "hf mfdes read -n 1 -t 0 -o 000000 -l 000000 -a 123456\n"
+                  "hf mfdes read -n 1 -t 0                       --> Read all data from standard file, fileno 1"
                  );
 
     void *argtable[] = {
@@ -3099,13 +3099,13 @@ static int CmdHF14ADesReadData(const char *Cmd) {
         if (res == PM3_SUCCESS) {
             uint32_t len = le24toh(ft.length);
 
-            PrintAndLogEx(SUCCESS, "Read %u bytes from file %d:", ft.fileno, len);
+            PrintAndLogEx(SUCCESS, "Read %u bytes from file %d", ft.fileno, len);
             PrintAndLogEx(INFO, "Offset  | Data                                            | Ascii");
             PrintAndLogEx(INFO, "----------------------------------------------------------------------------");
 
             for (uint32_t i = 0; i < len; i += 16) {
                 uint32_t l = len - i;
-                PrintAndLogEx(INFO, "%02d/0x%02X | %s| %s", i, i, sprint_hex(&ft.data[i], l > 16 ? 16 : l), sprint_ascii(&ft.data[i], l > 16 ? 16 : l));
+                PrintAndLogEx(INFO, "%3d/0x%02X | %s| %s", i, i, sprint_hex(&ft.data[i], l > 16 ? 16 : l), sprint_ascii(&ft.data[i], l > 16 ? 16 : l));
             }
         } else {
             PrintAndLogEx(ERR, "Couldn't read data. Error %d", res);
@@ -3209,10 +3209,10 @@ static int CmdHF14ADesChangeValue(const char *Cmd) {
 static int CmdHF14ADesWriteData(const char *Cmd) {
 
     CLIParserContext *ctx;
-    CLIParserInit(&ctx, "hf mfdes writedata",
-                  "Write data to File\n"
+    CLIParserInit(&ctx, "hf mfdes write",
+                  "Write data to file\n"
                   "Make sure to select aid or authenticate aid before running this command.",
-                  "hf mfdes writedata -n 01 -t 0 -o 000000 -d 3132333435363738"
+                  "hf mfdes write -n 01 -t 0 -o 000000 -d 3132333435363738"
                  );
 
     void *argtable[] = {
@@ -5143,8 +5143,8 @@ static command_t CommandTable[] = {
     {"deletefile",       CmdHF14ADesDeleteFile,       IfPm3Iso14443a,  "Create Delete File"},
     {"dump",             CmdHF14ADesDump,             IfPm3Iso14443a,  "Dump all files"},
     {"getvalue",         CmdHF14ADesGetValueData,     IfPm3Iso14443a,  "Get value of file"},
-    {"readdata",         CmdHF14ADesReadData,         IfPm3Iso14443a,  "Read data from standard/backup/record file"},
-    {"writedata",        CmdHF14ADesWriteData,        IfPm3Iso14443a,  "Write data to standard/backup/record file"},
+    {"read",             CmdHF14ADesReadData,         IfPm3Iso14443a,  "Read data from standard/backup/record file"},
+    {"write",            CmdHF14ADesWriteData,        IfPm3Iso14443a,  "Write data to standard/backup/record file"},
     {NULL, NULL, NULL, NULL}
 };
 
