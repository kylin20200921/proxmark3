commit 3bcef75da657bca35fcbb47fb500719ae3e08cd8
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 8 13:30:24 2022 +0100

    coverity fix for the return type

diff --git a/include/iso14b.h b/include/iso14b.h
index 90e521b23..432baac0c 100644
--- a/include/iso14b.h
+++ b/include/iso14b.h
@@ -60,10 +60,10 @@ typedef struct {
 #define US_TO_SSP(x)   ( (uint32_t)((x) * 3.39) )
 #define SSP_TO_US(x)   ( (uint32_t)((x) / 3.39) )
 
-#define ETU_TO_SSP(x)  ((x) * 32)
-#define SSP_TO_ETU(x)  ((x) / 32)
+#define ETU_TO_SSP(x)  ((uint32_t)((x) * 32))
+#define SSP_TO_ETU(x)  ((uint32_t)((x) / 32))
 
-#define ETU_TO_US(x)   ((((x) * 9440000) / 1000000) + 0.5)
-#define US_TO_ETU(x)   ((((x) * 1000000 / 9440000) + 0.5))
+#define ETU_TO_US(x)   ((uint32_t)((((x) * 9440000) / 1000000) + 0.5))
+#define US_TO_ETU(x)   ((uint32_t)(((x) * 1000000 / 9440000) + 0.5))
 
 #endif // _ISO14B_H_
