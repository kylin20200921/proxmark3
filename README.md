commit 61d948e464c518544cc0533887bd254038f9d555
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Feb 12 12:06:27 2022 +0100

    text

diff --git a/doc/md/Use_of_Proxmark/1_Validation.md b/doc/md/Use_of_Proxmark/1_Validation.md
index 01669c01b..506e7ec82 100644
--- a/doc/md/Use_of_Proxmark/1_Validation.md
+++ b/doc/md/Use_of_Proxmark/1_Validation.md
@@ -15,28 +15,36 @@
 If all went well you should get some information about the firmware and memory usage as well as the prompt,  something like this.
 
 ```
-[=] Session log /home/iceman/.proxmark3/log_20210708.txt
+[=] Session log /home/iceman/.proxmark3/logs/log_20220212.txt
 [+] loaded from JSON file /home/iceman/.proxmark3/preferences.json
 [=] Using UART port /dev/ttyS3
 [=] Communicating with PM3 over USB-CDC
 
 
-██████╗ ███╗   ███╗ ████╗  
-██╔══██╗████╗ ████║   ══█║ 
-██████╔╝██╔████╔██║ ████╔╝ 
-██╔═══╝ ██║╚██╔╝██║   ══█║ 
-██║     ██║ ╚═╝ ██║ ████╔╝     Iceman ☕
-╚═╝     ╚═╝     ╚═╝ ╚═══╝  ❄️ bleeding edge
- 
- https://github.com/rfidresearchgroup/proxmark3/
+  ██████╗ ███╗   ███╗█████╗
+  ██╔══██╗████╗ ████║╚═══██╗
+  ██████╔╝██╔████╔██║ ████╔╝
+  ██╔═══╝ ██║╚██╔╝██║ ╚══██╗
+  ██║     ██║ ╚═╝ ██║█████╔╝
+  ╚═╝     ╚═╝     ╚═╝╚════╝     [ Iceman ❄️ ]
+
+
 
- [ Proxmark3 RFID instrument ] 
+
+ [ Proxmark3 RFID instrument ]
 
  [ CLIENT ]
-  client: RRG/Iceman/master/v4.13441-129-g60d132fcc 2021-07-08 22:00:00
-  compiled with GCC 10.3.0 OS:Linux ARCH:x86_64
- 
- [ PROXMARK RDV4 ]
+  RRG/Iceman/master/v4.14831-252 2022-02-08 05:03:08
+  compiled with............. GCC 10.3.0
+  platform.................. Linux / x86_64
+  Readline support.......... present
+  QT GUI support............ present
+  native BT support......... absent
+  Python script support..... present
+  Lua SWIG support.......... present
+  Python SWIG support....... present
+
+ [ PROXMARK3 ]
   device.................... RDV4
   firmware.................. RDV4
   external flash............ present
@@ -44,23 +52,23 @@ If all went well you should get some information about the firmware and memory u
   FPC USART for BT add-on... absent
 
  [ ARM ]
- bootrom: RRG/Iceman/master/v4.13441 2020-05-21 22:00:10
-      os: RRG/Iceman/master/v4.13441 2019-05-21 22:00:26
- compiled with GCC 9.2.1 20191025 (release) [ARM/arm-9-branch revision 277599]
+  bootrom: RRG/Iceman/master/v4.14831-176 2022-02-03 18:35:55
+       os: RRG/Iceman/master/v4.14831-205 2022-02-04 21:26:49
+  compiled with GCC 9.2.1 20191025 (release) [ARM/arm-9-branch revision 277599]
 
  [ FPGA ]
-  LF image built for 2s30vq100 on 2020-07-08 at 23: 8: 7
-  HF image built for 2s30vq100 on 2020-07-08 at 23: 8:19
-  HF FeliCa image built for 2s30vq100 on 2020-07-08 at 23: 8:30
+  LF image built for 2s30vq100 on 2020-07-08 at 23:08:07
+  HF image built for 2s30vq100 on 2020-07-08 at 23:08:19
+  HF FeliCa image built for 2s30vq100 on 2020-07-08 at 23:08:30
 
- [ Hardware ] 
+ [ Hardware ]
   --= uC: AT91SAM7S512 Rev A
   --= Embedded Processor: ARM7TDMI
   --= Internal SRAM size: 64K bytes
   --= Architecture identifier: AT91SAM7Sxx Series
   --= Embedded flash memory 512K bytes ( 59% used )
 
-[usb] pm3 --> 
+[usb] pm3 -->
 ```
 
 This `[usb] pm3 --> ` is the Proxmark3 interactive prompt.
