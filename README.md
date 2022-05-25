commit 8edcfb925beb45bbe1e5d0ae29eb5afe8c324246
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Aug 23 07:25:25 2021 +0200

    text

diff --git a/client/src/cmdhflist.c b/client/src/cmdhflist.c
index 74e5c3aec..8e1c010c7 100644
--- a/client/src/cmdhflist.c
+++ b/client/src/cmdhflist.c
@@ -345,9 +345,11 @@ int applyIso14443a(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize) {
             break;
         }
         case NTAG_I2C_FASTWRITE: 
-            if (cmdsize == 69) {
+            if (cmdsize == 69)
                 snprintf(exp, size, "FAST WRITE (%d - %d)", cmd[1], cmd[2]);
-            }
+            else
+                snprintf(exp, size, "?");
+            
             break;
         default:
             return 0;
