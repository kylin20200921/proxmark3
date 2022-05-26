commit df88335cf1f7302f3be72cd31589a9fc70427f37
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Feb 27 08:07:10 2022 +0100

    passage about --force when flashing

diff --git a/doc/md/Use_of_Proxmark/0_Compilation-Instructions.md b/doc/md/Use_of_Proxmark/0_Compilation-Instructions.md
index 5de6a78c3..8b03d016f 100644
--- a/doc/md/Use_of_Proxmark/0_Compilation-Instructions.md
+++ b/doc/md/Use_of_Proxmark/0_Compilation-Instructions.md
@@ -13,6 +13,7 @@
     - [if you got an error](#if-you-got-an-error)
   - [Install](#install)
   - [Flash the BOOTROM & FULLIMAGE](#flash-the-bootrom--fullimage)
+  - [flasher stops and warns you about firmware image](#flasher-stops-and-warns-you-about-firmware-image)
   - [Run the client](#run-the-client)
   - [Next steps](#next-steps)
 
@@ -105,6 +106,28 @@ or
 proxmark3 /dev/ttyACM0 --flash --unlock-bootloader --image /tmp/my-bootrom.elf --image /tmp/my-fullimage.elf
 ```
 
+
+## flasher stops and warns you about firmware image
+^[Top](#top)
+
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
+You will need to add the `--force`  in order to continue flashing. 
+
+```sh
+pm3-flash-all --force
+```
+
+
+
 ## Run the client
 ^[Top](#top)
 
