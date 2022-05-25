commit af91f119d297c86c9d893ae052f6b4869ab5122e
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Sep 10 21:31:47 2021 +0200

    reversed when message is printed

diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index b1b76d3e5..983bd5799 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -525,9 +525,9 @@ plot:
 
     } while (continuous);
 
-    if (disconnectAfter) {
+    if (disconnectAfter == false) {
         if (silent == false) {
-            PrintAndLogEx(INFO, "field dropped.");
+            PrintAndLogEx(INFO, "field is on");
         }
     }
 
