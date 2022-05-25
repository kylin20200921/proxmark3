commit 21cbfd61bd50c94c49225909f25a7e8bf60cd460
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Dec 31 11:42:15 2021 +0100

    text

diff --git a/doc/md/Use_of_Proxmark/0_Compilation-Instructions.md b/doc/md/Use_of_Proxmark/0_Compilation-Instructions.md
index 9ac9472ea..f15d74f3d 100644
--- a/doc/md/Use_of_Proxmark/0_Compilation-Instructions.md
+++ b/doc/md/Use_of_Proxmark/0_Compilation-Instructions.md
@@ -1,6 +1,25 @@
+<a id="Top"></a>
+
 # Compilation instructions
 
+# Table of Contents
+- [Compilation instructions](#compilation-instructions)
+- [Table of Contents](#table-of-contents)
+  - [Tuning compilation parameters](#tuning-compilation-parameters)
+    - [Compile for Proxmark3 RDV4](#compile-for-proxmark3-rdv4)
+    - [Compile for generic Proxmark3 platforms](#compile-for-generic-proxmark3-platforms)
+  - [Get the latest commits](#get-the-latest-commits)
+  - [Clean and compile everything](#clean-and-compile-everything)
+    - [if you got an error](#if-you-got-an-error)
+  - [Install](#install)
+  - [Flash the BOOTROM & FULLIMAGE](#flash-the-bootrom--fullimage)
+  - [Run the client](#run-the-client)
+  - [Next steps](#next-steps)
+
+
+
 ## Tuning compilation parameters
+^[Top](#top)
 
 The client and the Proxmark3 firmware should always be in sync.
 Nevertheless, the firmware can be tuned depending on the Proxmark3 platform and options.
@@ -10,13 +29,18 @@ Indeed, the RRG/Iceman fork can be used on other Proxmark3 hardware platforms as
 Via some definitions, you can adjust the firmware for a given platform, but also to add features like the support of the Blue Shark add-on or to select which standalone mode to embed. To learn how to adjust the firmware, please read [Advanced compilation parameters](/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md).
 
 ### Compile for Proxmark3 RDV4
+^[Top](#top)
+
 The repo defaults for compiling a firmware and client suitable for Proxmark3 RDV4.
 
 ### Compile for generic Proxmark3 platforms
+^[Top](#top)
+
 In order to build this repo for generic Proxmark3 platforms we urge you to read [Advanced compilation parameters](/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md)
 
 
 ## Get the latest commits
+^[Top](#top)
 
 ```sh
 cd proxmark3
@@ -24,12 +48,15 @@ git pull
 ```
 
 ## Clean and compile everything
+^[Top](#top)
 
 ```sh
 make clean && make -j
 ```
 
 ### if you got an error
+^[Top](#top)
+
 Read the [troubleshooting guide](/doc/md/Installation_Instructions/Troubleshooting.md), 
 
 For instance,  on WSl-1 you usually get the `libQt5Core.so.5 not found` message
@@ -37,6 +64,7 @@ For instance,  on WSl-1 you usually get the `libQt5Core.so.5 not found` message
 
 
 ## Install
+^[Top](#top)
 
 This is an optional step. If you do
 
@@ -51,6 +79,7 @@ The commands given in the documentation assume you did the installation step. If
 e.g. calling `./pm3` or `client/proxmark3` instead of just `pm3` or `proxmark3`.
 
 ## Flash the BOOTROM & FULLIMAGE
+^[Top](#top)
 
 In most cases, you can run the following script which try to auto-detect the port to use, on several OS:
 
@@ -77,6 +106,7 @@ proxmark3 /dev/ttyACM0 --flash --unlock-bootloader --image /tmp/my-bootrom.elf -
 ```
 
 ## Run the client
+^[Top](#top)
 
 In most cases, you can run the script `pm3` which try to auto-detect the port to use, on several OS.
 ```sh
@@ -98,6 +128,7 @@ client/proxmark3 /dev/ttyACM0
 ```
 
 ## Next steps
+^[Top](#top)
 
 For the next steps, please read the following pages:
 
diff --git a/doc/md/Use_of_Proxmark/1_Validation.md b/doc/md/Use_of_Proxmark/1_Validation.md
index 7e75a12c3..97852e9a1 100644
--- a/doc/md/Use_of_Proxmark/1_Validation.md
+++ b/doc/md/Use_of_Proxmark/1_Validation.md
@@ -1,4 +1,16 @@
-## 1. Validating proxmark client functionality
+<a id="Top"></a>
+
+# 1. Validating proxmark client functionality
+
+# Table of Contents
+- [1. Validating proxmark client functionality](#1-validating-proxmark-client-functionality)
+- [Table of Contents](#table-of-contents)
+    - [To get interactive help](#to-get-interactive-help)
+    - [First tests](#first-tests)
+    - [To quit the client](#to-quit-the-client)
+  - [Next steps](#next-steps)
+
+
 
 If all went well you should get some information about the firmware and memory usage as well as the prompt,  something like this.
 
@@ -55,10 +67,12 @@ This `[usb] pm3 --> ` is the Proxmark3 interactive prompt.
 
 
 ### To get interactive help
+^[Top](#top)
 
 For basic help type `help`. Or for help on a set of sub commands type the command followed by `help`. For example `hf mf help`.
 
 ### First tests
+^[Top](#top)
 
 These commands will return some info about your Proxmark software and hardware status.
 ```
@@ -70,12 +84,15 @@ These commands will return some info about your Proxmark software and hardware s
 You are now ready to use your newly flashed proxmark3 device.  Many commands uses the `h` parameter to show a help text.
 
 ### To quit the client
+^[Top](#top)
+
 ```
 [usb] pm3 --> quit
 ```
 or simple press `CTRL-D`.
 
 ## Next steps
+^[Top](#top)
 
 Some configuration steps are still needed.
 
diff --git a/doc/md/Use_of_Proxmark/2_Configuration-and-Verification.md b/doc/md/Use_of_Proxmark/2_Configuration-and-Verification.md
index 8ed9adf2b..175561062 100644
--- a/doc/md/Use_of_Proxmark/2_Configuration-and-Verification.md
+++ b/doc/md/Use_of_Proxmark/2_Configuration-and-Verification.md
@@ -1,4 +1,18 @@
+<a id="Top"></a>
+
+# 2. Configuration and Verification
+
+# Table of Contents
+- [2. Configuration and Verification](#2-configuration-and-verification)
+- [Table of Contents](#table-of-contents)
+    - [First things on your Proxmark3 RDV4](#first-things-on-your-proxmark3-rdv4)
+    - [Verify sim module firmware version](#verify-sim-module-firmware-version)
+  - [Next steps](#next-steps)
+
+
+
 ### First things on your Proxmark3 RDV4
+^[Top](#top)
 
 You will need to run these commands to make sure your RDV4 is prepared
 ```
@@ -22,6 +36,7 @@ Set all t55xx settings to defaults (will set all 4 at once)
 
 
 ### Verify sim module firmware version
+^[Top](#top)
 
 To make sure you got the latest sim module firmware.
 
@@ -91,6 +106,7 @@ Run hw status command to verify that the upgrade went well.
 ```
 
 ## Next steps
+^[Top](#top)
 
 For the next steps, please read the following page:
 
diff --git a/doc/md/Use_of_Proxmark/3_Commands-and-Features.md b/doc/md/Use_of_Proxmark/3_Commands-and-Features.md
index 59be59127..373a24ab4 100644
--- a/doc/md/Use_of_Proxmark/3_Commands-and-Features.md
+++ b/doc/md/Use_of_Proxmark/3_Commands-and-Features.md
@@ -1,5 +1,15 @@
+<a id="Top"></a>
+
+# 3. Commands and Features
+
+# Table of Contents
+- [3. Commands and Features](#3-commands-and-features)
+- [Table of Contents](#table-of-contents)
+  - [To get interactive help](#to-get-interactive-help)
+  - [New Features in RDV4](#new-features-in-rdv4)
+  - [Useful commands](#useful-commands)
+
 
-# Commands and Features
 
 Please make sure you've gone through the following pages firstly:
 
@@ -8,14 +18,17 @@ Please make sure you've gone through the following pages firstly:
 * [First Use and Verification](/doc/md/Use_of_Proxmark/2_Configuration-and-Verification.md)
 
 ## To get interactive help
+^[Top](#top)
 
 As seen before, for basic help type `help`. Or for help on a set of sub commands type the command followed by `help`. For example `hf mf help`.  Many commands uses the `-h` / `--help` parameter to show a help text.
 
 ## New Features in RDV4
+^[Top](#top)
 
 Further details coming soon
 
 ## Useful commands
+^[Top](#top)
 
 Here are some commands to start off with.
 
diff --git a/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md b/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md
index 920974c51..150ad762a 100644
--- a/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md
+++ b/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md
@@ -1,4 +1,19 @@
-# Advanced compilation parameters
+<a id="Top"></a>
+
+# 4. Advanced compilation parameters
+
+# Table of Contents
+- [4. Advanced compilation parameters](#4-advanced-compilation-parameters)
+- [Table of Contents](#table-of-contents)
+  - [Client](#client)
+  - [Firmware](#firmware)
+  - [PLATFORM](#platform)
+  - [PLATFORM_EXTRAS](#platform_extras)
+  - [STANDALONE](#standalone)
+  - [256kb versions](#256kb-versions)
+  - [Next step](#next-step)
+
+
 
 The client and the Proxmark3 firmware should always be in sync.
 Nevertheless, the firmware can be tuned depending on the Proxmark3 platform and options.
@@ -7,7 +22,10 @@ Indeed, the RRG/Iceman fork can be used on other Proxmark3 hardware platforms as
 
 Via some definitions, you can adjust the firmware for a given platform, but also to add features like the support of the Blue Shark add-on or to select which standalone mode to embed.
 
+
+
 ## Client
+^[Top](#top)
 
 The client doesn't depend on the capabilities of the Proxmark3 it's connected to.
 So you can use the same client for different Proxmark3 platforms, given that everything is running the same version.
@@ -28,6 +46,7 @@ make SKIPBT=1
 
 
 ## Firmware
+^[Top](#top)
 
 By default, the firmware is of course tuned for the Proxmark3 RDV4 device, which has built-in support for 256kb onboard flash SPI memory, Sim module (smart card support), FPC connector.
 These features make it very different from all other Proxmark3 devices, there is non other like this one.
@@ -38,6 +57,7 @@ If you need to tune things and save the configuration, create a file `Makefile.p
 For an up-to-date exhaustive list of options, you can run `make PLATFORM=`.
 
 ## PLATFORM
+^[Top](#top)
 
 Here are the supported values you can assign to `PLATFORM` in `Makefile.platform`:
 
@@ -58,6 +78,7 @@ Known issues:
 * Proxmark Pro:  it has different fpga and unknown pin assignments.  Unsupported.
 
 ## PLATFORM_EXTRAS
+^[Top](#top)
 
 Here are the supported values you can assign to `PLATFORM_EXTRAS` in `Makefile.platform`:
 
@@ -71,6 +92,7 @@ If you have installed a Blue Shark add-on on your RDV4, define `PLATFORM_EXTRAS=
 
 
 ## STANDALONE
+^[Top](#top)
 
 The RRG/Iceman repository gives you to easily choose which standalone mode to embed in the firmware.
 
@@ -109,6 +131,7 @@ Here are the supported values you can assign to `STANDALONE` in `Makefile.platfo
 By default `STANDALONE=LF_SAMYRUN`.
 
 ## 256kb versions
+^[Top](#top)
 
 If you own a Proxmark3 Easy with only 256kb, you can use a few definitions to help you getting a smaller firmware.
 
@@ -154,5 +177,6 @@ Situation might change when the firmware is growing of course, requiring to skip
 Last note: if you skip a tech, be careful not to use a standalone mode which requires that same tech, else the firmware size reduction won't be much.
 
 ## Next step
+^[Top](#top)
 
 See [Compilation instructions](/doc/md/Use_of_Proxmark/0_Compilation-Instructions.md)
