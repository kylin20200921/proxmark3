commit 451de246b70e53ea84851653d8134f8898a08004
Author: iceman1001 <iceman@iuse.se>
Date:   Sun May 16 23:01:37 2021 +0200

    if client wants to know how much SPIFFS

diff --git a/include/pmflash.h b/include/pmflash.h
index 4c970a628..c2f90bfc1 100644
--- a/include/pmflash.h
+++ b/include/pmflash.h
@@ -35,7 +35,6 @@
 # define FLASH_MEM_MAX_4K_SECTOR   0x3F000
 #endif
 
-
 #ifndef FLASH_MEM_ID_LEN
 # define FLASH_MEM_ID_LEN 8
 #endif
@@ -79,4 +78,9 @@ typedef struct {
     uint8_t signature[FLASH_MEM_SIGNATURE_LEN];
 } PACKED rdv40_validation_t;
 
+// SPIFFS current allocates 128kb of the 256kb available.
+#ifndef FLASH_SPIFFS_ALLOCATED_SIZE
+# define FLASH_SPIFFS_ALLOCATED_SIZE (1024 * 128)
+#endif
+
 #endif // __PMFLASH_H
