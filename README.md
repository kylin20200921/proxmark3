commit fa10e7464eb7f7ac75320228c3cd90ef25bb182f
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 29 10:18:16 2022 +0100

    cppcheck unused

diff --git a/client/src/cmdhfmfdes.c b/client/src/cmdhfmfdes.c
index 95736bcd5..5afa3917e 100644
--- a/client/src/cmdhfmfdes.c
+++ b/client/src/cmdhfmfdes.c
@@ -3397,15 +3397,11 @@ static int CmdHF14ADesGetFileSettings(const char *Cmd) {
 
 static int DesfireCreateFileParameters(
     CLIParserContext *ctx,
-
     uint8_t pfileid, uint8_t pisofileid,
-    uint8_t amodeid,
-    uint8_t frightsid,
+    uint8_t amodeid, uint8_t frightsid,
     uint8_t r_modeid, uint8_t w_modeid, uint8_t rw_modeid, uint8_t ch_modeid,
+    uint8_t *data, size_t *datalen ) {
 
-    uint8_t *data,
-    size_t *datalen
-) {
     *datalen = 0;
 
     uint32_t fileid = 1;
@@ -4474,7 +4470,11 @@ static int CmdHF14ADesClearRecordFile(const char *Cmd) {
     return PM3_SUCCESS;
 }
 
-static int DesfileReadISOFileAndPrint(DesfireContext_t *dctx, bool select_current_file, uint8_t fnum, uint16_t fisoid, int filetype, uint32_t offset, uint32_t length, bool noauth, bool verbose) {
+static int DesfileReadISOFileAndPrint(DesfireContext_t *dctx,
+          bool select_current_file, uint8_t fnum,
+          uint16_t fisoid, int filetype, 
+          uint32_t offset, uint32_t length,
+          bool noauth, bool verbose) {
 
     if (filetype == RFTAuto) {
         PrintAndLogEx(ERR, "ISO mode needs to specify file type");
@@ -4498,7 +4498,7 @@ static int DesfileReadISOFileAndPrint(DesfireContext_t *dctx, bool select_curren
 
     uint8_t resp[2048] = {0};
     size_t resplen = 0;
-    int res = 0;
+    int res;
 
     if (filetype == RFTData) {
         res = DesfireISOReadBinary(dctx, !select_current_file, (select_current_file) ? 0x00 : fnum, offset, length, resp, &resplen);
@@ -4568,8 +4568,12 @@ static int DesfileReadISOFileAndPrint(DesfireContext_t *dctx, bool select_curren
     return PM3_SUCCESS;
 }
 
-static int DesfileReadFileAndPrint(DesfireContext_t *dctx, uint8_t fnum, int filetype, uint32_t offset, uint32_t length, uint32_t maxdatafilelength, bool noauth, bool verbose) {
-    int res = 0;
+static int DesfileReadFileAndPrint(DesfireContext_t *dctx, 
+          uint8_t fnum, int filetype,
+          uint32_t offset, uint32_t length,
+          uint32_t maxdatafilelength, bool noauth, bool verbose) {
+
+    int res;
     // length of record for record file
     size_t reclen = 0;
 
@@ -4880,7 +4884,11 @@ static int CmdHF14ADesReadData(const char *Cmd) {
     return res;
 }
 
-static int DesfileWriteISOFile(DesfireContext_t *dctx, bool select_current_file, uint8_t fnum, uint16_t fisoid, int filetype, uint32_t offset, uint8_t *data, uint32_t datalen, bool verbose) {
+static int DesfileWriteISOFile(DesfireContext_t *dctx,
+          bool select_current_file, uint8_t fnum,
+          uint16_t fisoid, int filetype,
+          uint32_t offset, uint8_t *data,
+          uint32_t datalen, bool verbose) {
 
     if (filetype == RFTAuto) {
         PrintAndLogEx(ERR, "ISO mode needs to specify file type");
@@ -4902,7 +4910,7 @@ static int DesfileWriteISOFile(DesfireContext_t *dctx, bool select_current_file,
         return PM3_EINVARG;
     }
 
-    int res = 0;
+    int res;
     if (filetype == RFTData) {
         res = DesfireISOUpdateBinary(dctx, !select_current_file, (select_current_file) ? 0x00 : fnum, offset, data, datalen);
         if (res != PM3_SUCCESS) {
