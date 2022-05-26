commit e04b094cd229e74322c6be9ef14df26cb442367f
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Feb 5 11:21:03 2022 +0100

    emphase issue

diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index 022f2b53b..0031871c1 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -1843,7 +1843,7 @@ int infoHF14A(bool verbose, bool do_nack_test, bool do_aid_search) {
         }
 
         if (card.ats[0] != card.ats_len - 2) {
-            PrintAndLogEx(WARNING, "ATS may be corrupted. Length of ATS (%d bytes incl. 2 Bytes CRC) doesn't match TL", card.ats_len);
+            PrintAndLogEx(WARNING, _RED_("ATS may be corrupted.") " Length of ATS (%d bytes incl. 2 Bytes CRC) doesn't match TL", card.ats_len);
         }
 
         PrintAndLogEx(SUCCESS, "ATS: " _YELLOW_("%s")"[ %02X %02X ]", sprint_hex(card.ats, card.ats_len - 2), card.ats[card.ats_len - 1], card.ats[card.ats_len]);
