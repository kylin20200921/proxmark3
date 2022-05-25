commit 083e557b9ffbd95b82b24d36e28f403b9b4b8487
Author: iceman1001 <iceman@iuse.se>
Date:   Tue Jun 22 07:53:00 2021 +0200

    fix little endian vs big endian in the macros...  again... but this time correct

diff --git a/client/src/cmdlfem4x50.c b/client/src/cmdlfem4x50.c
index 2e85786eb..89ee9efb0 100644
--- a/client/src/cmdlfem4x50.c
+++ b/client/src/cmdlfem4x50.c
@@ -344,7 +344,7 @@ int CmdEM4x50Login(const char *Cmd) {
         return PM3_EINVARG;
     }
 
-    uint32_t password = BYTES2UINT32(pwd);
+    uint32_t password = BYTES2UINT32_BE(pwd);
 
     // start
     clearCommandBuffer();
@@ -395,8 +395,8 @@ int CmdEM4x50Brute(const char *Cmd) {
     }
 
     em4x50_data_t etd;
-    etd.password1 = BYTES2UINT32(first);
-    etd.password2 = BYTES2UINT32(last);
+    etd.password1 = BYTES2UINT32_BE(first);
+    etd.password2 = BYTES2UINT32_BE(last);
 
     // 27 passwords/second (empirical value)
     const int speed = 27;
@@ -624,7 +624,7 @@ int CmdEM4x50Read(const char *Cmd) {
             PrintAndLogEx(FAILED, "password length must be 4 bytes instead of %d", pwd_len);
             return PM3_EINVARG;
         } else {
-            etd.password1 = BYTES2UINT32(pwd);
+            etd.password1 = BYTES2UINT32_BE(pwd);
             etd.pwd_given = true;
         }
     }
@@ -663,7 +663,7 @@ int CmdEM4x50Info(const char *Cmd) {
             PrintAndLogEx(FAILED, "password length must be 4 bytes instead of %d", pwd_len);
             return PM3_EINVARG;
         } else {
-            etd.password1 = BYTES2UINT32(pwd);
+            etd.password1 = BYTES2UINT32_BE(pwd);
             etd.pwd_given = true;
         }
     }
@@ -775,7 +775,7 @@ int CmdEM4x50Dump(const char *Cmd) {
             CLIParserFree(ctx);
             return PM3_EINVARG;
         } else {
-            etd.password1 = BYTES2UINT32(pwd);
+            etd.password1 = BYTES2UINT32_BE(pwd);
             etd.pwd_given = true;
         }
     }
@@ -867,14 +867,14 @@ int CmdEM4x50Write(const char *Cmd) {
             PrintAndLogEx(FAILED, "password length must be 4 bytes instead of %d", pwd_len);
             return PM3_EINVARG;
         } else {
-            etd.password1 = BYTES2UINT32(pwd);
+            etd.password1 = BYTES2UINT32_BE(pwd);
             etd.pwd_given = true;
         }
     }
 
     etd.addresses = (addr << 8) | addr;
     etd.addr_given = true;
-    etd.word = BYTES2UINT32(word);
+    etd.word = BYTES2UINT32_BE(word);
 
     clearCommandBuffer();
     SendCommandNG(CMD_LF_EM4X50_WRITE, (uint8_t *)&etd, sizeof(etd));
@@ -936,14 +936,14 @@ int CmdEM4x50WritePwd(const char *Cmd) {
         PrintAndLogEx(FAILED, "password length must be 4 bytes instead of %d", pwd_len);
         return PM3_EINVARG;
     } else {
-        etd.password1 = BYTES2UINT32(pwd);
+        etd.password1 = BYTES2UINT32_BE(pwd);
     }
 
     if (npwd_len != 4) {
         PrintAndLogEx(FAILED, "password length must be 4 bytes instead of %d", npwd_len);
         return PM3_EINVARG;
     } else {
-        etd.password2 = BYTES2UINT32(npwd);
+        etd.password2 = BYTES2UINT32_BE(npwd);
     }
 
     PacketResponseNG resp;
@@ -999,7 +999,7 @@ int CmdEM4x50Wipe(const char *Cmd) {
 
     em4x50_data_t etd = {.pwd_given = false, .word = 0x0, .password2 = 0x0};
 
-    etd.password1 = BYTES2UINT32(pwd);
+    etd.password1 = BYTES2UINT32_BE(pwd);
     etd.pwd_given = true;
 
     // clear password
@@ -1095,7 +1095,7 @@ int CmdEM4x50Restore(const char *Cmd) {
             PrintAndLogEx(FAILED, "password length must be 4 bytes instead of %d", pwd_len);
             return PM3_EINVARG;
         } else {
-            etd.password1 = BYTES2UINT32(pwd);
+            etd.password1 = BYTES2UINT32_BE(pwd);
             etd.pwd_given = true;
             // if password is available protection and control word can be restored
             startblock = EM4X50_PROTECTION;
@@ -1122,7 +1122,7 @@ int CmdEM4x50Restore(const char *Cmd) {
         PrintAndLogEx(INPLACE, "Restoring block %i", i);
 
         etd.addresses = i << 8 | i;
-        etd.word = reflect32(BYTES2UINT32((data + 4 * i)));
+        etd.word = reflect32(BYTES2UINT32_BE((data + 4 * i)));
 
         PacketResponseNG resp;
         clearCommandBuffer();
@@ -1172,7 +1172,7 @@ int CmdEM4x50Sim(const char *Cmd) {
             PrintAndLogEx(FAILED, "password length must be 4 bytes, got %d", pwd_len);
             return PM3_EINVARG;
         } else {
-            password = BYTES2UINT32(pwd);
+            password = BYTES2UINT32_BE(pwd);
         }
     }
 
diff --git a/client/src/cmdlfem4x70.c b/client/src/cmdlfem4x70.c
index 59584a2bc..5c7d0d06a 100644
--- a/client/src/cmdlfem4x70.c
+++ b/client/src/cmdlfem4x70.c
@@ -251,7 +251,7 @@ int CmdEM4x70Unlock(const char *Cmd) {
         return PM3_EINVARG;
     }
 
-    etd.pin = BYTES2UINT32_BE(pin);
+    etd.pin = BYTES2UINT32(pin);
 
     clearCommandBuffer();
     SendCommandNG(CMD_LF_EM4X70_UNLOCK, (uint8_t *)&etd, sizeof(etd));
@@ -370,7 +370,7 @@ int CmdEM4x70WritePIN(const char *Cmd) {
         return PM3_EINVARG;
     }
 
-    etd.pin = BYTES2UINT32_BE(pin);
+    etd.pin = BYTES2UINT32(pin);
 
     clearCommandBuffer();
     SendCommandNG(CMD_LF_EM4X70_WRITEPIN, (uint8_t *)&etd, sizeof(etd));
