commit 248cc83aab19076c9960d7c80b94050d5b81a2fc
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Mar 20 10:14:46 2022 +0100

    textual

diff --git a/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md b/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md
index 400b5cba8..f905084f0 100644
--- a/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md
+++ b/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md
@@ -170,11 +170,13 @@ To flash on OS X, better to enter the bootloader mode manually, else you may exp
 
 ### the button trick
 ^[Top](#top)
+
 With your Proxmark3 unplugged from your machine, press and hold the button on your Proxmark3 as you plug it into a USB port. You can release the button, two of the four LEDs should stay on. You're in bootloader mode, ready for the next step. In case the two LEDs don't stay on when you're releasing the button, you've an old bootloader, start over and keep the button pressed during the whole flashing procedure.
 
 
 ## Run it
 ^[Top](#top)
+
 To use the compiled client, you can use `pm3` script, it is a wrapper of the proxmark3 client that handles automatic detection of your proxmark.
 ```sh
 pm3
diff --git a/doc/md/Installation_Instructions/Mac-OS-X-MacPorts-Installation-Instructions.md b/doc/md/Installation_Instructions/Mac-OS-X-MacPorts-Installation-Instructions.md
index dd237c04c..6fa5a1754 100644
--- a/doc/md/Installation_Instructions/Mac-OS-X-MacPorts-Installation-Instructions.md
+++ b/doc/md/Installation_Instructions/Mac-OS-X-MacPorts-Installation-Instructions.md
@@ -108,11 +108,14 @@ _Take extra note to instructions if you don't have a Proxmark3 RDV4 device._
 To flash on OS X, better to enter the bootloader mode manually, else you may experience errors.
 
 ### the button trick
+^[Top](#top)
+
 With your Proxmark3 unplugged from your machine, press and hold the button on your Proxmark3 as you plug it into a USB port. You can release the button, two of the four LEDs should stay on. You're in bootloader mode, ready for the next step. In case the two LEDs don't stay on when you're releasing the button, you've an old bootloader, start over and keep the button pressed during the whole flashing procedure.
 
 
 ## Run it
 ^[Top](#top)
+
 To use the compiled client, you can use `pm3` script, it is a wrapper of the proxmark3 client that handles automatic detection of your proxmark.
 ```sh
 pm3
