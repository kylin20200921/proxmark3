commit ec3effcb302894ae44fa7c19325f36bbf0b42397
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Feb 25 20:15:19 2022 +0100

    too small array

diff --git a/client/src/cmdlfio.c b/client/src/cmdlfio.c
index 429b1ef8e..bb05af40f 100644
--- a/client/src/cmdlfio.c
+++ b/client/src/cmdlfio.c
@@ -137,7 +137,7 @@ int demodIOProx(bool verbose) {
     calccrc &= 0xff;
     calccrc = 0xff - calccrc;
 
-    char crc_str[36] = {0};
+    char crc_str[40] = {0};
 
     if (crc == calccrc) {
         snprintf(crc_str, sizeof(crc_str), "( " _GREEN_("ok") " )");
