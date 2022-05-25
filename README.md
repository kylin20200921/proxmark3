commit 28f4a19bd617ae48ac7fe185799a1c556798cc51
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Dec 31 11:15:25 2021 +0100

    text

diff --git a/doc/cliparser.md b/doc/cliparser.md
index a837a5afc..08d277180 100644
--- a/doc/cliparser.md
+++ b/doc/cliparser.md
@@ -1,4 +1,34 @@
-# Cliparser
+# Note on Cliparser
+<a id="Top"></a>
+
+
+# Table of Contents
+- [Note on Cliparser](#note-on-cliparser)
+- [Table of Contents](#table-of-contents)
+  - [cliparser setup and use](#cliparser-setup-and-use)
+  - [design comments](#design-comments)
+  - [common options](#common-options)
+  - [How to implement in source code](#how-to-implement-in-source-code)
+    - [setup the parser data structure](#setup-the-parser-data-structure)
+    - [define the context](#define-the-context)
+    - [define the options](#define-the-options)
+    - [Notes:](#notes)
+      - [bool option.  true if supplied](#bool-option--true-if-supplied)
+      - [integer that is optional](#integer-that-is-optional)
+      - [integer that is required](#integer-that-is-required)
+      - [double that is optional](#double-that-is-optional)
+      - [double that is required](#double-that-is-required)
+      - [String option that is optional and only one instance can be provided](#string-option-that-is-optional-and-only-one-instance-can-be-provided)
+      - [String option that is required and only one instance can be provided](#string-option-that-is-required-and-only-one-instance-can-be-provided)
+      - [String option that is optional and can have up to 250 instances provided](#string-option-that-is-optional-and-can-have-up-to-250-instances-provided)
+      - [String option that is required/at least one instance and can have up to 250 instances](#string-option-that-is-requiredat-least-one-instance-and-can-have-up-to-250-instances)
+      - [unsigned integer optional](#unsigned-integer-optional)
+      - [unsigned integer required](#unsigned-integer-required)
+    - [show the menu](#show-the-menu)
+    - [clean up](#clean-up)
+    - [retrieving options](#retrieving-options)
+
+
 
 The old style with mixed custom commandline parsing of user parameters or options was messy and confusing.  You can find all kinds in the Proxmark3 client.
 Samples
@@ -36,11 +66,13 @@ This is the _new_ and _preferred_ way to implement _helptext_ and _cli parsing_
 
 
 ## cliparser setup and use
+^[Top](#top)
 
 The parser will format and color and layout as needed.
 It will also add the `-h --help` option automatic.
 
 ## design comments
+^[Top](#top)
 
 * where possible all options should be lowercase.
 * extended options preceded with -- should be short
@@ -52,6 +84,8 @@ It will also add the `-h --help` option automatic.
 
 
 ## common options
+^[Top](#top)
+
     -h --help       : help
     --cn            : card number
     --fn            : facility number
@@ -69,8 +103,11 @@ It will also add the `-h --help` option automatic.
 
 
 ## How to implement in source code
+^[Top](#top)
 
 ### setup the parser data structure
+^[Top](#top)
+
 Header file to include
 
     #include "cliparser.h"
@@ -81,6 +118,8 @@ In the command function, setup the context
 
 
 ### define the context
+^[Top](#top)
+
 `CLIParserInit (\<context\>, \<description\>, \<notes\n examples ... \>);`
 
 use -> to separate example and example comment and \\n to separate examples.
@@ -94,6 +133,7 @@ e.g. lf indala clone -r a0000000a0002021 -> this uses .....
                   "lf indala clone -l -r 80000001b23523a6c2e31eba3cbee4afb3c6ad1fcf649393928c14e5");
 
 ### define the options
+^[Top](#top)
 
     void *argtable[] = {
         arg_param_begin,
@@ -110,6 +150,8 @@ _All options has a parameter index,  since `-h --help` is added automatic, it wi
 Hence all options you add will start at index 1 and upwards. It added in the define "arg_param_begin_
 
 ### Notes:
+^[Top](#top)
+
 #### bool option.  true if supplied
 `bool : arg_lit0 ("<short option>", "<long option>", <"description">)`
 
@@ -150,17 +192,21 @@ Unsigned values, like  u32 and u64 can be accomplished with
 
 
 ### show the menu
+^[Top](#top)
+
 `CLIExecWithReturn(\<context\>, \<command line to parse\>, \<arg/opt table\>, \<return on error\>);`
 
     CLIExecWithReturn(ctx, Cmd, argtable, false);
 
 ### clean up
+^[Top](#top)
+
 Once you have extracted the options, cleanup the context.
 
     CLIParserFree(ctx);
 
 ### retrieving options
-
+^[Top](#top)
 
 The parser will format and color and layout as needed.
 It will also add the `-h --help` option automatic.
