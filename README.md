commit c3b876c46c9ca209bc16ba7ad1825df4504f54a5
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Dec 31 11:22:10 2021 +0100

    text

diff --git a/doc/colors_notes.md b/doc/colors_notes.md
index 182383cf3..9d8704c87 100644
--- a/doc/colors_notes.md
+++ b/doc/colors_notes.md
@@ -1,9 +1,9 @@
-# Notes on Color usage.
+# Notes on Color usage
 <a id="Top"></a>
 
 
 # Table of Contents
-- [Notes on Color usage.](#notes-on-color-usage)
+- [Notes on Color usage](#notes-on-color-usage)
 - [Table of Contents](#table-of-contents)
   - [style/color](#stylecolor)
     - [Definition](#definition)
diff --git a/doc/md/Development/Makefile-vs-CMake.md b/doc/md/Development/Makefile-vs-CMake.md
index df696e5ec..d77734cfc 100644
--- a/doc/md/Development/Makefile-vs-CMake.md
+++ b/doc/md/Development/Makefile-vs-CMake.md
@@ -1,6 +1,21 @@
 # Makefile vs CMake
+<a id="Top"></a>
+
+
+# Table of Contents
+- [Makefile vs CMake](#makefile-vs-cmake)
+- [Table of Contents](#table-of-contents)
+  - [Client](#client)
+  - [Tools](#tools)
+  - [ARM](#arm)
+    - [Features to be used via `Makefile.platform`](#features-to-be-used-via-makefileplatform)
+    - [Other features](#other-features)
+  - [Global](#global)
+  
+
 
 ## Client
+^[Top](#top)
 
 The client can be compiled both with the historical Makefile and with a newer CMakeLists.txt.
 At the moment both are maintained because they don't perfectly overlap yet.
@@ -74,6 +89,7 @@ At the moment both are maintained because they don't perfectly overlap yet.
 | libpm3 with SWIG Lua+Python| **no** | *ongoing* | cf libpm3_experiments branch |
 
 ## Tools
+^[Top](#top)
 
 `makefile` only at the moment
 
@@ -82,10 +98,12 @@ At the moment both are maintained because they don't perfectly overlap yet.
 | Skip GPU-dependent code | `SKIPGPU=1` | to skip ht2crack5gpu tool when compiling the hitag2crack toolsuite |
 
 ## ARM
+^[Top](#top)
 
 `makefile` only at the moment
 
-### Features to be used via `Makefile.platform`:
+### Features to be used via `Makefile.platform`
+^[Top](#top)
 
 `SKIP_*`, `STANDALONE`
 
@@ -97,7 +115,8 @@ At the moment both are maintained because they don't perfectly overlap yet.
 | Skip LF/HF techs in the firmware | `SKIP_`*`=1` | see `common_arm/Makefile.hal` for a list |
 | Standalone mode choice | `STANDALONE=` | see `doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md` for a list |
 
-### Other features:
+### Other features
+^[Top](#top)
 
 | Feature | Makefile | Remarks |
 |-----|---|---|
@@ -107,5 +126,6 @@ At the moment both are maintained because they don't perfectly overlap yet.
 | Tag firmware image | `FWTAG=` | for maintainers |
 
 ## Global
+^[Top](#top)
 
 `makefile` only at the moment
