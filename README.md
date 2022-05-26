commit 7fe8354b47a85d336ffacd45b3303ac5a6d7ae1e
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Mar 13 03:04:04 2022 +0100

    added ascii to diff command output

diff --git a/client/src/cmddata.c b/client/src/cmddata.c
index 0afb4a557..3df4d3193 100644
--- a/client/src/cmddata.c
+++ b/client/src/cmddata.c
@@ -3090,7 +3090,7 @@ static int CmdDiff(const char *Cmd) {
     if (inB == NULL)
         PrintAndLogEx(INFO, "inB null");
 
-    int hdr_sln = (width * 3) + 1;
+    int hdr_sln = (width * 4) + 2;
     PrintAndLogEx(INFO, "");
 
     char hdr0[200] = " #  | " _CYAN_("A");
@@ -3106,7 +3106,7 @@ static int CmdDiff(const char *Cmd) {
 
     // index 4bytes, spaces, bar, bytes with ansi codes
     // (16 * 2 * 8 ) + 32 
-    char line[500] = {0};
+    char line[800] = {0};
 
     // print data diff loop
     for (int i = 0; i < n;  i += width ) {
@@ -3117,30 +3117,48 @@ static int CmdDiff(const char *Cmd) {
         // if ok,  just print
         if (diff == 0) {
             hex_to_buffer((uint8_t*)line, inA + i, width, width, 0, 1, true);
-            strcat(line + strlen(line), "| ");
+            ascii_to_buffer((uint8_t*)(line + strlen(line)), inA + i, width, width, 0);
+            strcat(line + strlen(line), " | ");
             hex_to_buffer((uint8_t*)(line + strlen(line)), inB + i, width, width, 0, 1, true);
+            ascii_to_buffer((uint8_t*)(line + strlen(line)), inB + i, width, width, 0);
         } else {
 
             char dlnA[240] = {0};
             char dlnB[240] = {0};
+            char dlnAii[180] = {0};
+            char dlnBii[180] = {0};
+
             memset(dlnA, 0, sizeof(dlnA));
             memset(dlnB, 0, sizeof(dlnB));
+            memset(dlnAii, 0, sizeof(dlnAii));
+            memset(dlnBii, 0, sizeof(dlnBii));
+
             // if diff,  time to find it
             for (int j = i; j < (i + width); j++) {
+                    
+                char ca = inA[j];
+                char cb = inB[j];
 
                 if (inA[j] != inB[j]) {
                     //PrintAndLogEx(INFO, "%02X -- %02X", inA[j], inB[j]);
                     // diff
                     sprintf(dlnA + strlen(dlnA), _GREEN_("%02X "), inA[j]);
                     sprintf(dlnB + strlen(dlnB), _RED_("%02X "), inB[j]);
+
+                    sprintf(dlnAii + strlen(dlnAii), _GREEN_("%c"), ((ca < 32) || (ca == 127)) ? '.' : ca);
+                    sprintf(dlnBii + strlen(dlnBii), _RED_("%c"), ((cb < 32) || (cb == 127)) ? '.' : cb);
+
                 } else {
                     //PrintAndLogEx(INFO, "%02X ++ %02X", inA[j], inB[j]);
                     // normal
                     sprintf(dlnA + strlen(dlnA), "%02X ", inA[j]);
                     sprintf(dlnB + strlen(dlnB), "%02X ", inB[j]);
+
+                    sprintf(dlnAii + strlen(dlnAii), "%c", ((ca < 32) || (ca == 127)) ? '.' : ca);
+                    sprintf(dlnBii + strlen(dlnBii), "%c", ((cb < 32) || (cb == 127)) ? '.' : cb);
                 }
             }
-            sprintf(line, "%s| %s", dlnA, dlnB);
+            sprintf(line, "%s%s | %s%s", dlnA, dlnAii, dlnB, dlnBii);
         }
         PrintAndLogEx(INFO, "%03X | %s", i, line);
     }
diff --git a/client/src/util.c b/client/src/util.c
index bb517b5bb..94ecdd94f 100644
--- a/client/src/util.c
+++ b/client/src/util.c
@@ -148,17 +148,44 @@ bool CheckStringIsHEXValue(const char *value) {
     return true;
 }
 
-void hex_to_buffer(const uint8_t *buf, const uint8_t *hex_data, const size_t hex_len, const size_t hex_max_len,
+void ascii_to_buffer(uint8_t *buf, const uint8_t *hex_data, const size_t hex_len,
+                     const size_t hex_max_len, const size_t min_str_len) {
+
+    if (buf == NULL) return;
+
+    char *tmp = (char *)buf;
+    memset(tmp, 0x00, hex_max_len);
+
+    size_t max_len = (hex_len > hex_max_len) ? hex_max_len : hex_len;
+
+    size_t i = 0;
+    for (i = 0; i < max_len; ++i, tmp++) {
+        char c = hex_data[i];
+        sprintf(tmp, "%c", ((c < 32) || (c == 127)) ? '.' : c);
+    }
+
+    size_t m = (min_str_len > i) ? min_str_len : 0;
+    if (m > hex_max_len)
+        m = hex_max_len;
+
+    for (; i < m; i++, tmp++)
+        sprintf(tmp, " ");
+
+    // remove last space
+    *tmp = '\0';
+}
+
+void hex_to_buffer(uint8_t *buf, const uint8_t *hex_data, const size_t hex_len, const size_t hex_max_len,
                    const size_t min_str_len, const size_t spaces_between, bool uppercase) {
 
     if (buf == NULL) return;
 
     char *tmp = (char *)buf;
-    size_t i;
     memset(tmp, 0x00, hex_max_len);
 
     size_t max_len = (hex_len > hex_max_len) ? hex_max_len : hex_len;
 
+    size_t i;
     for (i = 0; i < max_len; ++i, tmp += 2 + spaces_between) {
         sprintf(tmp, (uppercase) ? "%02X" : "%02x", (unsigned int) hex_data[i]);
 
@@ -168,16 +195,15 @@ void hex_to_buffer(const uint8_t *buf, const uint8_t *hex_data, const size_t hex
 
     i *= (2 + spaces_between);
 
-    size_t mlen = min_str_len > i ? min_str_len : 0;
-    if (mlen > hex_max_len)
-        mlen = hex_max_len;
+    size_t m = min_str_len > i ? min_str_len : 0;
+    if (m > hex_max_len)
+        m = hex_max_len;
 
-    for (; i < mlen; i++, tmp += 1)
+    for (; i < m; i++, tmp++)
         sprintf(tmp, " ");
 
     // remove last space
     *tmp = '\0';
-    return;
 }
 
 // printing and converting functions
diff --git a/client/src/util.h b/client/src/util.h
index e24dacc3d..fa9b0eb94 100644
--- a/client/src/util.h
+++ b/client/src/util.h
@@ -59,7 +59,9 @@ void FillFileNameByUID(char *filenamePrefix, const uint8_t *uid, const char *ext
 int FillBuffer(uint8_t *data, size_t maxDataLength, size_t *dataLength, ...);
 
 bool CheckStringIsHEXValue(const char *value);
-void hex_to_buffer(const uint8_t *buf, const uint8_t *hex_data, const size_t hex_len,
+void ascii_to_buffer(uint8_t *buf, const uint8_t *hex_data, const size_t hex_len,
+                   const size_t hex_max_len, const size_t min_str_len);
+void hex_to_buffer(uint8_t *buf, const uint8_t *hex_data, const size_t hex_len,
                    const size_t hex_max_len, const size_t min_str_len, const size_t spaces_between,
                    bool uppercase);
 
