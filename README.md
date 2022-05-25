commit 3bfc17e110ae8b5db3b815bcdbaf2451579328e2
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Sep 17 17:57:08 2021 +0200

    textual

diff --git a/README.md b/README.md
index 28e5baffa..8dc7ed8df 100644
--- a/README.md
+++ b/README.md
@@ -145,14 +145,7 @@ This repo compiles nicely on
 
 
 ## Precompiled binaries
-We don't maintain any precompiled binaries in this repo. There is community effort over at the Proxmark3 forum where package maintainer [@gator96100](https://github.com/gator96100) has set up a AWS bucket with precompiled Proxspace (MinGW) binaries which are recompiled every night and with that also up-to-date. We link to these files here as to make it easier for users. If you are having troubles with these files, we suggest to read the [homepage of his proxmark builds](https://www.proxmarkbuilds.org/) or read the [sticky thread at forum](http://www.proxmark.org/forum/viewtopic.php?pid=24763#p24763) 
-
-### Proxmark3 RDV4 devices
-- [Precompiled builds for RDV40 dedicated x64](https://www.proxmarkbuilds.org/#rdv40-64/)
-- [Precompiled builds for RDV40 dedicated with Bluetooth addon x64](https://www.proxmarkbuilds.org/#rdv40_bt-64/)
-
-### Generic Proxmark3 devices
-- [Precompiled builds for RRG / Iceman repository x64](https://www.proxmarkbuilds.org/#rrg_other-64/)
+See [Proxmark3 precompiled builds](https://www.proxmarkbuilds.org/) 
 
 
 ## Official channels
@@ -179,14 +172,6 @@ The official PM3-GUI from Gaucho will not work. Not to mention is quite old and
 - [Proxmark3 GUI cross-compiled](https://github.com/wh201906/Proxmark3GUI/) which is recently updated and claims to support latest source of this repo.
 - [Proxmark3_GUI](https://github.com/Phreak87/Proxmark3_GUI) simple gui in vb.net
 
-# Donations
-
-Nothing says thank you as much as a donation. 
-
-If you feel the love, do feel free to become a [Iceman patron](https://www.patreon.com/iceman1001).
-
-If you are using @gator96100 's Proxspace with pre-compiled binaries do consider buy him a coffee for his efforts.
-
 
 ## Citation
 Use this bibtex to cite this repository globally:
diff --git a/client/src/proxmark3.c b/client/src/proxmark3.c
index 40eb8c01c..93446104a 100644
--- a/client/src/proxmark3.c
+++ b/client/src/proxmark3.c
@@ -36,9 +36,9 @@
 #include "preferences.h"
 
 #ifndef LIBPM3
-#define BANNERMSG1 "      Iceman :coffee:"
-#define BANNERMSG2 "  :snowflake: bleeding edge"
-#define BANNERMSG3 "  https://github.com/rfidresearchgroup/proxmark3/"
+#define BANNERMSG1 ""
+#define BANNERMSG2 "   [ :snowflake: Iceman :snowflake: ]"
+#define BANNERMSG3 ""
 
 typedef enum LogoMode { UTF8, ANSI, ASCII } LogoMode;
 
