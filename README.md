commit 2523ac71778dcde98603098ced74c4cfcadf1c94
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Mar 20 09:34:36 2022 +0100

    text

diff --git a/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md b/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md
index 8821e4768..09747b435 100644
--- a/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md
+++ b/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md
@@ -42,17 +42,19 @@ Visual Studio Code still runs under Rosetta 2 and if you're developing for proxm
 These instructions comes from \@Chrisfu, where we got the proxmark3.rb scriptfile from.
 For further questions about Mac & Homebrew, contact [\@Chrisfu on Twitter](https://github.com/chrisfu/)
 
-0. Install XCode Command Line Tools if you haven't yet already done so: `xcode-select --install`
+0. Install XCode Command Line Tools if you haven't yet already done so:
+  - `xcode-select --install`
 
-1. Install homebrew if you haven't yet already done so: http://brew.sh/
-
-2. Install xquartz: `brew install xquartz`
+1. Install homebrew if you haven't yet already done so:
+  - http://brew.sh/
 
-3. Install sha256sum: `brew install coreutils`
-
-4. Tap this repo: `brew tap RfidResearchGroup/proxmark3`
+2. Install xquartz:
+  - `brew install xquartz`
+ 
+3. Tap this repo:
+  - `brew tap RfidResearchGroup/proxmark3`
 
-5. Install Proxmark3:
+4. Install Proxmark3:
   - `brew install proxmark3` for stable release 
   - `brew install --HEAD proxmark3` for latest non-stable from GitHub (use this if previous command fails)
   - `brew install --with-blueshark proxmark3` for blueshark support, stable release
@@ -94,7 +96,12 @@ If port detection failed, you'll have to call the flasher manually and specify t
 proxmark3 /dev/tty.usbmodemiceman1 --flash --unlock-bootloader --image /usr/local/share/proxmark3/firmware/bootrom.elf --image /usr/local/share/proxmark3/firmware/fullimage.elf
 ```
 
-> Depending on the firmware version your Proxmark3 can also appear as `/dev/tty.usbmodem881`.
+> Depending on the firmware version your Proxmark3 can also appear as:
+> `/dev/tty.usbmodem881`
+
+
+When compiling and you didn't flash the device,  the client will now warn that you have a full image that doesn't match the source code of your client.  If you know what you are doing, you can ignore it.
+
 
 
 ## Run the client
diff --git a/doc/md/Installation_Instructions/Mac-OS-X-MacPorts-Installation-Instructions.md b/doc/md/Installation_Instructions/Mac-OS-X-MacPorts-Installation-Instructions.md
index 78572dfce..4efd93ab3 100644
--- a/doc/md/Installation_Instructions/Mac-OS-X-MacPorts-Installation-Instructions.md
+++ b/doc/md/Installation_Instructions/Mac-OS-X-MacPorts-Installation-Instructions.md
@@ -54,7 +54,7 @@ These instructions will show how to setup the environment on OSX to the point wh
 2. Install dependencies:
 
     ```
-    sudo port install readline qt5 qt5-qtbase pkgconfig arm-none-eabi-gcc arm-none-eabi-binutils lua52 coreutils
+    sudo port install readline qt5 qt5-qtbase pkgconfig arm-none-eabi-gcc arm-none-eabi-binutils lua52 coreutils openssl@3
     ```
 
 3. Clamp Python version for pkg-config
