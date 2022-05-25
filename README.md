commit b8aedada544f08f7a97375f84cdde2734a5044f7
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jun 20 08:56:06 2021 +0200

    text

diff --git a/client/src/cmddata.c b/client/src/cmddata.c
index 2a715325c..4371a0187 100644
--- a/client/src/cmddata.c
+++ b/client/src/cmddata.c
@@ -2702,7 +2702,7 @@ typedef struct {
 static int print_modulation(lf_modulation_t b) {
     PrintAndLogEx(INFO, " Modulation........ " _GREEN_("%s"), GetSelectedModulationStr(b.modulation));
     PrintAndLogEx(INFO, " Bit clock......... " _GREEN_("RF/%d"), b.bitrate);
-    PrintAndLogEx(INFO, " Approx baudrate... " _GREEN_("%.f") "bauds", (125000 / (float)b.bitrate));
+    PrintAndLogEx(INFO, " Approx baudrate... " _GREEN_("%.f") " baud", (125000 / (float)b.bitrate));
     switch (b.modulation) {
         case DEMOD_PSK1:
         case DEMOD_PSK2:
