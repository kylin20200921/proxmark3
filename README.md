commit 2671795b8b9e836398219eecc287beb389820869
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Feb 26 17:15:32 2022 +0100

    textual

diff --git a/.github/ISSUE_TEMPLATE/bug_report.md b/.github/ISSUE_TEMPLATE/bug_report.md
index de546a42e..6ca8e6f6b 100644
--- a/.github/ISSUE_TEMPLATE/bug_report.md
+++ b/.github/ISSUE_TEMPLATE/bug_report.md
@@ -14,7 +14,7 @@ read the [troubleshooting guide](/doc/md/Installation_Instructions/Troubleshooti
 Try compiling with verbose.  `make VERBOSE=1` with main makefile or `make V=1` with cmake.
 
 ***flashing problems***
-Have you followed the instructions properly?  ie,  flashed bootrom seperately first if you are going from Offical repo to RRG/Iceman repo.
+Have you followed the instructions properly?  ie,  flashed bootrom seperately first if you are going from Offical repo to Iceman repo.
 
 
 -
diff --git a/client/src/cmdflashmem.c b/client/src/cmdflashmem.c
index b1cf1fdfd..97126ad9b 100644
--- a/client/src/cmdflashmem.c
+++ b/client/src/cmdflashmem.c
@@ -576,7 +576,7 @@ static int CmdFlashMemInfo(const char *Cmd) {
     bool is_keyok = (mbedtls_rsa_check_pubkey(rsa) == 0);
     PrintAndLogEx(
         (is_keyok) ? SUCCESS : FAILED,
-        "RRG/Iceman RSA public key check.... ( %s )",
+        "RDV4 RSA public key check.... ( %s )",
         (is_keyok) ?  _GREEN_("ok") : _RED_("fail")
     );
 
@@ -584,7 +584,7 @@ static int CmdFlashMemInfo(const char *Cmd) {
     if (verbose) {
         PrintAndLogEx(
             (is_keyok) ? SUCCESS : FAILED,
-            "RRG/Iceman RSA private key check... ( %s )",
+            "RDV4 RSA private key check... ( %s )",
             (is_keyok) ?  _GREEN_("ok") : _YELLOW_("N/A")
         );
     }
diff --git a/doc/T5577_Guide.md b/doc/T5577_Guide.md
index 6d25357df..e9792bb19 100644
--- a/doc/T5577_Guide.md
+++ b/doc/T5577_Guide.md
@@ -1,7 +1,7 @@
 # T5577 Introduction Guide
 <a id="Top"></a>
 
-### Based on RRG/Iceman Proxmark3 repo
+### Based on Iceman Proxmark3 repo
 
 ### Ver.1 8 Sep 2019
 ### Ver.2 7 March 2021
diff --git a/doc/md/Installation_Instructions/Linux-Installation-Instructions.md b/doc/md/Installation_Instructions/Linux-Installation-Instructions.md
index 4a53c52fe..e7fe580cd 100644
--- a/doc/md/Installation_Instructions/Linux-Installation-Instructions.md
+++ b/doc/md/Installation_Instructions/Linux-Installation-Instructions.md
@@ -137,7 +137,7 @@ And a new `/dev/ttyACM0` should have appeared.
 
 Add current user to the proper group to get permission to use `/dev/ttyACM0`.
 
-This step can be done from the RRG/Iceman Proxmark3 repo with:
+This step can be done from the Iceman Proxmark3 repo with:
 
 ```sh
 make accessrights
diff --git a/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md b/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md
index 91a2fedc4..e356bc2ba 100644
--- a/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md
+++ b/doc/md/Installation_Instructions/Mac-OS-X-Homebrew-Installation-Instructions.md
@@ -63,11 +63,11 @@ For more info, go to https://github.com/RfidResearchGroup/homebrew-proxmark3
 ## Upgrade HomeBrew tap formula
 ^[Top](#top)
 
-*This method is useful for those looking to run bleeding-edge versions of RRG/iceman's client. Keep this in mind when attempting to update your HomeBrew tap formula as this procedure could easily cause a build to break if an update is unstable on macOS.* 
+*This method is useful for those looking to run bleeding-edge versions of iceman's fork. Keep this in mind when attempting to update your HomeBrew tap formula as this procedure could easily cause a build to break if an update is unstable on macOS.* 
 
 Tested on macOS Mojave 10.14.4
 
-*Note: This assumes you have already installed RRG/iceman's fork from HomeBrew as mentioned above*
+*Note: This assumes you have already installed iceman's fork from HomeBrew as mentioned above*
 
 Force HomeBrew to pull the latest source from github
 
diff --git a/doc/md/Installation_Instructions/Windows-Installation-Instructions.md b/doc/md/Installation_Instructions/Windows-Installation-Instructions.md
index f46a41cb6..8bc9b9fc3 100644
--- a/doc/md/Installation_Instructions/Windows-Installation-Instructions.md
+++ b/doc/md/Installation_Instructions/Windows-Installation-Instructions.md
@@ -11,7 +11,7 @@
   - [Driver Installation ( Windows 7 )](#driver-installation--windows-7-)
   - [Download ProxSpace repo](#download-proxspace-repo)
   - [Launch ProxSpace](#launch-proxspace)
-  - [Clone the RRG/Iceman repository](#clone-the-rrgiceman-repository)
+  - [Clone the Iceman repository](#clone-the-iceman-repository)
   - [Compile and use the project](#compile-and-use-the-project)
   - [Done!](#done)
 - [Installing pre-compiled binaries with ProxSpace](#installing-pre-compiled-binaries-with-proxspace)
@@ -21,7 +21,7 @@
   - [X Server Installation](#x-server-installation)
   - [Windows Terminal Installation](#windows-terminal-installation)
   - [Dependencies](#dependencies)
-  - [Clone the RRG/Iceman repository](#clone-the-rrgiceman-repository-1)
+  - [Clone the Iceman repository](#clone-the-iceman-repository-1)
   - [Compile and use the project](#compile-and-use-the-project-1)
   - [Done!](#done-1)
 
@@ -81,7 +81,7 @@ You'll get a Bash prompt and your home directory should become the ProxSpace `pm
 
 Please note you will need to use `/` in paths as you are using Bash.
 
-## Clone the RRG/Iceman repository
+## Clone the Iceman repository
 ^[Top](#top)
 
 ```sh
@@ -174,7 +174,7 @@ _note_
 If you don't need the graphical components of the Proxmark3 client, you can skip the installation of `qtbase5-dev`.  
 If you don't need support for Python3 scripts in the Proxmark3 client, you can skip the installation of `libpython3-dev`.
 
-## Clone the RRG/Iceman repository
+## Clone the Iceman repository
 ^[Top](#top)
 
 ```sh
diff --git a/doc/md/Use_of_Proxmark/0_Compilation-Instructions.md b/doc/md/Use_of_Proxmark/0_Compilation-Instructions.md
index f15d74f3d..5de6a78c3 100644
--- a/doc/md/Use_of_Proxmark/0_Compilation-Instructions.md
+++ b/doc/md/Use_of_Proxmark/0_Compilation-Instructions.md
@@ -24,7 +24,7 @@
 The client and the Proxmark3 firmware should always be in sync.
 Nevertheless, the firmware can be tuned depending on the Proxmark3 platform and options.
 
-Indeed, the RRG/Iceman fork can be used on other Proxmark3 hardware platforms as well.
+Indeed, the Iceman fork can be used on other Proxmark3 hardware platforms as well.
 
 Via some definitions, you can adjust the firmware for a given platform, but also to add features like the support of the Blue Shark add-on or to select which standalone mode to embed. To learn how to adjust the firmware, please read [Advanced compilation parameters](/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md).
 
diff --git a/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md b/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md
index 90faa6e41..494f5a8ab 100644
--- a/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md
+++ b/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md
@@ -18,7 +18,7 @@
 The client and the Proxmark3 firmware should always be in sync.
 Nevertheless, the firmware can be tuned depending on the Proxmark3 platform and options.
 
-Indeed, the RRG/Iceman fork can be used on other Proxmark3 hardware platforms as well.
+Indeed, the Iceman fork can be used on other Proxmark3 hardware platforms as well.
 
 Via some definitions, you can adjust the firmware for a given platform, but also to add features like the support of the Blue Shark add-on or to select which standalone mode to embed.
 
@@ -94,7 +94,7 @@ If you have installed a Blue Shark add-on on your RDV4, define `PLATFORM_EXTRAS=
 ## STANDALONE
 ^[Top](#top)
 
-The RRG/Iceman repository gives you to easily choose which standalone mode to embed in the firmware.
+The Iceman repository gives you to easily choose which standalone mode to embed in the firmware.
 
 Here are the supported values you can assign to `STANDALONE` in `Makefile.platform`:
 
diff --git a/tools/mkversion.sh b/tools/mkversion.sh
index 31a1dd121..f950d9f19 100755
--- a/tools/mkversion.sh
+++ b/tools/mkversion.sh
@@ -15,7 +15,7 @@ if [ "$1" = "--short" ]; then
 fi
 
 # if you are making your own fork,  change this line to reflect your fork-name
-fullgitinfo="RRG/Iceman"
+fullgitinfo="Iceman"
 # GIT status  0 = dirty,  1 = clean ,  2 = undecided
 clean=2
 
diff --git a/tools/pm3_tests.sh b/tools/pm3_tests.sh
index ee1794bde..75b988285 100755
--- a/tools/pm3_tests.sh
+++ b/tools/pm3_tests.sh
@@ -211,7 +211,7 @@ function CheckExecute() {
   return $RESULT
 }
 
-echo -e "\n${C_BLUE}RRG/Iceman Proxmark3 test tool ${C_NC}\n"
+echo -e "\n${C_BLUE}Iceman Proxmark3 test tool ${C_NC}\n"
 
 echo -n "work directory: "
 pwd
