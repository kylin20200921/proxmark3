commit ea7f2e03d38bd24b862392af04014646b9ca5942
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 1 15:38:57 2022 +0100

    cppcheck fixes

diff --git a/client/src/cmdhfksx6924.c b/client/src/cmdhfksx6924.c
index 6642804d2..9014ca519 100644
--- a/client/src/cmdhfksx6924.c
+++ b/client/src/cmdhfksx6924.c
@@ -111,7 +111,7 @@ static int CmdHFKSX6924Info(const char *Cmd) {
     uint16_t sw = 0;
     int res = KSX6924Select(true, true, buf, sizeof(buf), &len, &sw);
 
-    if (res) {
+    if (res || (len == 0)) {
         if (keep == false) {
             DropField();
         }
@@ -134,6 +134,7 @@ static int CmdHFKSX6924Info(const char *Cmd) {
     // FCI Response is a BER-TLV, we are interested in tag 6F,B0 only.
     const uint8_t *p = buf;
     struct tlv fci_tag;
+    memset(&fci_tag, 0, sizeof(fci_tag));
 
     while (len > 0) {
         memset(&fci_tag, 0, sizeof(fci_tag));
diff --git a/client/src/cmdlfgallagher.c b/client/src/cmdlfgallagher.c
index bcc09e53e..a8981e700 100644
--- a/client/src/cmdlfgallagher.c
+++ b/client/src/cmdlfgallagher.c
@@ -30,7 +30,7 @@
 static int CmdHelp(const char *Cmd);
 
 static void scramble(uint8_t *arr, uint8_t len) {
-    uint8_t lut[] = {
+    const uint8_t lut[] = {
         0xa3, 0xb0, 0x80, 0xc6, 0xb2, 0xf4, 0x5c, 0x6c, 0x81, 0xf1, 0xbb, 0xeb, 0x55, 0x67, 0x3c, 0x05,
         0x1a, 0x0e, 0x61, 0xf6, 0x22, 0xce, 0xaa, 0x8f, 0xbd, 0x3b, 0x1f, 0x5e, 0x44, 0x04, 0x51, 0x2e,
         0x4d, 0x9a, 0x84, 0xea, 0xf8, 0x66, 0x74, 0x29, 0x7f, 0x70, 0xd8, 0x31, 0x7a, 0x6d, 0xa4, 0x00,
@@ -55,7 +55,7 @@ static void scramble(uint8_t *arr, uint8_t len) {
 }
 
 static void descramble(uint8_t *arr, uint8_t len) {
-    uint8_t lut[] = {
+    const uint8_t lut[] = {
         0x2f, 0x6e, 0xdd, 0xdf, 0x1d, 0x0f, 0xb0, 0x76, 0xad, 0xaf, 0x7f, 0xbb, 0x77, 0x85, 0x11, 0x6d,
         0xf4, 0xd2, 0x84, 0x42, 0xeb, 0xf7, 0x34, 0x55, 0x4a, 0x3a, 0x10, 0x71, 0xe7, 0xa1, 0x62, 0x1a,
         0x3e, 0x4c, 0x14, 0xd3, 0x5e, 0xb2, 0x7d, 0x56, 0xbc, 0x27, 0x82, 0x60, 0xe3, 0xae, 0x1f, 0x9b,
@@ -294,8 +294,8 @@ static int CmdGallagherClone(const char *Cmd) {
     bool em = arg_get_lit(ctx, 3);
     int16_t region_code = arg_get_int_def(ctx, 4, -1);
     int32_t facility_code = arg_get_int_def(ctx, 5, -1);
-    uint64_t card_number = arg_get_int_def(ctx, 6, -1);
-    uint32_t issue_level = arg_get_int_def(ctx, 7, -1);
+    int32_t card_number = arg_get_int_def(ctx, 6, -1);
+    int32_t issue_level = arg_get_int_def(ctx, 7, -1);
     CLIParserFree(ctx);
 
     bool use_raw = raw_len > 0;
@@ -393,8 +393,8 @@ static int CmdGallagherSim(const char *Cmd) {
 
     int16_t region_code = arg_get_int_def(ctx, 2, -1);
     int32_t facility_code = arg_get_int_def(ctx, 3, -1);
-    uint64_t card_number = arg_get_int_def(ctx, 4, -1);
-    uint32_t issue_level = arg_get_int_def(ctx, 5, -1);
+    int32_t card_number = arg_get_int_def(ctx, 4, -1);
+    int32_t issue_level = arg_get_int_def(ctx, 5, -1);
     CLIParserFree(ctx);
 
     bool use_raw = raw_len > 0;
