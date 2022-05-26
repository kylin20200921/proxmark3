commit 63612c93758c391dd0cf9b6b6b5486cf4f16aa32
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Mar 13 21:42:38 2022 +0100

    data diff - handle different lengths

diff --git a/client/src/cmddata.c b/client/src/cmddata.c
index a8f65a388..0961c6853 100644
--- a/client/src/cmddata.c
+++ b/client/src/cmddata.c
@@ -3107,16 +3107,16 @@ static int CmdDiff(const char *Cmd) {
     PrintAndLogEx(INFO, hdr0);
     PrintAndLogEx(INFO, hdr1);
 
-    // index 4bytes, spaces, bar, bytes with ansi codes
-    // (16 * 2 * 8 ) + 32 
     char line[880] = {0};
 
     // print data diff loop
-    for (int i = 0; i < n;  i += width ) {
+    int i;
+    for (i = 0; i < n;  i += width ) {
 
         memset(line, 0, sizeof(line));
 
         int diff = memcmp(inA + i, inB + i, width);        
+
         // if ok,  just print
         if (diff == 0) {
             hex_to_buffer((uint8_t*)line, inA + i, width, width, 0, 1, true);
@@ -3143,20 +3143,17 @@ static int CmdDiff(const char *Cmd) {
                 char cb = inB[j];
 
                 if (inA[j] != inB[j]) {
-                    //PrintAndLogEx(INFO, "%02X -- %02X", inA[j], inB[j]);
-                    // diff
+
+                    // diff / add colors
                     sprintf(dlnA + strlen(dlnA), _GREEN_("%02X "), inA[j]);
                     sprintf(dlnB + strlen(dlnB), _RED_("%02X "), inB[j]);
-
                     sprintf(dlnAii + strlen(dlnAii), _GREEN_("%c"), ((ca < 32) || (ca == 127)) ? '.' : ca);
                     sprintf(dlnBii + strlen(dlnBii), _RED_("%c"), ((cb < 32) || (cb == 127)) ? '.' : cb);
 
                 } else {
-                    //PrintAndLogEx(INFO, "%02X ++ %02X", inA[j], inB[j]);
                     // normal
                     sprintf(dlnA + strlen(dlnA), "%02X ", inA[j]);
                     sprintf(dlnB + strlen(dlnB), "%02X ", inB[j]);
-
                     sprintf(dlnAii + strlen(dlnAii), "%c", ((ca < 32) || (ca == 127)) ? '.' : ca);
                     sprintf(dlnBii + strlen(dlnBii), "%c", ((cb < 32) || (cb == 127)) ? '.' : cb);
                 }
@@ -3166,6 +3163,49 @@ static int CmdDiff(const char *Cmd) {
         PrintAndLogEx(INFO, "%03X | %s", i, line);
     }
 
+    // mod
+
+
+    // print different length 
+    bool tallestA = (datalenA > datalenB); 
+    if (tallestA) {
+        n = datalenA;
+    } else {
+        n = datalenB;
+    }
+
+    // print data diff loop
+    for (; i < n;  i += width ) {
+
+        memset(line, 0, sizeof(line));
+
+        if (tallestA) {
+            hex_to_buffer((uint8_t*)line, inA + i, width, width, 0, 1, true);
+            ascii_to_buffer((uint8_t*)(line + strlen(line)), inA + i, width, width, 0);
+            strcat(line + strlen(line), " | ");
+            for (int j = 0; j < width; j++) {
+                strcat(line + strlen(line), "-- ");
+            }
+            for (int j = 0; j < width; j++) {
+                strcat(line + strlen(line), ".");
+            }
+        } else {
+
+            for (int j = 0; j < width; j++) {
+                strcat(line + strlen(line), "-- ");
+            }
+            for (int j = 0; j < width; j++) {
+                strcat(line + strlen(line), ".");
+            }
+            strcat(line + strlen(line), " | ");
+            hex_to_buffer((uint8_t*)(line + strlen(line)), inB + i, width, width, 0, 1, true);
+            ascii_to_buffer((uint8_t*)(line + strlen(line)), inB + i, width, width, 0);
+        }
+
+        PrintAndLogEx(INFO, "%03X | %s", i, line);
+    }
+
+    // footer
     PrintAndLogEx(INFO, hdr1);
     PrintAndLogEx(NORMAL, "");
 
