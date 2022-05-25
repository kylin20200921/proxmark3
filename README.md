commit e37a1a81ec3dd636c869b8452d36bed2544c7648
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Dec 27 19:24:52 2021 +0100

    remove arpa.inet dependency

diff --git a/client/src/ksx6924/ksx6924core.c b/client/src/ksx6924/ksx6924core.c
index 8685934b0..631d94771 100644
--- a/client/src/ksx6924/ksx6924core.c
+++ b/client/src/ksx6924/ksx6924core.c
@@ -21,12 +21,6 @@
 
 #include "ksx6924core.h"
 
-#ifdef _WIN32
-# include <winsock2.h> // ntohl
-#else
-# include <arpa/inet.h> // ntohl
-#endif
-
 #include <string.h>
 #include "emv/emvcore.h"
 #include "iso7816/apduinfo.h"
@@ -34,7 +28,8 @@
 #include "protocols.h"
 #include "ui.h"
 #include "util.h"
-#include "comms.h"        // clearCommandBuffer
+#include "comms.h"         // clearCommandBuffer
+#include "commonutil.h"    // ntohl (pm3 version)
 
 // Date type. This is the actual on-card format.
 typedef struct {
@@ -299,8 +294,8 @@ bool KSX6924ParsePurseInfo(const uint8_t *purseInfo, size_t purseLen, struct ksx
     convert_internal_date(internalPurseInfo->issueDate, &(ret->issueDate));
     convert_internal_date(internalPurseInfo->expiryDate, &(ret->expiryDate));
 
-    ret->balMax = ntohl(*(uint32_t *)(internalPurseInfo->balMax));
-    ret->mmax = ntohl(*(uint32_t *)(internalPurseInfo->mmax));
+    ret->balMax = MemBeToUint4byte((uint8_t*)internalPurseInfo->balMax);
+    ret->mmax = MemBeToUint4byte((uint8_t*)internalPurseInfo->mmax);
 
     memcpy(&ret->rfu, &internalPurseInfo->rfu, 8);
 
@@ -435,7 +430,7 @@ bool KSX6924GetBalance(uint32_t *result) {
         return false;
     }
 
-    *result = ntohl(*(uint32_t *)(arr));
+    *result =  MemBeToUint4byte((uint8_t*)arr);
     return true;   
 }
 
