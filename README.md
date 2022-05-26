commit 2eaa60e95e38ac6a6fc3b9c9e30ad288df3df764
Author: iceman1001 <iceman@iuse.se>
Date:   Wed Feb 16 00:20:02 2022 +0100

    cppcheck

diff --git a/client/src/cmdtrace.c b/client/src/cmdtrace.c
index 0ddd6cf71..acbc5e5ef 100644
--- a/client/src/cmdtrace.c
+++ b/client/src/cmdtrace.c
@@ -79,7 +79,7 @@ static bool merge_topaz_reader_frames(uint32_t timestamp, uint32_t *duration, ui
 
     return true;
 }
-static uint8_t calc_pos(uint8_t *d) {
+static uint8_t calc_pos(const uint8_t *d) {
     // PCB [CID] [NAD] [INF] CRC CRC
     uint8_t pos = 1;
     if ((d[0] & 0x08) == 0x08)  // cid byte following
