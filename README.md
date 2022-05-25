commit 4919d14c13d9d07fbca55a40ca034350bfa82c38
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 1 16:29:51 2022 +0100

    cppcheck fix

diff --git a/client/src/wiegand_formats.c b/client/src/wiegand_formats.c
index 75f9ea987..7f507d982 100644
--- a/client/src/wiegand_formats.c
+++ b/client/src/wiegand_formats.c
@@ -1414,7 +1414,7 @@ int HIDFindCardFormat(const char *format) {
 bool HIDPack(int format_idx, wiegand_card_t *card, wiegand_message_t *packed, bool preamble) {
     memset(packed, 0, sizeof(wiegand_message_t));
 
-    if (format_idx < 0 || format_idx >= ARRAYLEN(FormatTable))
+    if (format_idx < 0 || format_idx >= ARRAYLEN(FormatTable) - 1)
         return false;
 
     return FormatTable[format_idx].Pack(card, packed, preamble);
