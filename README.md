commit e45924b45093b19cd48d51b682639767c4bb82df
Author: iceman1001 <iceman@iuse.se>
Date:   Fri May 14 22:45:57 2021 +0200

    moar format,  WIP, need more samples

diff --git a/client/src/wiegand_formats.c b/client/src/wiegand_formats.c
index 96d4677c8..b989aeff1 100644
--- a/client/src/wiegand_formats.c
+++ b/client/src/wiegand_formats.c
@@ -1206,6 +1206,47 @@ static bool Unpack_pw39(wiegand_message_t *packed, wiegand_card_t *card) {
     return true;
 }
 
+
+static bool Pack_bc40(wiegand_card_t *card, wiegand_message_t *packed, bool preamble) {
+
+    memset(packed, 0, sizeof(wiegand_message_t));
+
+    if (card->FacilityCode > 0xFFF) return false; // Can't encode FC.
+    if (card->CardNumber > 0xFFFFF) return false; // Can't encode CN.
+    if (card->IssueLevel > 0) return false; // Not used in this format
+    if (card->OEM > 0x7F) return false; // Not used in this format
+
+    packed->Length = 39; // Set number of bits
+
+    set_linear_field(packed, card->OEM, 0, 7);
+
+    // cost center 12
+    set_linear_field(packed, card->FacilityCode, 7, 12);
+    set_linear_field(packed, card->CardNumber, 19, 19);
+
+    set_bit_by_position(packed,
+                        oddparity32(get_linear_field(packed, 19, 19))
+                        , 39);
+
+    if (preamble)
+        return add_HID_header(packed);
+    return true;
+}
+
+static bool Unpack_bc40(wiegand_message_t *packed, wiegand_card_t *card) {
+    memset(card, 0, sizeof(wiegand_card_t));
+
+    if (packed->Length != 39) return false; // Wrong length? Stop here.
+
+    card->OEM = get_linear_field(packed, 0, 7);
+    card->FacilityCode = get_linear_field(packed, 7, 12);
+    card->CardNumber = get_linear_field(packed, 19, 19);
+
+    card->ParityValid =
+        (get_bit_by_position(packed, 39) == oddparity32(get_linear_field(packed, 19, 19)));
+    return true;
+}
+
 // ---------------------------------------------------------------------------------------------------
 
 void print_desc_wiegand(cardformat_t *fmt, wiegand_message_t *packed) {
@@ -1324,6 +1365,7 @@ static const cardformat_t FormatTable[] = {
     {"P10001",  Pack_P10001,  Unpack_P10001,  "HID P10001 Honeywell 40-bit",   {1, 1, 0, 1, 0}}, // from cardinfo.barkweb.com.au
     {"Casi40",  Pack_CasiRusco40, Unpack_CasiRusco40, "Casi-Rusco 40-bit",     {1, 0, 0, 0, 0}}, // from cardinfo.barkweb.com.au
     {"C1k48s",  Pack_C1k48s,  Unpack_C1k48s,  "HID Corporate 1000 48-bit std", {1, 1, 0, 0, 1}}, // imported from old pack/unpack
+    {"BC40",    Pack_bc40,    Unpack_bc40,    "Bundy TimeClock 40-bit",     {1, 1, 0, 1, 1}}, // from 
     {NULL, NULL, NULL, NULL, {0, 0, 0, 0, 0}} // Must null terminate array
 };
 
@@ -1341,7 +1383,7 @@ void HIDListFormats(void) {
         ++i;
     }
     PrintAndLogEx(INFO, "------------------------------------------------------------");
-    PrintAndLogEx(INFO, "Available card formats: " _YELLOW_("%u"), ARRAYLEN(FormatTable));
+    PrintAndLogEx(INFO, "Available card formats: " _YELLOW_("%" PRIu32), ARRAYLEN(FormatTable));
     PrintAndLogEx(NORMAL, "");
     return;
 }
