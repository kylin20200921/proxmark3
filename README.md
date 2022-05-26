commit 2574f49801f6ed1a2248d14fa06b84143177484b
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Feb 27 08:17:29 2022 +0100

    passage about --force when flashing

diff --git a/doc/md/Installation_Instructions/Troubleshooting.md b/doc/md/Installation_Instructions/Troubleshooting.md
index 141d62b41..7367bf08b 100644
--- a/doc/md/Installation_Instructions/Troubleshooting.md
+++ b/doc/md/Installation_Instructions/Troubleshooting.md
@@ -7,6 +7,7 @@ Always use the latest repository commits from *master* branch. There are always
 ## Table of Contents
 
   * [pm3 or pm3-flash* doesn't see my Proxmark](#pm3-or-pm3-flash-doesnt-see-my-proxmark)
+  * [pm3-flash* stops and warns about up-to-date firmware images](#pm3-flash-stops-and-warns-about-up-to-date-firmware-images)
   * [My Proxmark3 seems bricked](#my-proxmark3-seems-bricked)
      * [Maybe just a false alarm?](#maybe-just-a-false-alarm)
      * [Find out why it would be bricked](#find-out-why-it-would-be-bricked)
@@ -42,6 +43,25 @@ Note that with the Bluetooth adapter, you *have to* use directly the client, and
 
 * [Bluetooth](/doc/bt_manual_v10.md)
 
+
+## `pm3-flash*` stops and warns about up-to-date firmware images
+^[Top](#top)
+
+The Proxmark3 software and firmware is connected tightly. The strong recommendation is to use the client with a Proxmark3 device flashed with firmware images from same compilation time.  
+In the flash process you might get this message because the firmware images is downloaded or distributed and you have compiled your own client.  Ie,  not from same compilation time.
+To minimize the risks the flasher warns about it and stops.  
+
+```
+    Make sure to flash a correct and up-to-date version
+    You can force flashing this firmware by using the option '--force'
+```
+
+You need to add the `--force` param in order to continue flashing. 
+
+```sh
+pm3-flash-all --force
+```
+
 ## My Proxmark3 seems bricked
 ^[Top](#top)
 
