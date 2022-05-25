commit d330d22c482c1db1b620f25bb8b928b0c5cd9057
Author: iceman1001 <iceman@iuse.se>
Date:   Tue Jun 22 10:46:21 2021 +0200

    text

diff --git a/client/src/cipurse/cipursecore.c b/client/src/cipurse/cipursecore.c
index c3024a9b2..3d5cd7e8b 100644
--- a/client/src/cipurse/cipursecore.c
+++ b/client/src/cipurse/cipursecore.c
@@ -227,27 +227,28 @@ void CIPURSECSetActChannelSecurityLevels(CipurseChannelSecurityLevel req, Cipurs
 
 static void CIPURSEPrintPersoMode(uint8_t data) {
     if ((data & 0x01) == 0x01)
-        PrintAndLogEx(INFO, "Perso: filesystem");
+        PrintAndLogEx(INFO, "Perso... " _YELLOW_("filesystem"));
     if ((data & 0x02) == 0x02)
-        PrintAndLogEx(INFO, "Perso: EMV");
+        PrintAndLogEx(INFO, "Perso... " _YELLOW_("EMV"));
     if ((data & 0x04) == 0x04)
-        PrintAndLogEx(INFO, "Perso: transaction supported");
+        PrintAndLogEx(INFO, "Perso... " _YELLOW_("transaction supported"));
 }
 
+// 2021 iceman: what is the description text of profile L,S,T ?
 static void CIPURSEPrintProfileInfo(uint8_t data) {
     if ((data & 0x01) == 0x01)
-        PrintAndLogEx(INFO, "Profile: L");
+        PrintAndLogEx(INFO, "Profile... L");
     if ((data & 0x02) == 0x02)
-        PrintAndLogEx(INFO, "Profile: S");
+        PrintAndLogEx(INFO, "Profile... S");
     if ((data & 0x04) == 0x04)
-        PrintAndLogEx(INFO, "Profile: T");
+        PrintAndLogEx(INFO, "Profile... T");
 }
 
 static void CIPURSEPrintManufacturerInfo(uint8_t data) {
     if (data == 0)
-        PrintAndLogEx(INFO, "Manufacturer: n/a");
+        PrintAndLogEx(INFO, "Manufacturer... n/a");
     else
-        PrintAndLogEx(INFO, "Manufacturer: %s", getTagInfo(data)); // getTagInfo from cmfhf14a.h
+        PrintAndLogEx(INFO, "Manufacturer... %s", getTagInfo(data)); // getTagInfo from cmfhf14a.h
 }
 
 void CIPURSEPrintInfoFile(uint8_t *data, size_t len) {
