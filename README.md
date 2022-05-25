commit 59b86af1a8665250592f0f1deb03f20398125fa0
Author: iceman1001 <iceman@iuse.se>
Date:   Mon May 24 21:54:09 2021 +0200

    fix #1286 - wrong pwd/key length checks

diff --git a/client/src/cmdlfhitag.c b/client/src/cmdlfhitag.c
index bd246c281..232acf068 100644
--- a/client/src/cmdlfhitag.c
+++ b/client/src/cmdlfhitag.c
@@ -559,13 +559,13 @@ static int CmdLFHitagReader(const char *Cmd) {
     }
 
     // sanity checks
-    if (keylen != 0 && keylen != 6) {
-        PrintAndLogEx(WARNING, "Wrong KEY len expected 0 or 6, got %d", keylen);
+    if (keylen != 0 && keylen != 4) {
+        PrintAndLogEx(WARNING, "Wrong KEY len expected 0 or 4, got %d", keylen);
         return PM3_EINVARG;
     }
 
-    if (nalen != 0 && nalen != 8) {
-        PrintAndLogEx(WARNING, "Wrong NR/AR len expected 0 or 8, got %d", nalen);
+    if (nalen != 0 && nalen != 6) {
+        PrintAndLogEx(WARNING, "Wrong NR/AR len expected 0 or 6, got %d", nalen);
         return PM3_EINVARG;
     }
 
diff --git a/client/src/mifare/ndef.h b/client/src/mifare/ndef.h
index 12a6646e8..dc2f2aa87 100644
--- a/client/src/mifare/ndef.h
+++ b/client/src/mifare/ndef.h
@@ -11,6 +11,7 @@
 #ifndef _NDEF_H_
 #define _NDEF_H_
 
+#include <stdbool.h>
 #include "common.h"
 
 typedef enum {
