commit 81c6918ac582d1cd9643404b9e545b5e214eb7a1
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Jan 6 23:25:25 2022 +0100

    cppcheck

diff --git a/client/deps/hardnested/hardnested_tables.c b/client/deps/hardnested/hardnested_tables.c
index 1ab600275..b124345d0 100644
--- a/client/deps/hardnested/hardnested_tables.c
+++ b/client/deps/hardnested/hardnested_tables.c
@@ -93,41 +93,49 @@ static inline void clear_bitarray24(uint32_t *bitarray) {
     memset(bitarray, 0x00, sizeof(uint32_t) * (1 << 19));
 }
 
-
-static inline uint32_t test_bit24(uint32_t *bitarray, uint32_t index) {
+static inline uint32_t test_bit24(const uint32_t *bitarray, uint32_t index) {
     return bitarray[index >> 5] & (0x80000000 >> (index & 0x0000001f));
 }
 
-
 static inline void set_bit24(uint32_t *bitarray, uint32_t index) {
     bitarray[index >> 5] |= 0x80000000 >> (index & 0x0000001f);
 }
 
+static inline uint32_t next_state(const uint32_t *bitset, uint32_t state) {
+    if (++state == 1 << 24) {
+        return 1 << 24;
+    }
 
-static inline uint32_t next_state(uint32_t *bitset, uint32_t state) {
-    if (++state == 1 << 24) return 1 << 24;
     uint32_t index = state >> 5;
     uint_fast8_t bit = state & 0x1f;
     uint32_t line = bitset[index] << bit;
     while (bit <= 0x1f) {
-        if (line & 0x80000000) return state;
+        if (line & 0x80000000) {
+            return state;
+        }
         state++;
         bit++;
         line <<= 1;
     }
+
     index++;
     while (bitset[index] == 0x00000000 && state < 1 << 24) {
         index++;
         state += 0x20;
     }
-    if (state >= 1 << 24) return 1 << 24;
+
+    if (state >= 1 << 24) {
+        return 1 << 24;
+    }
 #if defined __GNUC__
     return state + __builtin_clz(bitset[index]);
 #else
     bit = 0x00;
     line = bitset[index];
     while (bit <= 0x1f) {
-        if (line & 0x80000000) return state;
+        if (line & 0x80000000) {
+            return state;
+        }
         state++;
         bit++;
         line <<= 1;
@@ -137,7 +145,7 @@ static inline uint32_t next_state(uint32_t *bitset, uint32_t state) {
 }
 
 
-static inline uint32_t next_not_state(uint32_t *bitset, uint32_t state) {
+static inline uint32_t next_not_state(const uint32_t *bitset, uint32_t state) {
     if (++state == 1 << 24) return 1 << 24;
     uint32_t index = state >> 5;
     uint_fast8_t bit = state & 0x1f;
diff --git a/client/src/fileutils.c b/client/src/fileutils.c
index b026849b2..c38613959 100644
--- a/client/src/fileutils.c
+++ b/client/src/fileutils.c
@@ -686,7 +686,7 @@ int saveFileJSONrootEx(const char *preferredName, void *root, size_t flags, bool
     return PM3_EFILE;
 }
 
-int saveFileWAVE(const char *preferredName, int *data, size_t datalen) {
+int saveFileWAVE(const char *preferredName, const int *data, size_t datalen) {
 
     if (data == NULL) return PM3_EINVARG;
     char *fileName = newfilenamemcopy(preferredName, ".wav");
diff --git a/client/src/fileutils.h b/client/src/fileutils.h
index 616cba740..5e0e3a971 100644
--- a/client/src/fileutils.h
+++ b/client/src/fileutils.h
@@ -127,7 +127,7 @@ int saveFileJSONrootEx(const char *preferredName, void *root, size_t flags, bool
  * @param datalen the length of the data
  * @return 0 for ok
  */
-int saveFileWAVE(const char *preferredName, int *data, size_t datalen);
+int saveFileWAVE(const char *preferredName, const int *data, size_t datalen);
 
 /** STUB
  * @brief Utility function to save PM3 data to a file. This method takes a preferred name, but if that
diff --git a/client/src/graph.c b/client/src/graph.c
index a357fb093..5f0f5ef94 100644
--- a/client/src/graph.c
+++ b/client/src/graph.c
@@ -72,8 +72,8 @@ void save_restoreGB(uint8_t saveOpt) {
     }
 }
 
-void setGraphBuf(uint8_t *buff, size_t size) {
-    if (buff == NULL) return;
+void setGraphBuf(const uint8_t *src, size_t size) {
+    if (src == NULL) return;
 
     ClearGraph(false);
 
@@ -81,14 +81,14 @@ void setGraphBuf(uint8_t *buff, size_t size) {
         size = MAX_GRAPH_TRACE_LEN;
 
     for (size_t i = 0; i < size; ++i)
-        g_GraphBuffer[i] = buff[i] - 128;
+        g_GraphBuffer[i] = src[i] - 128;
 
     g_GraphTraceLen = size;
     RepaintGraphWindow();
 }
 
-size_t getFromGraphBuf(uint8_t *buff) {
-    if (buff == NULL) return 0;
+size_t getFromGraphBuf(uint8_t *dest) {
+    if (dest == NULL) return 0;
     if (g_GraphTraceLen == 0) return 0;
 
     size_t i;
@@ -96,7 +96,7 @@ size_t getFromGraphBuf(uint8_t *buff) {
         //trim
         if (g_GraphBuffer[i] > 127) g_GraphBuffer[i] = 127;
         if (g_GraphBuffer[i] < -127) g_GraphBuffer[i] = -127;
-        buff[i] = (uint8_t)(g_GraphBuffer[i] + 128);
+        dest[i] = (uint8_t)(g_GraphBuffer[i] + 128);
     }
     return i;
 }
diff --git a/client/src/graph.h b/client/src/graph.h
index 04815d52c..5649371a8 100644
--- a/client/src/graph.h
+++ b/client/src/graph.h
@@ -20,9 +20,9 @@ extern "C" {
 void AppendGraph(bool redraw, uint16_t clock, int bit);
 size_t ClearGraph(bool redraw);
 bool HasGraphData(void);
-void setGraphBuf(uint8_t *buff, size_t size);
+void setGraphBuf(const uint8_t *src, size_t size);
 void save_restoreGB(uint8_t saveOpt);
-size_t getFromGraphBuf(uint8_t *buff);
+size_t getFromGraphBuf(uint8_t *dest);
 void convertGraphFromBitstream(void);
 void convertGraphFromBitstreamEx(int hi, int low);
 bool isGraphBitstream(void);
