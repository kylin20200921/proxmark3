commit 22033272ec4b1466e9108bc371bbe68f171d92dd
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Mar 20 10:13:20 2022 +0100

    improving instructions to make them clear

diff --git a/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md b/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md
index 09747b435..400b5cba8 100644
--- a/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md
+++ b/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md
@@ -14,7 +14,10 @@
   - [Run the client](#run-the-client)
   - [Next steps](#next-steps)
 - [Homebrew (Mac OS X), developer installation](#homebrew-mac-os-x-developer-installation)
-  - [Compile and use the project](#compile-and-use-the-project)
+- [Clone the Iceman repository](#clone-the-iceman-repository)
+  - [Compile the project](#compile-the-project)
+    - [the button trick](#the-button-trick)
+  - [Run it](#run-it)
 
 
 
@@ -148,18 +151,36 @@ brew install recode
 brew install astyle
 ```
 
-
-## Compile and use the project
+# Clone the Iceman repository
 ^[Top](#top)
 
-To use the compiled client, the only difference is that the Proxmark3 port is `/dev/tty.usbmodemiceman1`, so commands become:
-
 ```sh
-proxmark3 /dev/ttyACM0  =>  proxmark3 /dev/tty.usbmodemiceman1
+git clone https://github.com/RfidResearchGroup/proxmark3.git
+cd proxmark3
 ```
 
+## Compile the project
+^[Top](#top)
+
 Now you're ready to follow the [compilation instructions](/doc/md/Use_of_Proxmark/0_Compilation-Instructions.md).
+From there, you can follow the original compilation instructions. 
+_Take extra note to instructions if you don't have a Proxmark3 RDV4 device._
 
 To flash on OS X, better to enter the bootloader mode manually, else you may experience errors.
+
+### the button trick
+^[Top](#top)
 With your Proxmark3 unplugged from your machine, press and hold the button on your Proxmark3 as you plug it into a USB port. You can release the button, two of the four LEDs should stay on. You're in bootloader mode, ready for the next step. In case the two LEDs don't stay on when you're releasing the button, you've an old bootloader, start over and keep the button pressed during the whole flashing procedure.
-From there, you can follow the original compilation instructions.
+
+
+## Run it
+^[Top](#top)
+To use the compiled client, you can use `pm3` script, it is a wrapper of the proxmark3 client that handles automatic detection of your proxmark.
+```sh
+pm3
+```
+
+If you want to manually select serial port, remember that the Proxmark3 port is `/dev/tty.usbmodemiceman1`, so commands become:
+```sh
+proxmark3 /dev/ttyACM0  =>  proxmark3 /dev/tty.usbmodemiceman1
+```
\ No newline at end of file
diff --git a/doc/md/Installation_Instructions/Mac-OS-X-MacPorts-Installation-Instructions.md b/doc/md/Installation_Instructions/Mac-OS-X-MacPorts-Installation-Instructions.md
index 4efd93ab3..dd237c04c 100644
--- a/doc/md/Installation_Instructions/Mac-OS-X-MacPorts-Installation-Instructions.md
+++ b/doc/md/Installation_Instructions/Mac-OS-X-MacPorts-Installation-Instructions.md
@@ -10,7 +10,10 @@
   - [Main prerequisite](#main-prerequisite)
   - [Installing latest releases](#installing-latest-releases)
   - [Build from source](#build-from-source)
-  - [Compile and use the project](#compile-and-use-the-project)
+- [Clone the Iceman repository](#clone-the-iceman-repository)
+  - [Compile the project](#compile-the-project)
+    - [the button trick](#the-button-trick)
+  - [Run it](#run-it)
 
 
 
@@ -87,15 +90,36 @@ These instructions will show how to setup the environment on OSX to the point wh
     ```
 
 
-## Compile and use the project
+# Clone the Iceman repository
 ^[Top](#top)
 
-Now you're ready to follow the [compilation instructions](/doc/md/Use_of_Proxmark/0_Compilation-Instructions.md).
+```sh
+git clone https://github.com/RfidResearchGroup/proxmark3.git
+cd proxmark3
+```
 
-To use the compiled client, you can use `pm3` script, it is a wrapper of the proxmark3 client that handles automatic detection of your proxmark.
+## Compile the project
+^[Top](#top)
+
+Now you're ready to follow the [compilation instructions](/doc/md/Use_of_Proxmark/0_Compilation-Instructions.md).
+From there, you can follow the original instructions. 
+_Take extra note to instructions if you don't have a Proxmark3 RDV4 device._
 
 To flash on OS X, better to enter the bootloader mode manually, else you may experience errors.
 
+### the button trick
 With your Proxmark3 unplugged from your machine, press and hold the button on your Proxmark3 as you plug it into a USB port. You can release the button, two of the four LEDs should stay on. You're in bootloader mode, ready for the next step. In case the two LEDs don't stay on when you're releasing the button, you've an old bootloader, start over and keep the button pressed during the whole flashing procedure.
 
-From there, you can follow the original instructions.
+
+## Run it
+^[Top](#top)
+To use the compiled client, you can use `pm3` script, it is a wrapper of the proxmark3 client that handles automatic detection of your proxmark.
+```sh
+pm3
+```
+
+If you want to manually select serial port, remember that the Proxmark3 port is `/dev/tty.usbmodemiceman1`, so commands become:
+```sh
+proxmark3 /dev/ttyACM0  =>  proxmark3 /dev/tty.usbmodemiceman1
+```
+
