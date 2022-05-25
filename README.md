commit 88d9942d3f4a8cde819ee4392077a0c00f076ab3
Author: iceman1001 <iceman@iuse.se>
Date:   Tue Jun 22 07:49:17 2021 +0200

    fix little endian vs big endian in the macros

diff --git a/client/src/cmdlfem4x50.c b/client/src/cmdlfem4x50.c
index 149ba0b42..2e85786eb 100644
--- a/client/src/cmdlfem4x50.c
+++ b/client/src/cmdlfem4x50.c
@@ -464,7 +464,9 @@ int CmdEM4x50Chk(const char *Cmd) {
     }
 
     size_t datalen = 0;
-    uint8_t data[100000] = {0x0};
+
+    // 2021 iceman: how many keys shall we reserv space for? The t55xx dictionary has 139 keys.
+    uint8_t data[2000 * 4] = {0x0};
     uint8_t *keys = data;
     uint32_t key_count = 0;
 
diff --git a/client/src/cmdlfem4x70.c b/client/src/cmdlfem4x70.c
index 5c7d0d06a..59584a2bc 100644
--- a/client/src/cmdlfem4x70.c
+++ b/client/src/cmdlfem4x70.c
@@ -251,7 +251,7 @@ int CmdEM4x70Unlock(const char *Cmd) {
         return PM3_EINVARG;
     }
 
-    etd.pin = BYTES2UINT32(pin);
+    etd.pin = BYTES2UINT32_BE(pin);
 
     clearCommandBuffer();
     SendCommandNG(CMD_LF_EM4X70_UNLOCK, (uint8_t *)&etd, sizeof(etd));
@@ -370,7 +370,7 @@ int CmdEM4x70WritePIN(const char *Cmd) {
         return PM3_EINVARG;
     }
 
-    etd.pin = BYTES2UINT32(pin);
+    etd.pin = BYTES2UINT32_BE(pin);
 
     clearCommandBuffer();
     SendCommandNG(CMD_LF_EM4X70_WRITEPIN, (uint8_t *)&etd, sizeof(etd));
diff --git a/include/common.h b/include/common.h
index 50d0aed69..95784acee 100644
--- a/include/common.h
+++ b/include/common.h
@@ -131,15 +131,21 @@ extern bool tearoff_enabled;
 #endif
 #endif
 
-// convert 2 bytes to U16
+// convert 2 bytes to U16 in little endian
 #ifndef BYTES2UINT16
 # define BYTES2UINT16(x) ((x[1] << 8) | (x[0]))
 #endif
-// convert 4 bytes to U32
+// convert 4 bytes to U32 in little endian
 #ifndef BYTES2UINT32
 # define BYTES2UINT32(x) ((x[3] << 24) | (x[2] << 16) | (x[1] << 8) | (x[0]))
 #endif
 
+// convert 4 bytes to U32 in big endian
+#ifndef BYTES2UINT32_BE
+# define BYTES2UINT32_BE(x) ((x[0] << 24) | (x[1] << 16) | (x[2] << 8) | (x[3]))
+#endif
+
+
 #define EVEN                        0
 #define ODD                         1
 
