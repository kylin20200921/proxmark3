commit 712ee2b10e7af0c1a216e34dd4278a99ae013ee8
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Dec 13 18:44:46 2021 +0100

    strndup fixes for mingw

diff --git a/client/pyscripts/pm3_help2list.py b/client/pyscripts/pm3_help2list.py
index 15ead4198..ff24dda8d 100755
--- a/client/pyscripts/pm3_help2list.py
+++ b/client/pyscripts/pm3_help2list.py
@@ -12,8 +12,8 @@ This version
  - Iceman
 
 Note:
-    This file is used as a helper script to generate the rl_vocabulory.h file need.
-    It also needs a working proxmark3 client to extract the help text.
+    This script is used as a helper script to generate the rl_vocabulory.h file.
+    It need a working proxmark3 client to extract the help text.
 
     Ie: this script can't be used inside the normal build sequence.
 """
@@ -69,6 +69,7 @@ extern "C" {
 #include <string.h>
 #include <readline/readline.h>
 #include "ui.h"                          // g_session
+#include "util.h"                        // str_ndup
 
 char* rl_command_generator(const char *text, int state);
 char **rl_command_completion(const char *text, int start, int end);
@@ -124,9 +125,9 @@ char* rl_command_generator(const char *text, int state) {
             const char *next = command + (rlen - len);
             const char *space = strstr(next, " ");
             if (space != NULL) {
-                return strndup(next, space - next);
+                return str_ndup(next, space - next);
             }
-            return strdup(next);
+            return str_dup(next);
         }
     }
 
diff --git a/client/src/rl_vocabulory.h b/client/src/rl_vocabulory.h
index 0604969e7..b3f6f86c2 100644
--- a/client/src/rl_vocabulory.h
+++ b/client/src/rl_vocabulory.h
@@ -20,6 +20,7 @@ extern "C" {
 #include <string.h>
 #include <readline/readline.h>
 #include "ui.h"                          // g_session
+#include "util.h"                        // str_ndup
 
 char* rl_command_generator(const char *text, int state);
 char **rl_command_completion(const char *text, int start, int end);
@@ -76,6 +77,7 @@ const static vocabulory_t vocabulory[] = {
     { 1, "data fsktonrz" }, 
     { 1, "data manrawdecode" }, 
     { 1, "data modulation" }, 
+    { 1, "data pwmdemod" }, 
     { 1, "data rawdemod" }, 
     { 1, "data askedgedetect" }, 
     { 1, "data autocorr" }, 
@@ -734,9 +736,9 @@ char* rl_command_generator(const char *text, int state) {
             const char *next = command + (rlen - len);
             const char *space = strstr(next, " ");
             if (space != NULL) {
-                return strndup(next, space - next);
+                return str_ndup(next, space - next);
             }
-            return strdup(next);
+            return str_dup(next);
         }
     }
 
