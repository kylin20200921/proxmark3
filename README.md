commit c5658f2a0b4f0da5028833c214a070368289914f
Author: iceman1001 <iceman@iuse.se>
Date:   Wed Feb 16 00:22:48 2022 +0100

    cppcheck

diff --git a/client/src/cipurse/cipursecore.c b/client/src/cipurse/cipursecore.c
index 41bce0eb5..738839b00 100644
--- a/client/src/cipurse/cipursecore.c
+++ b/client/src/cipurse/cipursecore.c
@@ -465,14 +465,14 @@ const char *CIPURSEGetSMR(uint8_t smr) {
     }
 }
 
-void CIPURSEPrintSMR(uint8_t *smrrec) {
+void CIPURSEPrintSMR(const uint8_t *smrrec) {
     PrintAndLogEx(INFO, "1. %s/%s", CIPURSEGetSMR((smrrec[0] >> 6) & 0x03), CIPURSEGetSMR((smrrec[0] >> 4) & 0x03));
     PrintAndLogEx(INFO, "2. %s/%s", CIPURSEGetSMR((smrrec[0] >> 2) & 0x03), CIPURSEGetSMR((smrrec[0] >> 0) & 0x03));
     PrintAndLogEx(INFO, "3. %s/%s", CIPURSEGetSMR((smrrec[1] >> 6) & 0x03), CIPURSEGetSMR((smrrec[1] >> 4) & 0x03));
     PrintAndLogEx(INFO, "4. %s/%s", CIPURSEGetSMR((smrrec[1] >> 2) & 0x03), CIPURSEGetSMR((smrrec[1] >> 0) & 0x03));
 }
 
-void CIPURSEPrintART(uint8_t *artrec, size_t artlen) {
+void CIPURSEPrintART(const uint8_t *artrec, size_t artlen) {
     if (artlen < 1 || artlen > 9)
         return;
     for (int i = 0; i < artlen; i++) {
diff --git a/client/src/cipurse/cipursecore.h b/client/src/cipurse/cipursecore.h
index 584fc8ea6..603bab765 100644
--- a/client/src/cipurse/cipursecore.h
+++ b/client/src/cipurse/cipursecore.h
@@ -65,8 +65,8 @@ bool CIPURSEChannelAuthenticate(uint8_t keyindex, uint8_t *key, bool verbose);
 void CIPURSECSetActChannelSecurityLevels(CipurseChannelSecurityLevel req, CipurseChannelSecurityLevel resp);
 
 const char *CIPURSEGetSMR(uint8_t smr);
-void CIPURSEPrintSMR(uint8_t *smrrec);
-void CIPURSEPrintART(uint8_t *artrec, size_t artlen);
+void CIPURSEPrintSMR(const uint8_t *smrrec);
+void CIPURSEPrintART(const uint8_t *artrec, size_t artlen);
 void CIPURSEPrintEFFileAttr(uint8_t *attr, size_t len);
 void CIPURSEPrintFileAttrEx(uint8_t *attr, size_t len, bool isDGI);
 void CIPURSEPrintFileAttr(uint8_t *attr, size_t len);
