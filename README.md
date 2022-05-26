commit b73c59dc1e005081a7edf87ab7dc7cbb224bd53e
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Mar 20 09:54:39 2022 +0100

    correcting text

diff --git a/doc/md/Installation_Instructions/Mac-OS-X-Compile-From-Source-Instructions.md b/doc/md/Installation_Instructions/Mac-OS-X-Compile-From-Source-Instructions.md
index 9a1e10e8f..943c3372b 100644
--- a/doc/md/Installation_Instructions/Mac-OS-X-Compile-From-Source-Instructions.md
+++ b/doc/md/Installation_Instructions/Mac-OS-X-Compile-From-Source-Instructions.md
@@ -5,54 +5,23 @@
 # Table of Contents
 - [Mac OS X - Compilation from source instructions](x#mac-os-x---compilation-from-source-instructions)
 - [Table of Contents](#table-of-contents)
-  - [Installing build prerequisites via Homebrew](#installing-build-prerequisites-via-homebrew)
-  - [Configure the build](#configure-the-build)
-  - [Compilation from source](#compilation-from-source)
+  - [Follow Homebrew developer instructions](#follow-homebrew-developer-instructions)
+  - [(optional) Running without sudo](#optional-running-without-sudo)
 
-## Installing build prerequisites via Homebrew
-^[Top](#top)
-
-We need to install the ``openssl`` library with ``brew``:
-```bash
-brew install openssl
-```
-To run the local install script below, we also require the GNU versions 
-of several core Unix utilities:
-```bash
-brew install coreutils
-```
 
-## Configure the build
+## Follow Homebrew developer instructions
 ^[Top](#top)
 
-Clone the repository by running the following:
-```bash
-git clone https://github.com/RfidResearchGroup/proxmark3.git
-cd proxmark3
-```
-Configure the build by editing ``Makefile.platform`` 
-(this step is optional -- the default will suffice):
-```bash
-cp Makefile.platform.sample Makefile.platform
-vim Makefile.platform
-```
+Follow the instructions here [developer instructions](doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md#homebrew-mac-os-x-developer-installation) and you are done. 
 
-## Compilation from source
+
+## (optional) Running without sudo
 ^[Top](#top)
 
-The following command has been shown to work on MacOS Big Sur with the 
-default Compiler Tools that comes installed on this release of the platform:
-```bash
-make clean && CFLAGS="-I /usr/local/opt/openssl/include" make -j
-```
-The rest of this section is an *optional* installation procedure. 
+This section is an *optional* installation procedure. 
 
-If you have ``sudo`` rights, you can install the proxmark3 utilities into the system 
-path by running
-```bash
-sudo make install
-```
-Otherwise, assuming you are using the ``bash`` shell (using ``chsh -s /bin/bash `whoami```), we can create 
+
+Assuming you are using the ``bash`` shell (using ``chsh -s /bin/bash `whoami```), we can create 
 an alias to the relevant commands:
 ```bash
 export BASHRC="~/.bash_profile"
diff --git a/doc/md/Use_of_Proxmark/1_Validation.md b/doc/md/Use_of_Proxmark/1_Validation.md
index ba7f1600f..51a701388 100644
--- a/doc/md/Use_of_Proxmark/1_Validation.md
+++ b/doc/md/Use_of_Proxmark/1_Validation.md
@@ -58,9 +58,10 @@ If all went well you should get some information about the firmware and memory u
   compiled with GCC 9.2.1 20191025 (release) [ARM/arm-9-branch revision 277599]
 
  [ FPGA ]
-  LF image built for 2s30vq100 on 2020-07-08 at 23:08:07
-  HF image built for 2s30vq100 on 2020-07-08 at 23:08:19
-  HF FeliCa image built for 2s30vq100 on 2020-07-08 at 23:08:30
+  LF image 2s30vq100 2022-03-20 09:28:32
+  HF image 2s30vq100 2022-03-20 09:02:07
+  HF FeliCa image 2s30vq100 2022-03-20 09:28:28
+  HF 15 image 2s30vq100 2022-03-20 09:01:59
 
  [ Hardware ]
   --= uC: AT91SAM7S512 Rev A
@@ -79,6 +80,7 @@ This `[usb] pm3 --> ` is the Proxmark3 interactive prompt.
 ^[Top](#top)
 
 For basic help type `help`. Or for help on a set of sub commands type the command followed by `help`. For example `hf mf help`.
+All commands now implement `-h` or `--help` parameter to give basic instructions on how to use the command.
 
 ### First tests
 ^[Top](#top)
diff --git a/doc/md/Use_of_Proxmark/3_Commands-and-Features.md b/doc/md/Use_of_Proxmark/3_Commands-and-Features.md
index 3ba3f3895..d0bff17b6 100644
--- a/doc/md/Use_of_Proxmark/3_Commands-and-Features.md
+++ b/doc/md/Use_of_Proxmark/3_Commands-and-Features.md
@@ -22,6 +22,10 @@ Please make sure you've gone through the following pages firstly:
 
 As seen before, for basic help type `help`. Or for help on a set of sub commands type the command followed by `help`. For example `hf mf help`.  Many commands uses the `-h` / `--help` parameter to show a help text.
 
+The Proxmark3 client now also supports tab-autocomplete in both commands and filenames. Like for instance `hf mf a<tab>`  will give you a list of three availble commands.  
+This feature is quite powerful and similar to your normal shell experiences.
+
+
 ## New Features in RDV4
 ^[Top](#top)
 
