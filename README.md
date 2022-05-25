commit dc29274cb38d1d02537f875fd7206bbd65ecebfa
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Dec 31 11:17:23 2021 +0100

    text

diff --git a/doc/colors_notes.md b/doc/colors_notes.md
index 38a3e9ede..182383cf3 100644
--- a/doc/colors_notes.md
+++ b/doc/colors_notes.md
@@ -1,10 +1,16 @@
-<a id="Top"></a>
 # Notes on Color usage.
+<a id="Top"></a>
+
 
-## Table of Contents
- * [style/color](#style_color)
- * [Proxspace](#proxspace)
- * [help texts](#help-texts)
+# Table of Contents
+- [Notes on Color usage.](#notes-on-color-usage)
+- [Table of Contents](#table-of-contents)
+  - [style/color](#stylecolor)
+    - [Definition](#definition)
+    - [Styled header](#styled-header)
+    - [non styled header](#non-styled-header)
+  - [Proxspace](#proxspace)
+  - [Help texts](#help-texts)
 
 The client should autodetect color support when starting.
 
@@ -16,10 +22,12 @@ We have gradually been introducing this color scheme into the client since we go
 
 ## style/color
 ^[Top](#top)
+
 The following definition has be crystallized out from these experiments.  Its not set in stone yet so take this document as a guideline for how to create unified system scheme.
 
 ### Definition
 ^[Top](#top)
+
 - blue - system related headers, banner
 - white  - normal
 - cyan - headers
@@ -31,6 +39,7 @@ The following definition has be crystallized out from these experiments.  Its no
 
 ### Styled header
 ^[Top](#top)
+
 ```
     PrintAndLogEx(NORMAL, "");
     PrintAndLogEx(INFO, "--- " _CYAN_("Tag Information") " ---------------------------");
@@ -41,14 +50,17 @@ For the command help texts using _YELLOW_ for the example makes it very easy to
 
 ### non styled header
 ^[Top](#top)
+
 Most commands doesn't use a header yet. We added it to make it standout (ie: yellow,  green) of the informative tidbits in the output of a command. 
 
 
 ## Proxspace
 ^[Top](#top)
+
 Proxspace has support for colors.
 
 
 ## Help texts
 ^[Top](#top)
+
 The help text uses a hard coded template deep inside the cliparser.c file.
\ No newline at end of file
