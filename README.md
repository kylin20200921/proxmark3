commit d5f39519a4d0e72fb755860b6a62528c68a68176
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Dec 31 11:04:05 2021 +0100

    text

diff --git a/doc/cloner_notes.md b/doc/cloner_notes.md
index 44f7fa013..83de05039 100644
--- a/doc/cloner_notes.md
+++ b/doc/cloner_notes.md
@@ -1,6 +1,5 @@
-<a id="Top"></a>
-
 # Notes on Cloner guns
+<a id="Top"></a>
 
 This document is based mostly on information posted on http://www.proxmark.org/forum/viewtopic.php?pid=39903#p39903
 
diff --git a/doc/jtag_notes.md b/doc/jtag_notes.md
index 1e6369767..eaea64b6e 100644
--- a/doc/jtag_notes.md
+++ b/doc/jtag_notes.md
@@ -1,8 +1,31 @@
+# Notes on JTAG
+<a id="top"></a>
+
 Some notes on how to reflash a bricked Proxmark3 over JTAG.
 
+# Table of Contents
+- [Notes on JTAG](#notes-on-jtag)
+- [Table of Contents](#table-of-contents)
+- [Linux and OpenOCD](#linux-and-openocd)
+  - [Using RDV4 scripts](#using-rdv4-scripts)
+  - [RDV4 pinout](#rdv4-pinout)
+  - [JLink pinout](#jlink-pinout)
+  - [Raspberry Pi pinout](#raspberry-pi-pinout)
+- [Where to find more information?](#where-to-find-more-information)
+  - [Third party notes on using a BusPirate](#third-party-notes-on-using-a-buspirate)
+  - [Third party notes on using a J-Link](#third-party-notes-on-using-a-j-link)
+  - [Third party notes on using a RaspBerry Pi](#third-party-notes-on-using-a-raspberry-pi)
+  - [Third party notes on using a J-Link on Windows](#third-party-notes-on-using-a-j-link-on-windows)
+  - [Stack Overflow write up article.](#stack-overflow-write-up-article)
+  - [Old original docs](#old-original-docs)
+
+
+
 # Linux and OpenOCD
+^[Top](#top)
 
 ## Using RDV4 scripts
+^[Top](#top)
 
 The RDV4 repository contains helper scripts for JTAG flashing.
 
@@ -22,12 +45,14 @@ In some rare situations, flashing the full image over JTAG may fail but the boot
 For advanced usages there are also `openocd_flash_dump.sh` for dumping the content of the Proxmark3 and `openocd_interactive.sh` for an OpenOCD console.
 
 ## RDV4 pinout
+^[Top](#top)
 
 The RDV4 JTAG header is quite smaller compared to other Proxmark3 platforms.  
 If you're using a J-Link, there is a [convenient adapter](https://github.com/RfidResearchGroup/proxmark3/wiki/Tools#jtag-adapter) made by Proxgrind.  
 You can also make yours with some 1.27mm headers (look for `1.27mm header` on Aliexpress) or Pogo pins or buy an already made clip, e.g. search `dykb clamp` on Aliexpress and take a 1.27mm single-row 6P version.
 
 ## JLink pinout
+^[Top](#top)
 
 J-Link [pinout](https://www.segger.com/interface-description.html):
 
@@ -48,6 +73,7 @@ GND | 6
 3.3 | 2
 
 ## Raspberry Pi pinout
+^[Top](#top)
 
 RPi [pinout](https://pinout.xyz/):
 
@@ -61,9 +87,12 @@ GND | 6
 3.3 | 1
 
 # Where to find more information?
+^[Top](#top)
+
 There has been lots of articles and blogposts about recovering, debricking, JTAG your Proxmark3 and you find here below an assortiment of resources that will be of help.
 
 ## Third party notes on using a BusPirate
+^[Top](#top)
 
 * https://github.com/Proxmark/proxmark3/wiki/Debricking-Proxmark3-with-buspirate
 * https://b4cktr4ck2.github.io/De-Brickify-Pm3-RDV2/
@@ -71,22 +100,29 @@ There has been lots of articles and blogposts about recovering, debricking, JTAG
 * https://joanbono.github.io/PoC/Flashing_Proxmark3.html
 
 ## Third party notes on using a J-Link
+^[Top](#top)
 
 * http://wiki.yobi.be/wiki/Proxmark
 
 ## Third party notes on using a RaspBerry Pi
+^[Top](#top)
 
 * http://www.lucasoldi.com/2017/01/17/unbrick-proxmark3-with-a-raspberry-pi-and-openocd/
 * https://wiki.elvis.science/index.php?title=Proxmark3:_Debricking
 * https://github.com/synthetos/PiOCD/wiki/Using-a-Raspberry-Pi-as-a-JTAG-Dongle
 
 ## Third party notes on using a J-Link on Windows
+^[Top](#top)
 
 * https://github.com/Proxmark/proxmark3/wiki/De-Bricking-Segger
 
 ## Stack Overflow write up article.
+^[Top](#top)
+
 https://stackoverflow.com/questions/48794076/error-halt-timed-out-wake-up-gdb/64291913#64291913
 
-## Old original doc,  
+## Old original docs
+^[Top](#top)
+
 Describes the SEGGER JLINK, JTAG process but be warned,  this document is old.
 https://github.com/RfidResearchGroup/proxmark3/blob/master/doc/original_proxmark3/Compiling%20Proxmark%20source%20and%20firmware%20upgrading%20v1.pdf
\ No newline at end of file
diff --git a/doc/loclass_notes.md b/doc/loclass_notes.md
index 741a81023..43398f749 100644
--- a/doc/loclass_notes.md
+++ b/doc/loclass_notes.md
@@ -1,4 +1,11 @@
-# About LOCLASS attack
+# Notes about the LOCLASS attack
+<a id="top"></a>
+
+# Table of Contents
+- [Notes about the LOCLASS attack](#notes-about-the-loclass-attack)
+- [Table of Contents](#table-of-contents)
+- [Unit testing](#unit-testing)
+
 
 This document is primarily intended for understanding `hf iclass loclass` and files used with it.
 
@@ -15,6 +22,8 @@ If you don't have access to a iClass SE reader configured in Elite mode there is
 
 
 # Unit testing
+^[Top](#top)
+
 In order to verify that loclass is actually working, there is a "unit" test mode.
 run `hf iclass loclass --test`.
 
diff --git a/doc/mfu_binary_format_notes.md b/doc/mfu_binary_format_notes.md
index 85935160b..a368265e6 100644
--- a/doc/mfu_binary_format_notes.md
+++ b/doc/mfu_binary_format_notes.md
@@ -1,11 +1,20 @@
 # Notes on MFU binary formats
+<a id="Top"></a>
+
+
+# Table of Contents
+- [Notes on MFU binary formats](#notes-on-mfu-binary-formats)
+- [Table of Contents](#table-of-contents)
+  - [New mfu format](#new-mfu-format)
+  - [Old mfu format](#old-mfu-format)
+  - [Plain mfu format](#plain-mfu-format)
+  - [future mfu format](#future-mfu-format)
+
 
-  - new mfu format
-  - old mfu format
-  - plain mfu format
-  - future mfu format
   
 ## New mfu format
+^[Top](#top)
+
 The new mfu binary format was created to compensate for different manufactures tag functions.
 Like UL-Ev1 has three counter and tearing bytes,  while NTAG only has one counter and tearing byte.
 PACK was removed from header, since its just normally part of the tag memory,  unreadable,  but when 
@@ -29,6 +38,8 @@ typedef struct {
 ```
 
 ## Old mfu format
+^[Top](#top)
+
 The old binary format saved the extra data on tag in order for the Proxmark3 to able to simulate a real tag.
 
 ```
@@ -47,6 +58,8 @@ typedef struct {
 ```
 
 ## Plain mfu format
+^[Top](#top)
+
 The first binary format for MFU was just a memory dump from the tag block 0 to end.
 No extra data was saved.  
 ```
@@ -54,6 +67,8 @@ No extra data was saved.
 ```
 
 ## future mfu format
+^[Top](#top)
+
 For developers of apps and other tools, like libnfc,   we don't recommend using binary formats.
 We decided to adopt a JSON based format,  which is much more flexible to changes of new tag functionality.
 
diff --git a/doc/new_frame_format.md b/doc/new_frame_format.md
index c5e7d542d..73c6faabb 100644
--- a/doc/new_frame_format.md
+++ b/doc/new_frame_format.md
@@ -1,11 +1,35 @@
 # New frame format documentation
+<a id="Top"></a>
 
 This document is primarily intended for developers only.
 
 A major change is the support of variable length frames between host and Proxmark3.  
 This is a step especially important for usage over FPC/USART/BT.
 
+
+# Table of Contents
+- [New frame format documentation](#new-frame-format-documentation)
+- [Table of Contents](#table-of-contents)
+  - [Old format](#old-format)
+  - [New format](#new-format)
+  - [Transition](#transition)
+  - [New format API](#new-format-api)
+    - [On the client, for sending frames](#on-the-client-for-sending-frames)
+    - [On the Proxmark3, for receiving frames](#on-the-proxmark3-for-receiving-frames)
+    - [On the Proxmark3, for sending frames](#on-the-proxmark3-for-sending-frames)
+    - [On the client, for receiving frames](#on-the-client-for-receiving-frames)
+  - [API transition](#api-transition)
+  - [Bootrom](#bootrom)
+    - [On the Proxmark3, for receiving frames](#on-the-proxmark3-for-receiving-frames-1)
+    - [On the Proxmark3, for sending frames](#on-the-proxmark3-for-sending-frames-1)
+    - [On the client, for sending frames](#on-the-client-for-sending-frames-1)
+    - [On the client, for receiving frames](#on-the-client-for-receiving-frames-1)
+  - [New usart RX FIFO](#new-usart-rx-fifo)
+  - [Timings](#timings)
+  - [Reference frames](#reference-frames)
+
 ## Old format
+^[Top](#top)
 
 Previously, frames were, in both directions like this:
 
@@ -21,6 +45,7 @@ So the frame size was fixed, 544 bytes, even for simple ACKs.
 When snooping the USB transfers, we can observe the host is sending 544b Bulk USB frames while the Proxmark3 is limited by its internal buffers and is sending 128b, 128b, 128b, 128b, 32b, so in total 5 packets.
 
 ## New format
+^[Top](#top)
 
 Even if we make the payload part variable in the old format, we've still a minimum of 32 bytes per frame with fields arbitrarily large.
 So we designed a new format from scratch:
@@ -74,10 +99,12 @@ Internal structures used to handle these packets are:
 But they are abstracted from the developer view with a new API. See below.
 
 ## Transition
+^[Top](#top)
 
 Because it's a long transition to clean all the code from the old format and because we don't want to break stuffs when flashing the bootloader, the old frames are still supported together with the new frames. The old structure is now called `PacketCommandOLD` and `PacketResponseOLD` and it's also abstracted from the developer view with the new API.
 
 ## New format API
+^[Top](#top)
 
 So the new API is a merge of the old and the new frame formats, to ensure a smooth transition.
 
@@ -114,7 +141,8 @@ After the full transition, we might remove the fields `oldarg` and `ng`.
     } PacketResponseNG;
 
 
-### On the client, for sending frames:
+### On the client, for sending frames
+^[Top](#top)
 
 (`client/comms.c`)
 
@@ -132,7 +160,8 @@ Warning : it makes sense to move from `SendCommandOLD` to `SendCommandMIX` only
 
 Internally these functions prepare the new or old frames and call `uart_communication` which calls `uart_send`.
 
-### On the Proxmark3, for receiving frames:
+### On the Proxmark3, for receiving frames
+^[Top](#top)
 
 (`armsrc/appmain.c`)
 
@@ -143,7 +172,8 @@ Internally these functions prepare the new or old frames and call `uart_communic
 `PacketReceive` is the commands broker.  
 Old handlers will still find their stuff in `PacketCommandNG.oldarg` field.
 
-### On the Proxmark3, for sending frames:
+### On the Proxmark3, for sending frames
+^[Top](#top)
 
 (`common/cmd.c`)
 
@@ -163,7 +193,8 @@ Example of a handler that supports both OLD/MIX and NG command styles and replie
         reply_mix(CMD_ACK, 0, 0, 0, packet->data.asBytes, packet->length);
     }
 
-### On the client, for receiving frames:
+### On the client, for receiving frames
+^[Top](#top)
 
 (`client/comms.c`)
 
@@ -174,6 +205,7 @@ Example of a handler that supports both OLD/MIX and NG command styles and replie
 Commands do `WaitForResponseTimeoutW` (or `dl_it`) which uses `getReply` to fetch responses.
 
 ## API transition
+^[Top](#top)
 
 In short, to move from one format to the other, we need for each command:
 
@@ -188,6 +220,7 @@ Meanwhile, a fast transition to MIX frames can be done with:
 * (pm3 TX) `reply_old` ⇒ `reply_mix` (but check the limited data size PM3_CMD_DATA_SIZE ⇒ PM3_CMD_DATA_SIZE_MIX)
 
 ## Bootrom
+^[Top](#top)
 
 Bootrom code will still use the old frame format to remain compatible with other repos supporting the old format and because it would hardly gain anything from the new format:
 * almost all frames convey 512b of payload, so difference in overhead is negligible
@@ -195,7 +228,8 @@ Bootrom code will still use the old frame format to remain compatible with other
 
 `SendCommandBL` is the same as `SendCommandOLD` with a different name to be sure not to migrate it.
 
-### On the Proxmark3, for receiving frames:
+### On the Proxmark3, for receiving frames
+^[Top](#top)
 
 (`bootrom/bootrom.c`)
 
@@ -204,7 +238,8 @@ Bootrom code will still use the old frame format to remain compatible with other
 
 also `usb_enable`, `usb_disable` (`common/usb_cdc.c`)
 
-### On the Proxmark3, for sending frames:
+### On the Proxmark3, for sending frames
+^[Top](#top)
 
 (`bootrom/bootrom.c`)
 
@@ -212,7 +247,8 @@ also `usb_enable`, `usb_disable` (`common/usb_cdc.c`)
 
 also `usb_enable`, `usb_disable` (`common/usb_cdc.c`)
 
-### On the client, for sending frames:
+### On the client, for sending frames
+^[Top](#top)
 
 Therefore, the flasher client (`client/flasher.c` + `client/flash.c`) must still use these old frames.  
 It uses a few commands in common with current client code:
@@ -222,13 +258,15 @@ It uses a few commands in common with current client code:
     SendCommandOLD
       ⇒ CMD_DEVICE_INFO / CMD_START_FLASH / CMD_FINISH_WRITE / CMD_HARDWARE_RESET
 
-### On the client, for receiving frames:
+### On the client, for receiving frames
+^[Top](#top)
 
 As usual, old frames are still supported
 
     WaitForResponseTimeout ⇒ PacketResponseNG
 
 ## New usart RX FIFO
+^[Top](#top)
 
 USART code has been rewritten to cope with unknown size packets.
 * using USART full duplex with double DMA buffer on RX & TX
@@ -264,6 +302,7 @@ USART code has been rewritten to cope with unknown size packets.
   * remember how many bytes we already copied to our FIFO
 
 ## Timings
+^[Top](#top)
 
 Reference (before new format):
 
@@ -416,7 +455,7 @@ Or if it's too complex to determine when we're sending the last command:
 
 
 ## Reference frames
-
+^[Top](#top)
 
 For helping debug...
 
