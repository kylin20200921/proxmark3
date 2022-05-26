commit a699c0b5ddea39ebeb562a51188fbdc8c741ccab
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Feb 28 05:38:11 2022 +0100

    text

diff --git a/client/src/cmdhw.c b/client/src/cmdhw.c
index 28976638e..a2fe65890 100644
--- a/client/src/cmdhw.c
+++ b/client/src/cmdhw.c
@@ -1157,8 +1157,8 @@ void pm3_version_short(void) {
 
             if (armsrc_mismatch) {
                 PrintAndLogEx(NORMAL, "");
-                PrintAndLogEx(WARNING, "  --> " _RED_("ARM firmware does not match the source at the time the client was compiled"));
-                PrintAndLogEx(WARNING,  " --> Make sure to flash a correct and up-to-date version");
+                PrintAndLogEx(WARNING, " --> " _RED_("ARM firmware does not match the source at the time the client was compiled"));
+                PrintAndLogEx(WARNING, " --> Make sure to flash a correct and up-to-date version");
             }
         }
     }
