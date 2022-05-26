commit fb91a1ca8802a3db264896ae947cd02df5068444
Author: iceman1001 <iceman@iuse.se>
Date:   Mon May 9 00:19:57 2022 +0200

    iclass print data now details legcay vs sio credential

diff --git a/client/src/cmdhficlass.c b/client/src/cmdhficlass.c
index 6586c9338..849c16c76 100644
--- a/client/src/cmdhficlass.c
+++ b/client/src/cmdhficlass.c
@@ -2571,6 +2571,8 @@ void printIclassDumpContents(uint8_t *iclass_dump, uint8_t startblock, uint8_t e
     if (i != 1)
         PrintAndLogEx(INFO, "  ......");
 
+    uint8_t fb = (iclass_dump + (6 * 8))[0];
+
     while (i <= endblock) {
         uint8_t *blk = iclass_dump + (i * 8);
 
@@ -2629,26 +2631,38 @@ void printIclassDumpContents(uint8_t *iclass_dump, uint8_t startblock, uint8_t e
 
         } else {
             const char *info_ks[] = {"CSN", "Config", "E-purse", "Debit", "Credit", "AIA", "User"};
-            const char *s = info_ks[6];
-            if (i < 6) {
-                s = info_ks[i];
-            }
-            if (i >= 6 && i <= 9) {
-                PrintAndLogEx(INFO, "%3d/0x%02X | " _YELLOW_("%s") "| " _YELLOW_("%s") " | %s | %s "
+
+            if (i >= 6 && i <= 9 && fb != 0x30) {
+                // legacy credential
+                PrintAndLogEx(INFO, "%3d/0x%02X | " _YELLOW_("%s") "| " _YELLOW_("%s") " | %s | User / Cred "
+                              , i
+                              , i
+                              , sprint_hex(blk, 8)
+                              , sprint_ascii(blk, 8)
+                              , lockstr
+                             );
+            } else if (i >= 6 && i <= 11 && fb == 0x30) {
+                // SIO credential
+                PrintAndLogEx(INFO, "%3d/0x%02X | " _CYAN_("%s") "| " _CYAN_("%s") " | %s | User / SIO"
                               , i
                               , i
                               , sprint_hex(blk, 8)
                               , sprint_ascii(blk, 8)
                               , lockstr
-                              , s
                              );
             } else {
+                const char *s = info_ks[6];
+                if (i < 6) {
+                    s = info_ks[i];
+                }
                 PrintAndLogEx(INFO, "%3d/0x%02X | %s | %s | %s ", i, i, sprint_hex_ascii(blk, 8), lockstr, s);
             }
         }
         i++;
     }
     PrintAndLogEx(INFO, "---------+-------------------------+----------+---+--------------");
+    PrintAndLogEx(HINT, _YELLOW_("yellow") " = legacy credential");
+    PrintAndLogEx(HINT, _CYAN_("cyan") " = SIO credential");
     PrintAndLogEx(NORMAL, "");
 }
 
