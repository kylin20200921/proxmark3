commit 8d843ca3634b89a79ff96228237f254ce4103233
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Feb 13 13:02:23 2022 +0100

    text

diff --git a/doc/md/Use_of_Proxmark/1_Validation.md b/doc/md/Use_of_Proxmark/1_Validation.md
index f49365019..ba7f1600f 100644
--- a/doc/md/Use_of_Proxmark/1_Validation.md
+++ b/doc/md/Use_of_Proxmark/1_Validation.md
@@ -15,26 +15,27 @@
 If all went well you should get some information about the firmware and memory usage as well as the prompt,  something like this.
 
 ```
-[=] Session log /home/iceman/.proxmark3/logs/log_20220212.txt
+[=] Session log /home/iceman/.proxmark3/logs/log_20220213.txt
 [+] loaded from JSON file /home/iceman/.proxmark3/preferences.json
 [=] Using UART port /dev/ttyS3
 [=] Communicating with PM3 over USB-CDC
 
 
-  ██████╗ ███╗   ███╗█████╗
-  ██╔══██╗████╗ ████║╚═══██╗
-  ██████╔╝██╔████╔██║ ████╔╝
-  ██╔═══╝ ██║╚██╔╝██║ ╚══██╗
-  ██║     ██║ ╚═╝ ██║█████╔╝
-  ╚═╝     ╚═╝     ╚═╝╚════╝     [ Iceman ❄️ ]
-
+  8888888b.  888b     d888  .d8888b.
+  888   Y88b 8888b   d8888 d88P  Y88b
+  888    888 88888b.d88888      .d88P
+  888   d88P 888Y88888P888     8888"
+  8888888P"  888 Y888P 888      "Y8b.
+  888        888  Y8P  888 888    888
+  888        888   "   888 Y88b  d88P
+  888        888       888  "Y8888P"    [ Iceman ❄️ ]
 
 
 
  [ Proxmark3 RFID instrument ]
 
  [ CLIENT ]
-  RRG/Iceman/master/v4.14831-252 2022-02-08 05:03:08
+  RRG/Iceman/master/v4.14831-269 2022-02-13 05:03:08
   compiled with............. GCC 10.3.0
   platform.................. Linux / x86_64
   Readline support.......... present
@@ -52,8 +53,8 @@ If all went well you should get some information about the firmware and memory u
   FPC USART for BT add-on... absent
 
  [ ARM ]
-  bootrom: RRG/Iceman/master/v4.14831-176 2022-02-03 18:35:55
-       os: RRG/Iceman/master/v4.14831-205 2022-02-04 21:26:49
+  bootrom: RRG/Iceman/master/v4.14831-269 2022-02-13 05:03:55
+       os: RRG/Iceman/master/v4.14831-269 2022-02-13 05:03:49
   compiled with GCC 9.2.1 20191025 (release) [ARM/arm-9-branch revision 277599]
 
  [ FPGA ]
diff --git a/doc/md/Use_of_Proxmark/3_Commands-and-Features.md b/doc/md/Use_of_Proxmark/3_Commands-and-Features.md
index 373a24ab4..3ba3f3895 100644
--- a/doc/md/Use_of_Proxmark/3_Commands-and-Features.md
+++ b/doc/md/Use_of_Proxmark/3_Commands-and-Features.md
@@ -48,3 +48,9 @@ To get info on a ISO14443-A tag:
 ```
 [usb] pm3 --> hf 14a info
 ```
+
+A good starting point is the following [Cheat sheet](/doc/cheatsheet.md)
+
+Or 
+
+this compilation of links to [Proxmark3 walk throughs](https://github.com/RfidResearchGroup/proxmark3/wiki/More-cheat-sheets)
