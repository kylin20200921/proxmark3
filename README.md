commit 7fb0856cdb32e39766d06624dd3f0c3b6db41e7a
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Dec 31 11:11:29 2021 +0100

    text

diff --git a/doc/T5577_Guide.md b/doc/T5577_Guide.md
index 4d973cf00..26ca77434 100644
--- a/doc/T5577_Guide.md
+++ b/doc/T5577_Guide.md
@@ -1,10 +1,13 @@
 # T5577 Introduction Guide
+<a id="Top"></a>
 
 ### Based on RRG/Iceman Proxmark3 repo
 
 ### Ver.1 8 Sep 2019
 ### Ver.2 7 March 2021
 
+# Table of Contents
+
 | Contents                                                                            |
 | ----------------------------------------------------------------------------------- |
 | [Part 1](#part-1)                                                                   |
@@ -21,8 +24,10 @@
 | [The configuration Block – Block 3 Page 1](#the-configuration-block-block-3-page-1) |
 
 # Part 1
+^[Top](#top)
 
 ## Introduction
+^[Top](#top)
 
 The T5577 is a generic LF (Low Frequency) RFID card that is used in the
 125 Khz frequency space. It is a good card to use to learn about RFID and
@@ -49,6 +54,7 @@ try these as we go. To do so, have a blank T5577 card that you can use
 for this purpose.
 
 ## T5577 Overview
+^[Top](#top)
 
 The T5577 is a chip that can hold data and a configuration (Section
 4.12).
@@ -63,6 +69,7 @@ the chip how to behave.
  
 
 ## What data is on my T5577
+^[Top](#top)
 
 Let’s have a look and see what a card might look like in the proxmark3
 software. Since we can change the configuration of how the T5577 will
@@ -121,6 +128,7 @@ I will cover the meaning of this data as we go, but for now, lets keep
 it simple.
 
 ## Read and Write Blocks of Data
+^[Top](#top)
 
 The basic function of using the proxmark3 with rfid cards is to read and
 write data. This reading and writing must be done in the correct way
@@ -224,6 +232,7 @@ can see the card)
     ```
 
 ### Exercise 1
+^[Top](#top)
 
 Using the read and write commands you have learnt see if you can make
 the lf t55 dump command show the following data for blocks 1-7 (Page 0).
@@ -261,6 +270,7 @@ If you forget this data/password, you won't be able to read or write
 to the card.
 
 ## How do I use a password
+^[Top](#top)
 
 This can be a little tricky for beginners. 
 ***If you forget your password you will lose access to your card***.
@@ -521,6 +531,7 @@ required, please do not proceed.
     Yes we can!  We can see Block 0 is the correct config 00088040
 
 # Part 2 – Configuration Blocks
+^[Top](#top)
 
 One of the things a lot of people have trouble with or miss, is that the
 T5577 has two different and separate communications protocols, each with
@@ -539,6 +550,7 @@ from Block 3 Page 1. If the command is not encoded correctly it will
 ignore the command and revert back to default read mode.
 
 ## The configuration Block – Block 0 Page 0
+^[Top](#top)
 
 For this configuration the settings chosen will be for the purpose of
 the card when used in production. E.G. If you want the card to act like
@@ -685,6 +697,7 @@ password set (if not, review and get you card back to this state).
     (Max Block = 2). On the T5577 these will be Blocks 1 and 2.
 
 ## Exercise 2
+^[Top](#top)
 
 Using the skills form part 1, see if you can view the data in blocks 1 and 2.
 
@@ -699,3 +712,6 @@ it, we can follow the password section and update the config from
 ***Important : Don’t forget to set a valid password in block 7 and remember it.***
 
 ## The configuration Block – Block 3 Page 1
+^[Top](#top)
+
+_to be written_
\ No newline at end of file
diff --git a/doc/path_notes.md b/doc/path_notes.md
index 491f01cd0..8f0d20b88 100644
--- a/doc/path_notes.md
+++ b/doc/path_notes.md
@@ -1,4 +1,28 @@
-# Notes on paths.
+# Notes on path
+<a id="Top"></a>
+
+# Table of Contents
+- [Notes on path](#notes-on-path)
+- [Table of Contents](#table-of-contents)
+- [Installed elements](#installed-elements)
+  - [Binaries](#binaries)
+  - [Firmwares](#firmwares)
+  - [Traces](#traces)
+  - [JTAG-related stuff](#jtag-related-stuff)
+  - [Proxmark3 client files: dictionaries](#proxmark3-client-files-dictionaries)
+  - [Proxmark3 client files: cmd scripts](#proxmark3-client-files-cmd-scripts)
+  - [Proxmark3 client files: Lua libraries and scripts](#proxmark3-client-files-lua-libraries-and-scripts)
+  - [Proxmark3 client files: various resources](#proxmark3-client-files-various-resources)
+  - [Documentation](#documentation)
+- [User files](#user-files)
+  - [.history / log files](#history--log-files)
+  - [Proxmark3 client files and traces](#proxmark3-client-files-and-traces)
+- [Searching files](#searching-files)
+  - [TL;DR](#tldr)
+  - [Gory details](#gory-details)
+- [Scripts](#scripts)
+    - [Proxmark command script (.cmd)](#proxmark-command-script-cmd)
+    - [Shebangs (on *nix)](#shebangs-on-nix)
 
 
 With the recent (2019-09-01) changes and creation of `make install`  command it is easy to get lost.
@@ -6,8 +30,10 @@ With the recent (2019-09-01) changes and creation of `make install`  command it
 If you install the Proxmark tools with `make install`, they will go under the prefix `/usr/local/` but if you install the tools from your distro, there are chances the path is `/usr` so you'll have to adapt the paths presented here.
 
 # Installed elements
+^[Top](#top)
 
 ## Binaries
+^[Top](#top)
 
 The main Proxmark3 executables / shellscripts will be copied to
 
@@ -29,6 +55,7 @@ Some more executable / scripts will be copied to
 
 
 ## Firmwares
+^[Top](#top)
 
 The recovery / firmware files will be copied to
 
@@ -41,6 +68,7 @@ The recovery / firmware files will be copied to
 
 
 ## Traces
+^[Top](#top)
 
 Proxmark3 client has a lot of sample trace files for many different low frequency tags. They will be copied to
 
@@ -50,6 +78,7 @@ Proxmark3 client has a lot of sample trace files for many different low frequenc
 
 
 ## JTAG-related stuff
+^[Top](#top)
 
 JTAG configurations and helper scripts for OpenOCD will be copied to
 
@@ -57,6 +86,7 @@ JTAG configurations and helper scripts for OpenOCD will be copied to
 
 
 ## Proxmark3 client files: dictionaries
+^[Top](#top)
 
 Dictionaries used by the client will be copied to
 
@@ -76,6 +106,7 @@ if key isn't a hex number, the key is ignored.
 See [here](#proxmark3-client-files-and-traces) how to add your own dictionaries.
 
 ## Proxmark3 client files: cmd scripts
+^[Top](#top)
 
 Cmd scripts used by the client will be copied to
 
@@ -86,6 +117,7 @@ Cmd scripts used by the client will be copied to
 See [here](#proxmark3-client-files-and-traces) how to add your own cmd scripts.
 
 ## Proxmark3 client files: Lua libraries and scripts
+^[Top](#top)
 
 Lua libraries and scripts used by the client will be copied to
 
@@ -99,6 +131,7 @@ Lua libraries and scripts used by the client will be copied to
 See [here](#proxmark3-client-files-and-traces) how to add your own Lua scripts.
 
 ## Proxmark3 client files: various resources
+^[Top](#top)
 
 Various resources used by the client will be copied to
 
@@ -111,6 +144,7 @@ It comprises the needed files for commands like hardnested, fido, EMV, iClass.
 See [here](#proxmark3-client-files-and-traces) how to add your own resources.
 
 ## Documentation
+^[Top](#top)
 
 Documentation will be copied to
 
@@ -119,10 +153,12 @@ Documentation will be copied to
 ```
 
 # User files
+^[Top](#top)
 
 The client will make use of a personal directory `~/.proxmark3` (or more precisely `$HOME/.proxmark3`)
 
 ## .history / log files
+^[Top](#top)
 
 We have now a rolling log file, created new per day.  All these logfiles and the history file are now located at
 
@@ -132,6 +168,7 @@ We have now a rolling log file, created new per day.  All these logfiles and the
 ```
 
 ## Proxmark3 client files and traces
+^[Top](#top)
 
 If you wants to add scripts, dictionaries or other resources, you can use the same structure as the installed directory structure and add your own files there, e.g.
 
@@ -148,10 +185,12 @@ If you add a file with the same name as the file provided with the Proxmark3 ins
 See also [Scripts](#scripts) on how to write your own scripts.
 
 # Searching files
+^[Top](#top)
 
 With the directory structure explained above, the client applies some heuristics to find its files or the files you specified in command line.
 
 ## TL;DR
+^[Top](#top)
 
 It adds the expected suffix if you didn't provide it yet, then it looks (by order of precedence):
 
@@ -161,6 +200,7 @@ It adds the expected suffix if you didn't provide it yet, then it looks (by orde
 4. in the installed directory structure, so it works when installed
 
 ## Gory details
+^[Top](#top)
 
 The client is using _searchFile_ (in _client/fileutils.c_) when calling a Proxmark3 command with a filename or when the client needs to find its files.
 _searchFile_ takes as argument a relative path *pm3dir*, a file to search and possibly a *suffix*.
@@ -177,11 +217,14 @@ When a user provides a filename (including possibly a path), _searchFile_ will s
 * Try to find the filename in the *pm3dir* relative to where the client binary is when in the installed configuration (*foo.dic* -> *$(path_to_dir_of_proxmark3_bin)/../share/proxmark3/dictionaries/foo.dic* which resolves to e.g. */usr/share/proxmark3/dictionaries/foo.dic* or */usr/local/share/proxmark3/dictionaries/foo.dic*), so when the client is executed from a repo workdir, filenames are searched in the expected location.
 
 # Scripts
+^[Top](#top)
 
 You can provide your own lua or cmd scripts.
 Look at existing scripts for ideas how to create your own scripts.
 
 ### Proxmark command script (.cmd)
+^[Top](#top)
+
 For cmd scripts, the command line scripts, the client can run a text file containing Proxmark3 commands.
 
 A samplefile could be like this.
@@ -210,6 +253,7 @@ You can skip the script file extension, it works equally well with.
 
 
 ### Shebangs (on *nix)
+^[Top](#top)
 
 You can also use the magic of shebangs to make an executable script, e.g. taking the example above, we can write:
 
diff --git a/doc/termux_notes.md b/doc/termux_notes.md
index ae7207ddf..bbef0da20 100644
--- a/doc/termux_notes.md
+++ b/doc/termux_notes.md
@@ -1,5 +1,6 @@
-<a id="top"></a>
 # Proxmark 3 on Android
+<a id="top"></a>
+
 ## Table of Contents
   * [ Requirements ](#requirements)
   * [ Notes ](#notes)
@@ -209,6 +210,7 @@ Alternatively, if you have made the client in the git repo:
 ^[Top](#top)
 
 #### BTADDON Missing in Firmware of PM3
+^[Top](#top)
 
 1. Phone and pm3 are connected, blue led is on and *not* blinking
 2. BTUART Tool TCP Server at Port 4321 
@@ -231,4 +233,3 @@ Solution:
 Make sure you have installed a firmware with BTADDON compiled. 
 See: https://github.com/RfidResearchGroup/proxmark3/blob/master/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md#platform_extras  
 
-ENJOY !
diff --git a/doc/trace_notes.md b/doc/trace_notes.md
index 6f89cffbc..a13b209e6 100644
--- a/doc/trace_notes.md
+++ b/doc/trace_notes.md
@@ -1,10 +1,18 @@
-<a id="top"></a>
 # Notes about the tracelog
+<a id="top"></a>
+
 
-## Table of Contents
- * [Command](#trace-command)
- * [File format](#tracelog-format)
- * [Wireshark dissector interoperability](#trace-and-wireshark)
+# Table of Contents
+- [Notes about the tracelog](#notes-about-the-tracelog)
+- [Table of Contents](#table-of-contents)
+  - [Trace command](#trace-command)
+    - [Timing](#timing)
+    - [Sources](#sources)
+    - [Data](#data)
+    - [CRC](#crc)
+    - [Annotation](#annotation)
+  - [Tracelog format](#tracelog-format)
+  - [Trace and Wireshark](#trace-and-wireshark)
 
 
 ## Trace command
diff --git a/doc/uart_notes.md b/doc/uart_notes.md
index 9e71c3dbf..5c8cb65b6 100644
--- a/doc/uart_notes.md
+++ b/doc/uart_notes.md
@@ -1,9 +1,29 @@
-# Various notes about UART and baudrates
+# Notes about UART and baudrates
+<a id="top"></a>
 
 Proxmark3 RDV4 can interact with the client program running on a host via several means, let's go through each of them and study the notion of baudrate.
 
+# Table of Contents
+- [Notes about UART and baudrates](#notes-about-uart-and-baudrates)
+- [Table of Contents](#table-of-contents)
+  - [USB-CDC ACM](#usb-cdc-acm)
+  - [Proxmark3 FPC USART](#proxmark3-fpc-usart)
+  - [Proxmark3 FPC USART + BT add-on (blue shark)](#proxmark3-fpc-usart--bt-add-on-blue-shark)
+    - [BT add-on AT configuration mode](#bt-add-on-at-configuration-mode)
+    - [BT add-on connected mode](#bt-add-on-connected-mode)
+    - [RFCOMM](#rfcomm)
+    - [BT add-on baudrate](#bt-add-on-baudrate)
+    - [BT on host side: internal BT](#bt-on-host-side-internal-bt)
+    - [BT on host side: HC-06 dongle](#bt-on-host-side-hc-06-dongle)
+    - [HC-06 dongle AT configuration mode](#hc-06-dongle-at-configuration-mode)
+  - [Proxmark3 FPC USART + FTDI](#proxmark3-fpc-usart--ftdi)
+  - [BT add-on + FTDI](#bt-add-on--ftdi)
+  - [HC-06 AT Commands](#hc-06-at-commands)
+
+
 
 ## USB-CDC ACM
+^[Top](#top)
 
 USB CDC (Communications Device Class) ACM (Abstract Control Model) is a way to emulate serial ports over USB.
 On the host, it appears as a virtual serial port, e.g. `/dev/ttyACM0`.
@@ -16,6 +36,7 @@ The real communication speed relates only to the USB link and is roughly about 7
 In USB CDC ACM the host could "set" other baudrates and the USB CDC device would be informed of the changes (see `SET_LINE_CODING` and `GET_LINE_CODING`), which is potentially interesting if, behind, it has to configure a real UART, but we're not in this type of setup so baudrate notion on USB CDC (visible e.g. with `stty -F /dev/ttyACM0`) can simply be totally ignored. 
 
 ## Proxmark3 FPC USART
+^[Top](#top)
 
 Proxmark3 RDV4 has a FPC connector outputting on 2 pins a USART from the ARM:
 
@@ -40,8 +61,10 @@ And for things to work fine, both sets have to match!
 Internally, the desired baudrate is converted to UART settings: a BRGR and a FP. The resulting baudrate will be close to but not always equal to the desired baudrate. Serial ports typically have some error tolerance in the actual baudrates. Theoretically < 2.5% on each side (so 5% in total), < 2% to be on the safe side. In the current firmware configuration, the Proxmark3 can provide any baudrate up to 2Mbauds with an error of max 2%, and selected baudrates up to 6Mbauds (tested with a FTDI C232HM DDHSL-0 cable).
 
 ## Proxmark3 FPC USART + BT add-on (blue shark)
+^[Top](#top)
 
 ### BT add-on AT configuration mode
+^[Top](#top)
 
 When the BT add-on is turned on but no actively connected to a host, it's in a configuration mode where it accepts "AT" commands and its blue LED is blinking at about 1Hz.
 
@@ -54,18 +77,21 @@ Some specific commands are available when you add `BTADDON` to `PLATFORM_EXTRAS`
 Manual configuration is also possible with `usart txrx -d "AT+Px"` and `usart txrx -d "AT+BAUDx"`.
 
 ### BT add-on connected mode
+^[Top](#top)
 
 When the BT add-on is paired with a host and the host establishes an active connection, the blue LED turns on steadily.
 
 The add-on acts as a bridge, between its UART and the BT communication channel, here a RFCOMM channel.
 
 ### RFCOMM
+^[Top](#top)
 
 The Bluetooth RFCOMM protocol provides an emulation of serial ports over the L2CAP protocol ([ref](https://www.amd.e-technik.uni-rostock.de/ma/gol/lectures/wirlec/bluetooth_info/rfcomm.html)).
 
 As for USB-CDC, the real speed of the link is unrelated to serial baudrate notion. Literature mentions a maximal value of 360kbps for some implementations, but the HC-06 Bluetooth module within the BT add-on is limited as the vast majority of similar devices to 128kbps.
 
 ### BT add-on baudrate
+^[Top](#top)
 
 Which baudrate will make sense?
 
@@ -78,6 +104,7 @@ Using 115200 is safe and within the 128kbps limit.
 Using 230400 allows to maximize the BT channel capacity, but the gain is limited, about 10-15% faster than 115200. There is also a risk to take into account: you're delivering data too fast to the HC-06 than what it's capable to send over RFCOMM. So you're filling the internal buffer faster than it can be emptied. If you're doing it for too many data, you'll reach a point where data will be lost (once the internal buffer is full), which is observable with `hw status` for higher baudrates at the time of writing (`b17da830edadb8462e02a95a00b4a58302cce71b`).
 
 ### BT on host side: internal BT
+^[Top](#top)
 
 On the other side of the BT link is the host. If it has built-in Bluetooth, the host can present a virtual serial port to the Proxmark3 client.
 E.g. on Linux, `rfcomm` allows to create such bindings of a BT device and a virtual port: `rfcomm bind rfcomm0 20:19:04:20:04:08` will create the virtual port `/dev/rfcomm0`.
@@ -89,6 +116,7 @@ Note that the rfcomm implementation separates pairing from actual connection:
 * when the program closes, the link will be closed and the BT add-on blue LED will blink again, showing that the BT ad-on is in AT configuration mode.
 
 ### BT on host side: HC-06 dongle
+^[Top](#top)
 
 On the other side of the BT link is the host. If it does not have a built-in Bluetooth, the host can use
 * a generic BT add-on, and we're back in the previous case
@@ -111,12 +139,14 @@ The USB-UART speed is selected when a program opens the port. E.g.
 * the Python script meant to configure HC-06 dongle. The baudrate is given as option to `serial.Serial()` when opening `/dev/ttyUSB0`.
 
 ### HC-06 dongle AT configuration mode
+^[Top](#top)
 
 When the BT add-on is turned off, the HC-06 dongle will not be connected and will fall back into its AT configuration mode (its LED is blinking).
 
 `tools/btaddon/hc06_factory.py` changes several times the USB-UART baudrate and parity till it matches the HC-06 dongle settings, then changes the baudrate and parity of the dongle to a default value. (`hc06_factory.py` does more but we're only interested in baudrate in this discussion)
 
 ## Proxmark3 FPC USART + FTDI
+^[Top](#top)
 
 Just for completeness, let's mention the possibility to connect the Proxmark3 to the host via FPC and a USB-UART bridge such as a FTDI cable (or CP2102, ch341, PL2303...).
 It's not very interesting because USB-CDC is faster anyway.
@@ -124,6 +154,7 @@ It's not very interesting because USB-CDC is faster anyway.
 In that case, Proxmark3 USART settings (configured e.g. via `usart config` while connected by USB-CDC) have to match FTDI cable settings, selected by the program opening the serial port (`/dev/ttyUSB0`).
 
 ## BT add-on + FTDI
+^[Top](#top)
 
 Just for completeness, let's mention the possibility to connect the BT add-on to the host via FPC and a USB-UART bridge such as a FTDI cable (or CP2102, ch341, PL2303...).
 The only interest is for debug purposes, e.g. to reconfigure an add-on from the host rather than from the Proxmark3 itself.
@@ -148,6 +179,7 @@ Turn BTpower switch ON, leave Battery switch OFF
 Use e.g. `tools/btaddon/hc06_factory.py` with `role = b'S'`
 
 ## HC-06 AT Commands
+^[Top](#top)
 
 The HC-06 supports a subset of the AT commands and they are listed below. The commands are limited and vary in how to give parameters. A pure informative command like AT+VERSION just returns a string with current firmware the HC-06 has installed.
 
