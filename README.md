commit 23190a0070a76d34588b57ad4e1d9193d9935129
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Aug 22 21:35:02 2021 +0200

    wrong size

diff --git a/client/src/cmdhflist.c b/client/src/cmdhflist.c
index 55f85d99b..74e5c3aec 100644
--- a/client/src/cmdhflist.c
+++ b/client/src/cmdhflist.c
@@ -345,7 +345,7 @@ int applyIso14443a(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize) {
             break;
         }
         case NTAG_I2C_FASTWRITE: 
-            if (size == 69) {
+            if (cmdsize == 69) {
                 snprintf(exp, size, "FAST WRITE (%d - %d)", cmd[1], cmd[2]);
             }
             break;
