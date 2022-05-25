commit 8d5f31757f155c95d7ede4abf9cd1f6531b7344a
Author: iceman1001 <iceman@iuse.se>
Date:   Tue Jan 11 10:59:22 2022 +0100

    avoid printing null items

diff --git a/client/src/wiegand_formats.c b/client/src/wiegand_formats.c
index 1e417df5d..d7818661c 100644
--- a/client/src/wiegand_formats.c
+++ b/client/src/wiegand_formats.c
@@ -1259,6 +1259,11 @@ static bool Unpack_bc40(wiegand_message_t *packed, wiegand_card_t *card) {
 
 void print_desc_wiegand(cardformat_t *fmt, wiegand_message_t *packed) {
 
+    // return if invalid card format
+    if (fmt->Name == NULL) {
+        return;
+    }
+
     char *s = calloc(128, sizeof(uint8_t));
     sprintf(s, _YELLOW_("%-10s")" %-32s",  fmt->Name, fmt->Descrp);
 
@@ -1397,6 +1402,11 @@ void HIDListFormats(void) {
 }
 
 cardformat_t HIDGetCardFormat(int idx) {
+
+    // if idx is out-of-bounds, return the last item
+    if ((idx < 0) || (idx > ARRAYLEN(FormatTable) - 2)) {
+        return FormatTable[ARRAYLEN(FormatTable) - 1];
+    }
     return FormatTable[idx];
 }
 
