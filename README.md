commit 4bd6e8f61c3f6fb6de16fd3159b153bd58ca3fde
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Feb 24 17:20:27 2022 +0100

    fixes to "hf cipurse info"

diff --git a/client/src/cipurse/cipursecore.c b/client/src/cipurse/cipursecore.c
index fc20c4854..cce2d0484 100644
--- a/client/src/cipurse/cipursecore.c
+++ b/client/src/cipurse/cipursecore.c
@@ -307,12 +307,15 @@ static void CIPURSEPrintPersoMode(uint8_t data) {
 
 // 2021 iceman: what is the description text of profile L,S,T ?
 static void CIPURSEPrintProfileInfo(uint8_t data) {
+
+    PrintAndLogEx(INFO, "Profile........" NOLF);
     if ((data & 0x01) == 0x01)
-        PrintAndLogEx(INFO, "Profile........ L");
+        PrintAndLogEx(NORMAL, " L" NOLF);
     if ((data & 0x02) == 0x02)
-        PrintAndLogEx(INFO, "Profile........ S");
+        PrintAndLogEx(NORMAL, ", S" NOLF);
     if ((data & 0x04) == 0x04)
-        PrintAndLogEx(INFO, "Profile........ T");
+        PrintAndLogEx(NORMAL, ", T" NOLF);
+    PrintAndLogEx(NORMAL, "");
 }
 
 static void CIPURSEPrintManufacturerInfo(uint8_t data) {
