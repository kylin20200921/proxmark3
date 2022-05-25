commit 499b14d794b95fcc0d17985f557353a5663b6bb2
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Sep 18 12:37:36 2021 +0200

    added a ToC

diff --git a/README.md b/README.md
index 8dc7ed8df..4a7fada5d 100644
--- a/README.md
+++ b/README.md
@@ -11,6 +11,20 @@
 | ![MacOS Build and Test](https://github.com/RfidResearchGroup/proxmark3/workflows/MacOS%20Build%20and%20Test/badge.svg?branch=master) | ![Ubuntu Build and Test](https://github.com/RfidResearchGroup/proxmark3/workflows/Ubuntu%20Build%20and%20Test/badge.svg?branch=master) | [![Build status](https://ci.appveyor.com/api/projects/status/b4gwrhq3nc876cuu/branch/master?svg=true)](https://ci.appveyor.com/project/RfidResearchGroup/proxmark3/branch/master) |
 
 
+# Table of Contents
+ 1. [PROXMARK3 INSTALLATION AND OVERVIEW](#proxmark3-installation-and-overview)
+ 2. [Notes / helpful documents](#notes--helpful-documents)
+ 3. [How to build?](#how-to-build)
+     1. [Proxmark3 RDV4](#proxmark3-rdv4)
+     2. [Generic Proxmark3 platforms](#generic-proxmark3-platforms)
+ 4. [What has changed?](#what-has-changed)
+ 5. [Development](#development)
+ 6. [Supported operative systems](#supported-operative-systems)
+ 7. [Precompiled binaries](#precompiled-binaries)
+ 8. [Proxmark3 GUI](#proxmark3-gui)
+ 9. [Official channels](#official-channels)
+10. [Maintainers](#maintainers)
+11. [Citation](#citation)
 
 # PROXMARK3 INSTALLATION AND OVERVIEW
 
@@ -41,10 +55,13 @@
 |[Notes on cliparser usage](/doc/cliparser.md)|[Notes on clocks](/doc/clocks.md)|[Notes on DESFire usage](/doc/desfire.md)|
 
 # How to build?
+
 ## Proxmark3 RDV4
+
 See the instruction links in the tables above to build, flash and run for your Proxmark3 RDV4 device.
 
 ## Generic Proxmark3 platforms
+
 In order to build this repo for generic Proxmark3 platforms we urge you to read [Advanced compilation parameters](/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md)
 
 We define generic Proxmark3 platforms as following devices.
@@ -80,7 +97,7 @@ We define generic Proxmark3 platforms as following devices.
 > [OBS! Read the 256kb flash memory advisory](/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md#256kb-versions)
 
 
-## What has changed?
+# What has changed?
 
 Proxmark3 RDV4 hardware modifications:
   * added flash memory 256kb
@@ -106,7 +123,14 @@ see the [Changelog file](CHANGELOG.md) which we try to keep updated. In short th
   * auto detection of serial ports and seamless integration with Bluetooth addon
   * reconnect to device from inside client
   * Supports tearoff attacks
-  * the most comprehensive compiled known keys dictionaries
+  * Supports NFC NDEF type1, type2, type4a, type4b, mifare, barcode
+  * Supports pm3 client scripts,  lua scripts,  python scripts
+  * Most comprehensive collection of scripts available
+  * Wiegand encoding, decoding.
+  * Supports EMV
+  * Most standalone modes available with easy compilation
+  * extensive test script for client and external tools
+  * Most comprehensive compiled known keys dictionaries
   * Slimed down usb communications with NG-frames
   * the most compiled public known key recovery software
   * the fastest implementations of said software
@@ -114,14 +138,12 @@ see the [Changelog file](CHANGELOG.md) which we try to keep updated. In short th
   * interoperability of said fileformats with libnfc, MFC tool app etc
   * Supports more RFID based protocols than ever
   * Easy install for package maintainers, distro maintainers
+  * Supports cmake, make
+  * Builds without errors or warnings on more OS/platforms than ever.
   * More documentation 
 
 
-  
-All of this and more is what makes this repo different from any other Proxmark3 firmware / client software. It is truely bleeding edge in that sense of available functionality and experience with it. With all extensive testing its also quite more stable than any other Proxmark3 repo.
-
-
-## Development
+# Development
 
 > ⚠ **Note**: This is a bleeding edge repository. The maintainers actively is working out of this repository and will be periodically re-structuring the code to make it easier to comprehend, navigate, build, test, and contribute to, so **DO expect significant changes to code layout on a regular basis**.
 
@@ -132,6 +154,7 @@ The [public roadmap](https://github.com/RfidResearchGroup/proxmark3/wiki/Public-
 
 
 ## Supported operative systems 
+
 This repo compiles nicely on 
    - WSL1 on Windows 10
    - Proxspace enviroment [release v3.10](https://github.com/Gator96100/ProxSpace/releases)
@@ -145,9 +168,20 @@ This repo compiles nicely on
 
 
 ## Precompiled binaries
+
 See [Proxmark3 precompiled builds](https://www.proxmarkbuilds.org/) 
 
 
+## Proxmark3 GUI
+
+The official PM3-GUI from Gaucho will not work. Not to mention is quite old and not maintained any longer.
+
+- [Proxmark3 Universal GUI](https://github.com/burma69/PM3UniversalGUI) will work more or less.
+
+- [Proxmark3 GUI cross-compiled](https://github.com/wh201906/Proxmark3GUI/) which is recently updated and claims to support latest source of this repo.
+- [Proxmark3_GUI](https://github.com/Phreak87/Proxmark3_GUI) simple gui in vb.net
+
+
 ## Official channels
 Where do you find the community?
    - [RFID Hacking community discord server](https://discord.gg/QfPvGFRQxH)
@@ -156,21 +190,14 @@ Where do you find the community?
    - [Proxmark3 forum](http://www.proxmark.org/forum/index.php)
 
 
-## Maintainers ( package, distro )
+## Maintainers
 
 To all distro, package maintainers, we tried to make your life easier.
 
 `make install` is now available and if you want to know more.
-- [Notes for maintainers](/doc/md/Development/Maintainers.md)
-
-## Proxmark3 GUI
-
-The official PM3-GUI from Gaucho will not work. Not to mention is quite old and not maintained any longer.
-
-- [Proxmark3 Universal GUI](https://github.com/burma69/PM3UniversalGUI) will work more or less.
 
-- [Proxmark3 GUI cross-compiled](https://github.com/wh201906/Proxmark3GUI/) which is recently updated and claims to support latest source of this repo.
-- [Proxmark3_GUI](https://github.com/Phreak87/Proxmark3_GUI) simple gui in vb.net
+This document will be helpful for you
+- [Notes for maintainers](/doc/md/Development/Maintainers.md)
 
 
 ## Citation
@@ -187,4 +214,4 @@ If you need to refer to a specific state of the repository, use a commit number
 ```
   note = {Accessed: commit 12327f71a27da23831901847886aaf20e8ad3ca0}
   note = {Accessed: 2021-01-01}
-```
+```
\ No newline at end of file
