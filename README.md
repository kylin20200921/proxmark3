commit b246a2a53e24f8b6c2d576c18c36a2cee5a545cf
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 22 18:34:57 2022 +0100

    make zero check stricter

diff --git a/armsrc/iso14443a.c b/armsrc/iso14443a.c
index 1e867e60e..6611254f7 100644
--- a/armsrc/iso14443a.c
+++ b/armsrc/iso14443a.c
@@ -1128,7 +1128,7 @@ bool SimulateIso14443aInit(uint8_t tagType, uint16_t flags, uint8_t *data, tag_r
     }
 
     // if uid not supplied then get from emulator memory
-    if ( (memcmp(data, "\x00\x00\x00\x00", 4) == 0) || ((flags & FLAG_UID_IN_EMUL) == FLAG_UID_IN_EMUL)) {
+    if ( (memcmp(data, "\x00\x00\x00\x00\x00\x00\x00\x00\x00\x00", 10) == 0) || ((flags & FLAG_UID_IN_EMUL) == FLAG_UID_IN_EMUL)) {
         if (tagType == 2 || tagType == 7) {
             uint16_t start = MFU_DUMP_PREFIX_LENGTH;
             uint8_t emdata[8];
