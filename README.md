commit 78628cbe0c5a7286c78ed4487211f5981967784a
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Jan 13 21:42:44 2022 +0100

    Update README.md

diff --git a/README.md b/README.md
index 561b26fea..7182d68ef 100644
--- a/README.md
+++ b/README.md
@@ -1,4 +1,6 @@
-# Proxmark3
+# Iceman - Proxmark3 a RFID / NFC project.
+
+The Proxmark3 is the swiss-army tool of RFID, allowing for interactions with the vast majority of RFID tags on a global scale. Originally built by Jonathan Westhues, the device is now the goto tool for RFID Analysis for the enthusiast. Iceman repository is considered to be the pinnacle of features and functionality, enabling a huge range of extremely useful and convenient commands and LUA scripts to automate chip identification, penetration testing, and programming
 
 
 | Latest Release | Coverity | Contributors |
@@ -79,9 +81,11 @@ We define generic Proxmark3 platforms as following devices.
    - **Note**: unknown pin assignments.
  - ⚠  Ryscorp Proxmark3 Pro 
    - **Note**: device has different fpga and unknown pin assignments.
+   - **Note**: Company have dissappared, leaving their customers in the dark.
  - ⚠  iCopy-X
    - **Note**: experimental support, currently incompatible with iCopy-X GUI as Proxmark client commands are now using cliparser.
    - **Note**: see also [icopyx-community repos](https://github.com/iCopy-X-Community/) for upstream sources, reversed hw etc.
+   - **Note**: Uses DRM to lock down tags, ignores the open source licences. Use on your own risk. 
 
 **Unknown support status**
  - ⚠  VX
@@ -114,6 +118,7 @@ Proxmark3 RDV4 hardware modifications:
 
 This repo vs official Proxmark3 repo:
 see the [Changelog file](CHANGELOG.md) which we try to keep updated. In short this repo gives you a completely different user experience when it comes to Proxmark3.
+  * Supports command tab complete
   * richer CLI with use of colors / emojis
   * help text system implemented everywhere
   * hints system
@@ -179,14 +184,13 @@ See [Proxmark3 precompiled builds](https://www.proxmarkbuilds.org/)
 The official PM3-GUI from Gaucho will not work. Not to mention is quite old and not maintained any longer.
 
 - [Proxmark3 Universal GUI](https://github.com/burma69/PM3UniversalGUI) will work more or less.
-
 - [Proxmark3 GUI cross-compiled](https://github.com/wh201906/Proxmark3GUI/) which is recently updated and claims to support latest source of this repo.
 - [Proxmark3_GUI](https://github.com/Phreak87/Proxmark3_GUI) simple gui in vb.net
 
 
 ## Official channels
 Where do you find the community?
-   - [RFID Hacking community discord server](https://discord.gg/QfPvGFRQxH)
+   - [RFID Hacking community discord server](https://discord.gg/iceman)
    - [Proxmark3 IRC channel](https://web.libera.chat/?channels=#proxmark3)
    - [Proxmark3 sub reddit](https://www.reddit.com/r/proxmark3/)
    - [Proxmark3 forum](http://www.proxmark.org/forum/index.php)
@@ -205,9 +209,9 @@ This document will be helpful for you
 ## Citation
 Use this bibtex to cite this repository globally:
 ```
-@misc{proxmark3rrg,
+@misc{proxmark3,
   author = {C. {Herrmann} and P. {Teuwen} and O. {Moiseenko} and M. {Walker} and others},
-  title = {{Proxmark3 -- RRG / Iceman repo}},
+  title = {{Proxmark3 -- Iceman repo}},
   howpublished = {\url{https://github.com/RfidResearchGroup/proxmark3}},
   keywords = {rfid nfc iceman proxmark3 125khz 134khz 13.56mhz},
 }
