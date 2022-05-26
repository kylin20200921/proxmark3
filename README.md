commit b636544c1e23a0cc28bd8e92ef0388300eed6920
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Feb 27 12:27:12 2022 +0100

    text

diff --git a/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md b/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md
index e356bc2ba..8821e4768 100644
--- a/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md
+++ b/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md
@@ -48,9 +48,11 @@ For further questions about Mac & Homebrew, contact [\@Chrisfu on Twitter](https
 
 2. Install xquartz: `brew install xquartz`
 
-2. Tap this repo: `brew tap RfidResearchGroup/proxmark3`
+3. Install sha256sum: `brew install coreutils`
 
-3. Install Proxmark3:
+4. Tap this repo: `brew tap RfidResearchGroup/proxmark3`
+
+5. Install Proxmark3:
   - `brew install proxmark3` for stable release 
   - `brew install --HEAD proxmark3` for latest non-stable from GitHub (use this if previous command fails)
   - `brew install --with-blueshark proxmark3` for blueshark support, stable release
@@ -130,7 +132,7 @@ These instructions will show how to setup the environment on OSX to the point wh
 2. Install dependencies:
 
 ```
-brew install readline qt5 pkgconfig
+brew install readline qt5 pkgconfig coreutils
 brew install RfidResearchGroup/proxmark3/arm-none-eabi-gcc
 ```
 3. (optional) Install makefile dependencies:
