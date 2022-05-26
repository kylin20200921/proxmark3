commit 799fa850efede7c0f3b2afd6099fa3a4f93f0031
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Apr 30 20:55:41 2022 +0200

    twice atqa?

diff --git a/armsrc/iso14443a.c b/armsrc/iso14443a.c
index 6fc04a4cd..eb52583ca 100644
--- a/armsrc/iso14443a.c
+++ b/armsrc/iso14443a.c
@@ -2538,6 +2538,10 @@ int iso14443a_select_cardEx(uint8_t *uid_ptr, iso14a_card_select_t *p_card, uint
                 return 0;
             }
 
+            if (GetATQA(resp, resp_par, false, false) == false) {
+                return 0;
+            }
+
             p_card->sak = 0x0A;
             p_card->uidlen = 4;
             return 1;
