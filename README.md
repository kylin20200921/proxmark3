commit d1de0c99ea2a389cd59718121409695a0d0295ba
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Oct 17 11:39:19 2021 +0200

    trace list -t 14a - annotate ecp

diff --git a/client/src/cmdhflist.c b/client/src/cmdhflist.c
index b62da7c23..4380b5500 100644
--- a/client/src/cmdhflist.c
+++ b/client/src/cmdhflist.c
@@ -174,6 +174,11 @@ int applyIso14443a(char *exp, size_t size, uint8_t *cmd, uint8_t cmdsize, bool i
             return 1;
         }
 
+        if (cmdsize > 10 && (memcmp(cmd, "\x6a\x02\xC8\x01\x00\x03\x00\x02\x79", 9) == 0)) {
+            snprintf(exp, size, "ECP");
+            return 1;
+        }
+
         gs_ntag_i2c_state = 0;
         switch (cmd[0]) {
             case ISO14443A_CMD_WUPA:
