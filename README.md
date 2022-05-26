commit bdc5941ee0f28bfc9bc6ce6e4ac40074d348f265
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Feb 3 20:26:36 2022 +0100

    text about openssl dependency

diff --git a/doc/md/Installation_Instructions/Linux-Installation-Instructions.md b/doc/md/Installation_Instructions/Linux-Installation-Instructions.md
index e9e17005b..4a53c52fe 100644
--- a/doc/md/Installation_Instructions/Linux-Installation-Instructions.md
+++ b/doc/md/Installation_Instructions/Linux-Installation-Instructions.md
@@ -11,7 +11,7 @@
   - [On ArchLinux](#on-archlinux)
   - [On Fedora](#on-fedora)
   - [On openSUSE](#on-opensuse)
-- [Clone the RRG/Iceman repository](#clone-the-rrgiceman-repository)
+- [Clone the Iceman repository](#clone-the-iceman-repository)
 - [Check ModemManager](#check-modemmanager)
     - [⚠️ Very important ⚠️](#️-very-important-️)
 - [Check connection](#check-connection)
@@ -50,7 +50,7 @@ Install the requirements
 
 ```sh
 sudo apt-get install --no-install-recommends git ca-certificates build-essential pkg-config \
-libreadline-dev gcc-arm-none-eabi libnewlib-dev qtbase5-dev libbz2-dev libbluetooth-dev libpython3-dev
+libreadline-dev gcc-arm-none-eabi libnewlib-dev qtbase5-dev libbz2-dev libbluetooth-dev libpython3-dev libssl-dev
 ```
 
 If you don't need the native Bluetooth support in the client, you can skip the installation of `libbluetooth-dev`.
@@ -78,7 +78,7 @@ If you don't need support for Python3 scripts in the Proxmark3 client, you can s
 ^[Top](#top)
 
 ```sh
-sudo dnf install git make gcc gcc-c++ arm-none-eabi-gcc-cs arm-none-eabi-newlib readline-devel bzip2-devel qt5-qtbase-devel bluez-libs-devel python3-devel libatomic
+sudo dnf install git make gcc gcc-c++ arm-none-eabi-gcc-cs arm-none-eabi-newlib readline-devel bzip2-devel qt5-qtbase-devel bluez-libs-devel python3-devel libatomic openssl-devel
 ```
 
 If you don't need the native Bluetooth support in the client, you can skip the installation of `bluez-libs-devel`.
@@ -91,7 +91,7 @@ If you don't need support for Python3 scripts in the Proxmark3 client, you can s
 ^[Top](#top)
 
 ```sh
-sudo zypper install git patterns-devel-base-devel_basis gcc-c++ readline-devel libbz2-devel cross-arm-none-gcc9 cross-arm-none-newlib-devel python3-devel libqt5-qtbase-devel
+sudo zypper install git patterns-devel-base-devel_basis gcc-c++ readline-devel libbz2-devel cross-arm-none-gcc9 cross-arm-none-newlib-devel python3-devel libqt5-qtbase-devel libopenssl-devel
 ```
 
 Note that Bluez is not available on openSUSE so the native Bluetooth support won't be available in the client.
@@ -100,7 +100,7 @@ If you don't need the graphical components of the Proxmark3 client (such as in `
 
 If you don't need support for Python3 scripts in the Proxmark3 client, you can skip the installation of `python3-devel`.
 
-# Clone the RRG/Iceman repository
+# Clone the Iceman repository
 ^[Top](#top)
 
 ```sh
