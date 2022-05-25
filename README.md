commit 16783000f0d85d6c581da306cc7c2484d048ab66
Author: iceman1001 <iceman@iuse.se>
Date:   Sun May 16 23:03:13 2021 +0200

    rework some defines

diff --git a/client/src/cmdlfem4x50.c b/client/src/cmdlfem4x50.c
index 545544b15..149ba0b42 100644
--- a/client/src/cmdlfem4x50.c
+++ b/client/src/cmdlfem4x50.c
@@ -18,8 +18,7 @@
 #include "commonutil.h"
 #include "pmflash.h"
 #include "cmdflashmemspiffs.h"
-
-#define BYTES2UINT32(x) ((x[0] << 24) | (x[1] << 16) | (x[2] << 8) | (x[3]))
+#include "em4x50.h"
 
 static int CmdHelp(const char *Cmd);
 
@@ -465,12 +464,12 @@ int CmdEM4x50Chk(const char *Cmd) {
     }
 
     size_t datalen = 0;
-    uint8_t data[FLASH_MEM_MAX_SIZE] = {0x0};
+    uint8_t data[100000] = {0x0};
     uint8_t *keys = data;
     uint32_t key_count = 0;
 
     int res = loadFileDICTIONARY(filename, data, &datalen, 4, &key_count);
-    if (res || !key_count)
+    if ((res != PM3_SUCCESS) || (key_count == 0))
         return PM3_EFILE;
 
     PrintAndLogEx(INFO, "You can cancel this operation by pressing the pm3 button");
diff --git a/client/src/cmdlfem4x70.c b/client/src/cmdlfem4x70.c
index 032daf4b2..5c7d0d06a 100644
--- a/client/src/cmdlfem4x70.c
+++ b/client/src/cmdlfem4x70.c
@@ -19,9 +19,6 @@
 #define LOCKBIT_0 BITMASK(6)
 #define LOCKBIT_1 BITMASK(7)
 
-#define BYTES2UINT16(x) ((x[1] << 8) | (x[0]))
-#define BYTES2UINT32(x) ((x[3] << 24) | (x[2] << 16) | (x[1] << 8) | (x[0]))
-
 #define INDEX_TO_BLOCK(x) (((32-x)/2)-1)
 
 static int CmdHelp(const char *Cmd);
diff --git a/include/common.h b/include/common.h
index f0ba424c8..50d0aed69 100644
--- a/include/common.h
+++ b/include/common.h
@@ -131,6 +131,15 @@ extern bool tearoff_enabled;
 #endif
 #endif
 
+// convert 2 bytes to U16
+#ifndef BYTES2UINT16
+# define BYTES2UINT16(x) ((x[1] << 8) | (x[0]))
+#endif
+// convert 4 bytes to U32
+#ifndef BYTES2UINT32
+# define BYTES2UINT32(x) ((x[3] << 24) | (x[2] << 16) | (x[1] << 8) | (x[0]))
+#endif
+
 #define EVEN                        0
 #define ODD                         1
 
diff --git a/include/em4x50.h b/include/em4x50.h
index 45ad04467..ac8fb0bfd 100644
--- a/include/em4x50.h
+++ b/include/em4x50.h
@@ -11,6 +11,8 @@
 #ifndef EM4X50_H__
 #define EM4X50_H__
 
+#include "common.h"
+
 #define EM4X50_NO_WORDS             34
 
 // special words
@@ -45,8 +47,6 @@
 #define TIMEOUT_CMD                 3000
 #define DUMP_FILESIZE               136
 
-#define BYTES2UINT32(x) ((x[0] << 24) | (x[1] << 16) | (x[2] << 8) | (x[3]))
-
 typedef struct {
     bool addr_given;
     bool pwd_given;
