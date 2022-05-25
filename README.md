commit 3450439b7ba04285004d8b2b7b6288becc11273c
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Dec 31 11:36:43 2021 +0100

    text

diff --git a/doc/md/Installation_Instructions/Linux-Installation-Instructions.md b/doc/md/Installation_Instructions/Linux-Installation-Instructions.md
index ef8e735c7..bc9ec1cf1 100644
--- a/doc/md/Installation_Instructions/Linux-Installation-Instructions.md
+++ b/doc/md/Installation_Instructions/Linux-Installation-Instructions.md
@@ -1,4 +1,27 @@
+<a id="Top"></a>
+
+# Linux Installation Instructions
+
+# Table of Contents
+- [Linux Installation Instructions](#linux-installation-instructions)
+- [Table of Contents](#table-of-contents)
+- [External resources](#external-resources)
+- [Install the required dependencies](#install-the-required-dependencies)
+  - [On Debian / Ubuntu / Kali / ParrotOS / Raspbian](#on-debian--ubuntu--kali--parrotos--raspbian)
+  - [On ArchLinux](#on-archlinux)
+  - [On Fedora](#on-fedora)
+  - [On openSUSE](#on-opensuse)
+- [Clone the RRG/Iceman repository](#clone-the-rrgiceman-repository)
+- [Check ModemManager](#check-modemmanager)
+    - [⚠️ Very important ⚠️](#️-very-important-️)
+- [Check connection](#check-connection)
+- [Get permissions to use /dev/ttyACM0](#get-permissions-to-use-devttyacm0)
+- [Compile and use the project](#compile-and-use-the-project)
+
+
+
 # External resources
+^[Top](#top)
 
 You might want to follow one of these external resources to get an overview, but please still read carefully this page as some instructions may have evolved.
 
@@ -12,8 +35,10 @@ You might want to follow one of these external resources to get an overview, but
 
 
 # Install the required dependencies
+^[Top](#top)
 
 ## On Debian / Ubuntu / Kali / ParrotOS / Raspbian
+^[Top](#top)
 
 First what we want to do is get an update for the system. If you need to upgrade do this **before** the install. An upgrade was carried out prior to following these instructions. 
 
@@ -37,6 +62,7 @@ If you don't need support for Python3 scripts in the Proxmark3 client, you can s
 If you get some (non blocking) error at runtime such as _Gtk-Message: Failed to load module "canberra-gtk-module"_ you may have to install `libcanberra-gtk-module`.
 
 ## On ArchLinux
+^[Top](#top)
 
 ```sh
 sudo pacman -Sy git base-devel readline bzip2 arm-none-eabi-gcc arm-none-eabi-newlib qt5-base bluez python --needed
@@ -49,6 +75,7 @@ If you don't need the graphical components of the Proxmark3 client (such as in `
 If you don't need support for Python3 scripts in the Proxmark3 client, you can skip the installation of `python`.
 
 ## On Fedora
+^[Top](#top)
 
 ```sh
 sudo dnf install git make gcc gcc-c++ arm-none-eabi-gcc-cs arm-none-eabi-newlib readline-devel bzip2-devel qt5-qtbase-devel bluez-libs-devel python3-devel libatomic
@@ -61,6 +88,7 @@ If you don't need the graphical components of the Proxmark3 client (such as in `
 If you don't need support for Python3 scripts in the Proxmark3 client, you can skip the installation of `python3-devel`.
 
 ## On openSUSE
+^[Top](#top)
 
 ```sh
 sudo zypper install git patterns-devel-base-devel_basis gcc-c++ readline-devel libbz2-devel cross-arm-none-gcc9 cross-arm-none-newlib-devel python3-devel libqt5-qtbase-devel
@@ -73,18 +101,23 @@ If you don't need the graphical components of the Proxmark3 client (such as in `
 If you don't need support for Python3 scripts in the Proxmark3 client, you can skip the installation of `python3-devel`.
 
 # Clone the RRG/Iceman repository
+^[Top](#top)
 
 ```sh
 git clone https://github.com/RfidResearchGroup/proxmark3.git
 ```
 
 # Check ModemManager
+^[Top](#top)
 
 ### ⚠️ Very important ⚠️
+^[Top](#top)
+
 make sure ModemManager will not interfere, otherwise it could brick your Proxmark3!
 Read carefully [this page about ModemManager](ModemManager-Must-Be-Discarded.md) and follow its instructions.
 
 # Check connection
+^[Top](#top)
 
 Check the proxmark is being picked up by your computer. Plug it in, then:
 
@@ -100,6 +133,7 @@ cdc_acm 2-1.2:1.0: ttyACM0: USB ACM device
 And a new `/dev/ttyACM0` should have appeared.
 
 # Get permissions to use /dev/ttyACM0
+^[Top](#top)
 
 Add current user to the proper group to get permission to use `/dev/ttyACM0`.
 
@@ -118,5 +152,6 @@ To test you have the proper read & write rights, plug the Proxmark3 and execute:
 It must return `ok`. Otherwise this means you've got a permission problem to fix.
 
 # Compile and use the project
+^[Top](#top)
 
 Now you're ready to follow the [compilation instructions](/doc/md/Use_of_Proxmark/0_Compilation-Instructions.md).
diff --git a/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md b/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md
index bdd13df49..91a2fedc4 100644
--- a/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md
+++ b/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md
@@ -1,6 +1,25 @@
-# Homebrew (Mac OS X), automatic installation
+
+<a id="Top"></a>
+
+# Mac OS X - Homebrew automatic installation
+
+
+# Table of Contents
+- [Mac OS X - Homebrew automatic installation](#mac-os-x---homebrew-automatic-installation)
+- [Table of Contents](#table-of-contents)
+  - [Apple Silicon (M1) Notes](#apple-silicon-m1-notes)
+  - [Install Proxmark3 tools](#install-proxmark3-tools)
+  - [Upgrade HomeBrew tap formula](#upgrade-homebrew-tap-formula)
+  - [Flash the BOOTROM & FULLIMAGE](#flash-the-bootrom--fullimage)
+  - [Run the client](#run-the-client)
+  - [Next steps](#next-steps)
+- [Homebrew (Mac OS X), developer installation](#homebrew-mac-os-x-developer-installation)
+  - [Compile and use the project](#compile-and-use-the-project)
+
+
 
 ## Apple Silicon (M1) Notes
+^[Top](#top)
 
 Ensure Rosetta 2 is installed as it's currently needed to run `arm-none-eabi-gcc` as it's delivered as a precombiled x86_64 binary.
 
@@ -12,11 +31,13 @@ bad CPU type in executable
 
 Then you are missing Rosetta 2 and need to install it: `/usr/sbin/softwareupdate --install-rosetta`
 
-Homebrew has changed their prefix to differentiate between native Apple Silicon and Intel compiled binaries.  The Makefile attempts to account for this but please note that whichever terminal or application you're using must be running under Architecture "Apple" as seen by Activity Monitor as all child processes inherit the Rosetta 2 environment of their parent.  You can check which architecture you're currently running under with a `uname -m` in your terminal.
+Homebrew has changed their prefix to differentiate between native Apple Silicon and Intel compiled binaries.  The Makefile attempts to account for this but please note that 
+whichever terminal or application you're using must be running under Architecture "Apple" as seen by Activity Monitor as all child processes inherit the Rosetta 2 environment of their parent.  You can check which architecture you're currently running under with a `uname -m` in your terminal.
 
 Visual Studio Code still runs under Rosetta 2 and if you're developing for proxmark3 on an Apple Silicon Mac you might want to consider running the Insiders build which has support for running natively on Apple Silicon.
 
 ## Install Proxmark3 tools
+^[Top](#top)
 
 These instructions comes from \@Chrisfu, where we got the proxmark3.rb scriptfile from.
 For further questions about Mac & Homebrew, contact [\@Chrisfu on Twitter](https://github.com/chrisfu/)
@@ -40,6 +61,7 @@ For further questions about Mac & Homebrew, contact [\@Chrisfu on Twitter](https
 For more info, go to https://github.com/RfidResearchGroup/homebrew-proxmark3
 
 ## Upgrade HomeBrew tap formula
+^[Top](#top)
 
 *This method is useful for those looking to run bleeding-edge versions of RRG/iceman's client. Keep this in mind when attempting to update your HomeBrew tap formula as this procedure could easily cause a build to break if an update is unstable on macOS.* 
 
@@ -54,6 +76,7 @@ brew upgrade --fetch-HEAD proxmark3
 ```
 
 ## Flash the BOOTROM & FULLIMAGE
+^[Top](#top)
 
 With your Proxmark3 unplugged from your machine, press and hold the button on your Proxmark3 as you plug it into a USB port. You can release the button, two of the four LEDs should stay on. You're in bootloader mode, ready for the next step. In case the two LEDs don't stay on when you're releasing the button, you've an old bootloader, start over and keep the button pressed during the whole flashing procedure.
 
@@ -73,6 +96,7 @@ proxmark3 /dev/tty.usbmodemiceman1 --flash --unlock-bootloader --image /usr/loca
 
 
 ## Run the client
+^[Top](#top)
 
 ```sh
 pm3
@@ -85,6 +109,7 @@ proxmark3 /dev/tty.usbmodemiceman1
 ```
 
 ## Next steps
+^[Top](#top)
 
 For the next steps, please read the following pages:
 
@@ -96,6 +121,7 @@ For the next steps, please read the following pages:
 
 
 # Homebrew (Mac OS X), developer installation
+^[Top](#top)
 
 These instructions will show how to setup the environment on OSX to the point where you'll be able to clone and compile the repo by yourself, as on Linux, Windows, etc.
 
@@ -115,6 +141,7 @@ brew install astyle
 
 
 ## Compile and use the project
+^[Top](#top)
 
 To use the compiled client, the only difference is that the Proxmark3 port is `/dev/tty.usbmodemiceman1`, so commands become:
 
diff --git a/doc/md/Installation_Instructions/Mac-OS-X-MacPorts-Installation-Instructions.md b/doc/md/Installation_Instructions/Mac-OS-X-MacPorts-Installation-Instructions.md
index 6fb66b6e0..94294296e 100644
--- a/doc/md/Installation_Instructions/Mac-OS-X-MacPorts-Installation-Instructions.md
+++ b/doc/md/Installation_Instructions/Mac-OS-X-MacPorts-Installation-Instructions.md
@@ -1,8 +1,22 @@
-# MacPorts (Mac OS X)
 
+<a id="Top"></a>
+
+# Mac OS X - MacPorts automatic installation
 <b><h3>These insturctions won't work on Apple Silicon yet!</h3> An arm64 native build of arm-none-eabi-gcc is still not available (as of 2021-11-26).</b>
 
+# Table of Contents
+- [Mac OS X - MacPorts automatic installation](#mac-os-x---macports-automatic-installation)
+- [Table of Contents](#table-of-contents)
+  - [Main prerequisite](#main-prerequisite)
+  - [Installing latest releases](#installing-latest-releases)
+  - [Build from source](#build-from-source)
+  - [Compile and use the project](#compile-and-use-the-project)
+
+
+
+
 ## Main prerequisite
+^[Top](#top)
 
 1. Have MacPorts installed. Visit https://www.macports.org/ for more information.
 
@@ -24,12 +38,14 @@
     ```
 
 ## Installing latest releases
+^[Top](#top)
 
 Packaging for latest releases are available on MacPorts with the port name `proxmark3-iceman`, with a variant for PM3GENERIC firmwares available as `+pm3generic`.
 
 Installing is as simple as `sudo port install proxmark3-iceman` and if you want to install for PM3GENERIC, you can run `sudo port install proxmark3-iceman +pm3generic` instead.
 
 ## Build from source
+^[Top](#top)
 
 These instructions will show how to setup the environment on OSX to the point where you'll be able to clone and compile the repo by yourself, as on Linux, Windows, etc.
 
@@ -72,6 +88,7 @@ These instructions will show how to setup the environment on OSX to the point wh
 
 
 ## Compile and use the project
+^[Top](#top)
 
 Now you're ready to follow the [compilation instructions](/doc/md/Use_of_Proxmark/0_Compilation-Instructions.md).
 
diff --git a/doc/md/Installation_Instructions/ModemManager-Must-Be-Discarded.md b/doc/md/Installation_Instructions/ModemManager-Must-Be-Discarded.md
index 7025da4d3..17bce5cbf 100644
--- a/doc/md/Installation_Instructions/ModemManager-Must-Be-Discarded.md
+++ b/doc/md/Installation_Instructions/ModemManager-Must-Be-Discarded.md
@@ -1,4 +1,24 @@
+
+<a id="Top"></a>
+
+# Modem Manager must be discarded
+
+
+# Table of Contents
+- [Modem Manager must be discarded](#modem-manager-must-be-discarded)
+- [Table of Contents](#table-of-contents)
+- [If you're a Linux user](#if-youre-a-linux-user)
+- [Solution 1: remove ModemManager](#solution-1-remove-modemmanager)
+- [Solution 2: disable ModemManager](#solution-2-disable-modemmanager)
+- [Solution 3: use filtering udev rules](#solution-3-use-filtering-udev-rules)
+- [Solution 4: use global ttyACM filtering rule](#solution-4-use-global-ttyacm-filtering-rule)
+- [Testing ModemManager filtering effectiveness](#testing-modemmanager-filtering-effectiveness)
+- [I didn't read carefully this page and now my Proxmark3 is not responding](#i-didnt-read-carefully-this-page-and-now-my-proxmark3-is-not-responding)
+  - [Only the fullimage is damaged](#only-the-fullimage-is-damaged)
+  - [The bootloader is damaged](#the-bootloader-is-damaged)
+
 # If you're a Linux user
+^[Top](#top)
 
 ModemManager is a real threat that can lead to a bricked Proxmark3, read this very attentively.
 
@@ -14,6 +34,7 @@ Yes it makes the flashing failing. And if it happens while you're flashing the b
 ModemManager is a threat for the Proxmark3, but also for many other embedded devices, such as some Arduino platforms.
 
 # Solution 1: remove ModemManager
+^[Top](#top)
 
 If you don't need ModemManager, the safest is to remove it entirely.
 
@@ -27,6 +48,7 @@ sudo pacman -R modemmanager
 ```
 
 # Solution 2: disable ModemManager
+^[Top](#top)
 
 ```sh
 sudo systemctl stop ModemManager
@@ -34,6 +56,7 @@ sudo systemctl disable ModemManager
 ```
 
 # Solution 3: use filtering udev rules
+^[Top](#top)
 
 If you *really* need ModemManager, e.g. for your 4G device, you'll have to use some filtering rules to make sure it doesn't interfere with the Proxmark3. 
 
@@ -59,6 +82,7 @@ If it's using `filter-policy=strict`, either look at [solution 4](#solution-4-us
 In any case, it's very important that you test if the filtering is effective before attempting to flash your Proxmark3, see section [Testing ModemManager filtering effectiveness](#Testing-ModemManager-filtering-effectiveness).
 
 # Solution 4: use global ttyACM filtering rule
+^[Top](#top)
 
 Edit the system ModemManager configuration:
 ```sh
@@ -77,6 +101,7 @@ sudo service ModemManager restart
 It's very important that you test if the filtering is effective before attempting to flash your Proxmark3, see section [Testing ModemManager filtering effectiveness](#Testing-ModemManager-filtering-effectiveness).
 
 # Testing ModemManager filtering effectiveness
+^[Top](#top)
 
 If you chose to keep ModemManager, test the filtering effectiveness before attempting to flash Proxmark3.
 
@@ -107,12 +132,14 @@ sudo mmcli -G ERR
 ```
 
 # I didn't read carefully this page and now my Proxmark3 is not responding
+^[Top](#top)
 
 First of all, follow the instructions above to make sure ModemManager will not interfere with the Proxmark3 anymore.
 
 Now there are two possibilities:
 
 ## Only the fullimage is damaged
+^[Top](#top)
 
 If the flashing of the fullimage failed, you can still force the Proxmark to start in bootloader mode by keeping the button pressed while you're plugging it in and while you're attempting to flash it again.
 
@@ -126,7 +153,9 @@ In short:
 * release button
 * un/plug device
 
+
 ## The bootloader is damaged
+^[Top](#top)
 
 If attempting to flash via the button fails, this means your bootloader is corrupted.
 You'll have no other choice than flashing it via an external JTAG instrument.
diff --git a/doc/md/Installation_Instructions/Troubleshooting.md b/doc/md/Installation_Instructions/Troubleshooting.md
index 9f8e91dfd..141d62b41 100644
--- a/doc/md/Installation_Instructions/Troubleshooting.md
+++ b/doc/md/Installation_Instructions/Troubleshooting.md
@@ -1,6 +1,6 @@
-# Troubleshooting guide
+<a id="Top"></a>
 
-## First of all
+# Troubleshooting guide
 
 Always use the latest repository commits from *master* branch. There are always many fixes done almost daily.
 
@@ -43,8 +43,10 @@ Note that with the Bluetooth adapter, you *have to* use directly the client, and
 * [Bluetooth](/doc/bt_manual_v10.md)
 
 ## My Proxmark3 seems bricked
+^[Top](#top)
 
 ### Maybe just a false alarm?
+^[Top](#top)
 
 The flasher refused to flash your Proxmark3? Are there any messages in *red*? The most common reason is that the Proxmark3 RDV4 firmware recently got a new bootloader able to handle larger firmwares and... the image grew over 256k almost at the same time. So your old bootloader can't flash such new images. But it's easy, you just need to flash *first* the bootloader *only*, then the image.
 
@@ -59,12 +61,14 @@ proxmark3 <YOUR_PORT_HERE> --flash --image armsrc/obj/fullimage.elf
 ```
 
 ### Find out why it would be bricked
+^[Top](#top)
 
 The most common reason of a flashing failure is the interference of ModemManager, read carefully [how to avoid ModemManager-related issues](/doc/md/Installation_Instructions/ModemManager-Must-Be-Discarded.md) and fix your setup!
 
 Another possibility is if, when using the button for entering bootloader mode, the button was released during flashing (for old bootloaders) or the button was pressed again during flashing (for newer bootloaders).
 
 ### Determine if the bootloader was damaged or only the main OS image
+^[Top](#top)
 
 Unplug, press the Proxmark3 button and keep it pressed when you plug it on USB. If the red LEDs show a "off/on/off/on" pattern, you're good, you manually entered into the bootloader mode.
 On new bootloaders, you can release the button. If the pattern disappears, you're on an older bootloader and you've to do it again and keep the button pressed during all the flashing operation. 
@@ -90,12 +94,14 @@ proxmark3 <YOUR_PORT_HERE> --flash --unlock-bootloader --image bootrom/obj/bootr
 ```
 
 ### Ok, my bootloader is definitively dead, now what?
+^[Top](#top)
 
 At this point, only reflashing via JTAG can revive your Proxmark3.
 
 See [details here](/doc/jtag_notes.md).
 
 ## Slow to boot or difficulties to enumerate the device over USB
+^[Top](#top)
 
 You're using another Proxmark3 than a RDV4?
 The RDV4 firmware can run on other Proxmark3 as such but the booting procedure is a bit slower because of the absence of SIM and external flash.
@@ -103,16 +109,19 @@ Make sure to configure properly your `Makefile.platform` to get a firmware bette
 See [details here](/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md).
 
 ## Troubles with SIM card reader
+^[Top](#top)
 
 (RDV4 only) Make sure you've the latest SIM firmware according to the [configuration documentation](/doc/md/Use_of_Proxmark/2_Configuration-and-Verification.md#verify-sim-module-firmware-version).
 
 ## Troubles with t5577 commands or MFC/iClass/T55x7 dictionaries
+^[Top](#top)
 
 (RDV4 only) Make sure you've set everything up according to the [configuration documentation](/doc/md/Use_of_Proxmark/2_Configuration-and-Verification.md#first-things-on-your-rdv40).
 
 Instructions evolve over time so check if you're still up to date!
 
 ## File not found
+^[Top](#top)
 
 If Proxmark3 has been installed with `make install` or packaged for your distro, the binaries should be in your path and you can call them directly:
 
@@ -161,16 +170,20 @@ pm3 --> smart upgrade -f sim011.bin
 etc.
 
 ## Pixmap / pixbuf warnings
+^[Top](#top)
 
 If you get warnings related to pixmap or pixbuf such as *Pixbuf theme: Cannot load pixmap file* or *Invalid borders specified for theme pixmap*, it's a problem of your Theme, try another one and the problem should vanish. See e.g. [#354](https://github.com/RfidResearchGroup/proxmark3/issues/354) (Yaru theme on Ubuntu) and [#386](https://github.com/RfidResearchGroup/proxmark3/issues/386) (Kali-X theme on Kali).
 
 ## Usb cable
+^[Top](#top)
 
 It's needed to have a good USB cable to connect Proxmark3 to USB. If you have stability problems (Proxmark3 resets, firmware hangs, especially firmware hangs just after start, etc.) 
 
 - check your cable with a USB tester (or try to change it). It needs to have a resistance smaller or equal to 0.3 Ohm.
 
 ## WSL
+^[Top](#top)
+
 When ```explorer.exe .``` doesn't work.  
 Trying to access the dump files created in WSL,  you will need to run ```explorer.exe .```  but sometimes this doesn't work.
 [As seen here](https://github.com/microsoft/WSL/issues/4027)  they suggest checking the following registry value for *P9NP*
@@ -178,11 +191,15 @@ Trying to access the dump files created in WSL,  you will need to run ```explore
 [![screenshot of regedit](/doc/md/Installation_Instructions/wsl2_p9np.png)](/doc/md/Installation_Instructions/wsl2_p9np.png)
 
 ## Troubles with running the Proxmark3 client
+^[Top](#top)
+
 Some reports has stated that they needed to execute the Proxmark3 as root on their *nix system.  
 Try running it with
     `sudo ./pm3`  
 
 ## libQt5Core.so.5 not found
+^[Top](#top)
+
 On WSL1 / updated to Ubuntu 20.04,  there is a slight chance you experience problems when compiling the repo with QT5.
 The following steps is needed to make the development environment happy again.   
 ```
@@ -191,6 +208,8 @@ sudo strip --remove-section=.note.ABI-tag /usr/lib/x86_64-linux-gnu/libQt5Core.s
 ```
 
 ## target attribute is not supported on this machine
+^[Top](#top)
+
 If you get the message ```error: target attribute is not supported on this machine [-Werror=attributes]```
 when trying to compile,  its because you have an older arm-none-eabi tool chain. 
 
@@ -224,6 +243,8 @@ ticks.h:26:1: error: target attribute is not supported on this machine [-Werror=
 ```
 
 ## Qt Session management error
+^[Top](#top)
+
 If you get the message  
 
 ```
diff --git a/doc/md/Installation_Instructions/VSCode-Installation-Instructions.md b/doc/md/Installation_Instructions/VSCode-Installation-Instructions.md
index 079f301f6..482b52af1 100644
--- a/doc/md/Installation_Instructions/VSCode-Installation-Instructions.md
+++ b/doc/md/Installation_Instructions/VSCode-Installation-Instructions.md
@@ -1,4 +1,19 @@
+<a id="Top"></a>
+
+# Visual Studio Code Installation Instructions
+
+# Table of Contents
+- [Visual Studio Code Installation Instructions](#visual-studio-code-installation-instructions)
+- [Table of Contents](#table-of-contents)
+- [Visual Studio Code for debugging](#visual-studio-code-for-debugging)
+  - [Debian / Ubuntu / Kali / ParrotOS / Raspbian](#debian--ubuntu--kali--parrotos--raspbian)
+  - [Windows: WSL](#windows-wsl)
+  - [Windows: ProxSpace](#windows-proxspace)
+
+
+
 # Visual Studio Code for debugging
+^[Top](#top)
 
 Download and install [Visual Studio Code](https://code.visualstudio.com/) 
 
@@ -6,6 +21,7 @@ Download and install [J-Link Software and Documentation pack](https://www.segger
 
 
 ## Debian / Ubuntu / Kali / ParrotOS / Raspbian
+^[Top](#top)
 
 Install dependencies
 
@@ -27,6 +43,7 @@ now launch Visual Studio Code and open your project folder
 
 
 ## Windows: WSL
+^[Top](#top)
 
 Enter WSL prompt (`wsl` or `start windows terminal`)
 
@@ -52,6 +69,7 @@ code .
 
 
 ## Windows: ProxSpace
+^[Top](#top)
 
 Download and install [Visual Studio Code](https://code.visualstudio.com/) 
 
diff --git a/doc/md/Installation_Instructions/Windows-Installation-Instructions.md b/doc/md/Installation_Instructions/Windows-Installation-Instructions.md
index 6430b2bc2..f46a41cb6 100644
--- a/doc/md/Installation_Instructions/Windows-Installation-Instructions.md
+++ b/doc/md/Installation_Instructions/Windows-Installation-Instructions.md
@@ -1,11 +1,30 @@
-# Installing on Windows
 <a id="top"></a>
 
+# Windows Installation instructions
+
+
 ## Table of Contents
+- [Windows Installation instructions](#windows-installation-instructions)
+  - [Table of Contents](#table-of-contents)
+  - [Installing dev-environment with ProxSpace](#installing-dev-environment-with-proxspace)
+  - [Video Installation guide](#video-installation-guide)
+  - [Driver Installation ( Windows 7 )](#driver-installation--windows-7-)
+  - [Download ProxSpace repo](#download-proxspace-repo)
+  - [Launch ProxSpace](#launch-proxspace)
+  - [Clone the RRG/Iceman repository](#clone-the-rrgiceman-repository)
+  - [Compile and use the project](#compile-and-use-the-project)
+  - [Done!](#done)
+- [Installing pre-compiled binaries with ProxSpace](#installing-pre-compiled-binaries-with-proxspace)
+- [Installing dev-environment with WSL 1](#installing-dev-environment-with-wsl-1)
+    - [Stay away from WSL 2](#stay-away-from-wsl-2)
+    - [More about WSL](#more-about-wsl)
+  - [X Server Installation](#x-server-installation)
+  - [Windows Terminal Installation](#windows-terminal-installation)
+  - [Dependencies](#dependencies)
+  - [Clone the RRG/Iceman repository](#clone-the-rrgiceman-repository-1)
+  - [Compile and use the project](#compile-and-use-the-project-1)
+  - [Done!](#done-1)
 
-  * [Installing dev-environment with ProxSpace](#installing-dev-environment-with-proxspace)
-  * [Installing pre-compiled binaries with ProxSpace](#installing-pre-compiled-binaries-with-proxspace)
-  * [Installing dev-environment with WSL 1](#installing-dev-environment-with-wsl-1)
 
 There are two ways to install, build and use Proxmark3 on Windows:
 
@@ -20,10 +39,13 @@ We have listed three ways to use these two setups  (dev environment vs pre-compi
 ^[Top](#top)
 
 ## Video Installation guide
+^[Top](#top)
+
 _note:  this video is out-of-date but still informative_
 [![Windows Installation tutorial](https://raw.githubusercontent.com/Chrissy-Morgan/Proxmark3-RDV4-ParrotOS/master/screenshot-www.youtube.com-2019.03.17-20-44-33.png)](https://youtu.be/zzF0NCMJnYU "Windows Installation Tutorial")
 
 ## Driver Installation ( Windows 7 )
+^[Top](#top)
 
 _note: for Windows 7 you will this step.  On a later Windows edition skip this._
 
@@ -31,6 +53,7 @@ Install required drivers for your Windows installation. You may need admin privi
 Step by step guides are online such as [RyscCorps](https://store.ryscc.com/blogs/news/how-to-install-a-proxmark3-driver-on-windows-10).
 
 ## Download ProxSpace repo
+^[Top](#top)
 
 Download the Gator96100 ProxSpace package from https://github.com/Gator96100/ProxSpace/releases
 
@@ -50,6 +73,7 @@ C:\My Documents\My Projects\proxspace
 If you're running Windows in a Virtualbox guest, make sure not to install ProxSpace on a vbox shared drive. (It's ok later to move the `/pm3` subfolder to a shared drive and edit the `*.bat`)
 
 ## Launch ProxSpace
+^[Top](#top)
 
 Run `runme64.bat`.
 
@@ -58,6 +82,7 @@ You'll get a Bash prompt and your home directory should become the ProxSpace `pm
 Please note you will need to use `/` in paths as you are using Bash.
 
 ## Clone the RRG/Iceman repository
+^[Top](#top)
 
 ```sh
 cd
@@ -72,10 +97,12 @@ pacman -S mingw-w64-x86_64-astyle
 ```
 
 ## Compile and use the project
+^[Top](#top)
 
 To use the compiled client, the only differences are that executables end with `.exe` (e.g. `proxmark3.exe`) and that the Proxmark3 port is one of your `comX` ports where "X" is the com port number assigned to proxmark3 under Windows, so commands like `proxmark3 /dev/ttyACMX` become `proxmark3.exe comX`.
 
 ## Done!
+^[Top](#top)
 
 Now you're ready to follow the [compilation instructions](/doc/md/Use_of_Proxmark/0_Compilation-Instructions.md).
 
@@ -96,9 +123,13 @@ It has excellent instructions to follow.
 WSL 1 requires to run on Windows 10 version 1709 or above. Previous windows versions didn't have support for COM ports.
 
 ### Stay away from WSL 2
+^[Top](#top)
+
 *Microsoft introduced WSL 2 starting on Windows 10 version 2004 with Hyper-V powering its virtualization; As of 2020-08-13, WSL 2 does not support USB and Serial.*
 
 ### More about WSL
+^[Top](#top)
+
 Install WSL 1 with e.g. the standard Ubuntu. You can follow the guide on [Microsoft Docs](https://docs.microsoft.com/en-us/windows/wsl/install-win10) but be careful to follow WSL 1 specific instructions! When they recommend you to restart, you must restart.
 
 For WSL configuration, see [Manage and configure Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/wsl-config).
@@ -106,6 +137,7 @@ For WSL configuration, see [Manage and configure Windows Subsystem for Linux](ht
 Make sure your WSL can launch Windows processes to get the `pm3` scripts working (cf `interop` in the WSL settings).
 
 ## X Server Installation
+^[Top](#top)
 
 If you want to run the graphical components of the Proxmark3 client, you need to install a X Server such as in the list below, and launch it, e.g. by executing XLaunch.
  * [VcXsrv](https://sourceforge.net/projects/vcxsrv/) 
@@ -113,11 +145,14 @@ If you want to run the graphical components of the Proxmark3 client, you need to
 
 
 ## Windows Terminal Installation
+^[Top](#top)
+
 Microsoft has recently released a new terminal for their OS. It is much better experience than old `cmd.exe` so we strongly recommend installing it.
 It is also open sourced (see [github.com/microsoft/terminal](https://github.com/microsoft/terminal)). You can download and install from [GitHub](https://github.com/microsoft/terminal/releases/latest) or [Microsoft Store](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701).
 
 
 ## Dependencies
+^[Top](#top)
 
 Enter WSL prompt (`wsl` or Start Windows Terminal with `wt`) and from there, follow the [Linux Installation Instructions](/doc/md/Installation_Instructions/Linux-Installation-Instructions.md) for Ubuntu, summarized here below:
 
@@ -140,12 +175,14 @@ If you don't need the graphical components of the Proxmark3 client, you can skip
 If you don't need support for Python3 scripts in the Proxmark3 client, you can skip the installation of `libpython3-dev`.
 
 ## Clone the RRG/Iceman repository
+^[Top](#top)
 
 ```sh
 git clone https://github.com/RfidResearchGroup/proxmark3.git
 ```
 
 ## Compile and use the project
+^[Top](#top)
 
 To use the compiled client, the only difference is that the Proxmark3 port is translated from your `comX` port where **"X"** is the com port number assigned to proxmark3 under Windows, to a `/dev/ttySX`, so commands become:
 
@@ -181,6 +218,7 @@ echo "export DISPLAY=:0" >> ~/.bashrc
 Note that it may take a quite long time for a freshly plugged Proxmark3 to be visible on a WSL /dev/ttySX port.
 
 ## Done!
+^[Top](#top)
 
 Now you're ready to follow the [compilation instructions](/doc/md/Use_of_Proxmark/0_Compilation-Instructions.md).
 
