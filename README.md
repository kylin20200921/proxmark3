commit 36e86b1ce02ef6e126f781aa1fd0197207fed312
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Sep 10 21:02:33 2021 +0200

    text

diff --git a/client/src/cmdhficlass.c b/client/src/cmdhficlass.c
index 15fb5d72f..415aec9d5 100644
--- a/client/src/cmdhficlass.c
+++ b/client/src/cmdhficlass.c
@@ -2478,7 +2478,10 @@ void printIclassDumpContents(uint8_t *iclass_dump, uint8_t startblock, uint8_t e
     PrintAndLogEx(NORMAL, "");
     PrintAndLogEx(INFO, " block#  | data                    | ascii    |lck| info");
     PrintAndLogEx(INFO, "---------+-------------------------+----------+---+--------------");
-    PrintAndLogEx(INFO, "  0/0x00 | " _GREEN_("%s") " |   | CSN ", sprint_hex_ascii(iclass_dump, 8));
+    PrintAndLogEx(INFO, "  0/0x00 | " _GREEN_("%s") "| " _GREEN_("%s") " |   | CSN "
+        , sprint_hex(iclass_dump, 8)
+        , sprint_ascii(iclass_dump, 8)
+        );
 
     if (i != 1)
         PrintAndLogEx(INFO, "....");
@@ -2531,14 +2534,32 @@ void printIclassDumpContents(uint8_t *iclass_dump, uint8_t startblock, uint8_t e
                 s = info_nonks[i];
             }
 
-            PrintAndLogEx(INFO, "%3d/0x%02X | %s | %s | %s ", i, i, sprint_hex_ascii(blk, 8), lockstr, s);
+            PrintAndLogEx(INFO, "%3d/0x%02X | %s | %s | %s "
+                , i
+                , i
+                , sprint_hex_ascii(blk, 8)
+                , lockstr
+                , s
+                );
+
         } else {
             const char *info_ks[] = {"CSN", "Config", "E-purse", "Debit", "Credit", "AIA", "User"};
             const char *s = info_ks[6];
             if (i < 6) {
                 s = info_ks[i];
             }
-            PrintAndLogEx(INFO, "%3d/0x%02X | %s | %s | %s ", i, i, sprint_hex_ascii(blk, 8), lockstr, s);
+            if (i >= 6 && i <= 9) {
+                PrintAndLogEx(INFO, "%3d/0x%02X | " _YELLOW_("%s") "| " _YELLOW_("%s") " | %s | %s "
+                    , i
+                    , i
+                    , sprint_hex(blk, 8)
+                    , sprint_ascii(blk, 8)
+                    , lockstr
+                    , s
+                    );
+            } else {
+                PrintAndLogEx(INFO, "%3d/0x%02X | %s | %s | %s ", i, i, sprint_hex_ascii(blk, 8), lockstr, s);
+            }
         }
         i++;
     }
