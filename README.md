commit e5b97dccf37e54a801d7ded3f17f541202ee12ac
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Dec 31 11:19:30 2021 +0100

    text

diff --git a/armsrc/Standalone/readme.md b/armsrc/Standalone/readme.md
index dee415ffa..dacf5057c 100644
--- a/armsrc/Standalone/readme.md
+++ b/armsrc/Standalone/readme.md
@@ -1,4 +1,19 @@
 # Standalone Modes
+<a id="Top"></a>
+
+
+# Table of Contents
+- [Standalone Modes](#standalone-modes)
+- [Table of Contents](#table-of-contents)
+  - [Implementing a standalone mode](#implementing-a-standalone-mode)
+  - [Naming your standalone mode](#naming-your-standalone-mode)
+  - [Update MAKEFILE.HAL](#update-makefilehal)
+  - [Update MAKEFILE.INC](#update-makefileinc)
+  - [Adding identification string of your mode](#adding-identification-string-of-your-mode)
+  - [Compiling your standalone mode](#compiling-your-standalone-mode)
+  - [Submitting your code](#submitting-your-code)
+
+
 
 This contains functionality for different StandAlone modes. The fullimage will be built given the correct compiler flags used. Build targets for these files are contained in `Makefile.inc` and `Makefile.hal`
 
@@ -8,7 +23,9 @@ Have a look at the skeleton standalone mode, in the file `lf_skeleton.c`.
 As it is now, you can only have one standalone mode installed at the time unless you use the dankarmulti mode (see `dankarmulti.c` on how to use it).
 
 To avoid clashes between standalone modes, protect all your static variables with a specific namespace. See how it is done in the existing standalone modes.
+
 ## Implementing a standalone mode
+^[Top](#top)
 
 We suggest you keep your standalone code inside the `armsrc/Standalone` folder. And that you name your files according to your standalone mode name.
 
@@ -42,6 +59,7 @@ void RunMod(void) {
 ````
 
 ## Naming your standalone mode
+^[Top](#top)
 
 We suggest that you follow these guidelines:
 - Use HF/LF to denote which frequency your mode is targeting.  
@@ -58,6 +76,7 @@ This leads to your next step, your DEFINE name needed in Makefile.
 
 
 ## Update MAKEFILE.HAL
+^[Top](#top)
 
 Add your mode to the `Makefile.hal` help and modes list (alphabetically):
 ```
@@ -82,6 +101,8 @@ STANDALONE_MODES_REQ_BT :=
 ```
 
 ## Update MAKEFILE.INC
+^[Top](#top)
+
 Add your source code files like the following sample in the `Makefile.inc`
 
 ```
@@ -97,6 +118,8 @@ endif
 ```
 
 ## Adding identification string of your mode
+^[Top](#top)
+
 Do please add a identification string in a function called `ModInfo` inside your source code file.
 This will enable an easy way to detect on client side which standalone mode has been installed on the device.
 
@@ -107,6 +130,8 @@ void ModInfo(void) {
 ````
 
 ## Compiling your standalone mode
+^[Top](#top)
+
 Once all this is done, you and others can now easily compile different standalone modes by just selecting one of the standalone modes (list in `Makefile.hal` or ) , e.g.:
 
 - rename  Makefile.platform.sample -> Makefile.platform
@@ -143,6 +168,7 @@ When compiling you will see a header showing what configurations your project co
 Make sure it says your standalone mode name.  
 
 ## Submitting your code
+^[Top](#top)
 
 Once you're ready to share your mode, please
 
