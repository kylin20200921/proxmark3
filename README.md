commit 69ffcd31851daee96861f98d1b5f71ab62ffaa2c
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Dec 25 16:13:42 2021 +0100

    wrong byte in rats, and adapted two standalone modes

diff --git a/armsrc/Standalone/hf_craftbyte.c b/armsrc/Standalone/hf_craftbyte.c
index e13dd0310..2e8704e5e 100644
--- a/armsrc/Standalone/hf_craftbyte.c
+++ b/armsrc/Standalone/hf_craftbyte.c
@@ -98,6 +98,9 @@ void RunMod(void) {
                     } else if (card.sak == 0x20 && card.atqa[0] == 0x04 && card.atqa[1] == 0x03) {
                         DbpString("Mifare DESFire");
                         SimulateIso14443aTag(3, flags, card.uid, 0);
+                    } else if (card.sak == 0x20 && card.atqa[0] == 0x44 && card.atqa[1] == 0x03) {
+                        DbpString("Mifare DESFire Ev1/Plus/JCOP");
+                        SimulateIso14443aTag(3, flags, card.uid, 0);
                     } else {
                         Dbprintf("Unrecognized tag type -- defaulting to Mifare Classic emulation");
                         SimulateIso14443aTag(1, flags, card.uid, 0);
diff --git a/armsrc/Standalone/hf_young.c b/armsrc/Standalone/hf_young.c
index e7b69d550..aed5e0dd6 100644
--- a/armsrc/Standalone/hf_young.c
+++ b/armsrc/Standalone/hf_young.c
@@ -257,6 +257,9 @@ void RunMod(void) {
                     } else if (uids[selected].sak == 0x20 && uids[selected].atqa[0] == 0x04 && uids[selected].atqa[1] == 0x03) {
                         DbpString("Mifare DESFire");
                         SimulateIso14443aTag(3, flags, data, 0);
+                    } else if (uids[selected].sak == 0x20 && uids[selected].atqa[0] == 0x44 && uids[selected].atqa[1] == 0x03) {
+                        DbpString("Mifare DESFire Ev1/Plus/JCOP");
+                        SimulateIso14443aTag(3, flags, data, 0);
                     } else {
                         Dbprintf("Unrecognized tag type -- defaulting to Mifare Classic emulation");
                         SimulateIso14443aTag(1, flags, data, 0);
diff --git a/armsrc/iso14443a.c b/armsrc/iso14443a.c
index 7f3040685..f8aec2a81 100644
--- a/armsrc/iso14443a.c
+++ b/armsrc/iso14443a.c
@@ -1044,7 +1044,7 @@ bool SimulateIso14443aInit(int tagType, int flags, uint8_t *data, tag_response_i
             rATQA[0] = 0x04;
             rATQA[1] = 0x03;
             sak = 0x20;
-            memcpy(rRATS, "\x06\x75\x77\x81\x02\x00\x00\x00", 8);
+            memcpy(rRATS, "\x06\x75\x77\x81\x02\x80\x00\x00", 8);
         }
         break;
         case 4: { // ISO/IEC 14443-4 - javacard (JCOP)
