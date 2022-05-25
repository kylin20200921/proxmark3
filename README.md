commit 36086d12dcdc24c1ba763dfa61c14b615889a803
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Dec 31 10:58:25 2021 +0100

    text

diff --git a/doc/fpga_arm_notes.md b/doc/fpga_arm_notes.md
index 37aa6a911..4271f5790 100644
--- a/doc/fpga_arm_notes.md
+++ b/doc/fpga_arm_notes.md
@@ -1,4 +1,25 @@
-# Notes on ARM & FPGA comms
+# Notes on ARM & FPGA communications
+<a id="top"></a>
+
+# Table of Contents
+- [Notes on ARM & FPGA communications](#notes-on-arm--fpga-communications)
+- [Table of Contents](#table-of-contents)
+- [INTERFACE FROM THE ARM TO THE FPGA](#interface-from-the-arm-to-the-fpga)
+  - [FPGA](#fpga)
+    - [FPGA modes](#fpga-modes)
+  - [ARM FPGA communications](#arm-fpga-communications)
+  - [ARM GPIO setup](#arm-gpio-setup)
+  - [FPGA Setup](#fpga-setup)
+- [HARDWARE OVERVIEW](#hardware-overview)
+  - [ADC (ANALOG TO DIGITAL CONVERTER)](#adc-analog-to-digital-converter)
+  - [FIELD PROGRAMMABLE GATE ARRAY, FPGA](#field-programmable-gate-array-fpga)
+  - [MICROCONTROLLER](#microcontroller)
+  - [](#)
+  - [To behave like a READER](#to-behave-like-a-reader)
+  - [To behave like a TAG](#to-behave-like-a-tag)
+  - [To sniff traffic](#to-sniff-traffic)
+  - [FPGA purpose](#fpga-purpose)
+
 
 
 https://github.com/RfidResearchGroup/proxmark3/blob/master/doc/original_proxmark3/proxmark3.pdf
@@ -34,6 +55,8 @@ LF analog path (MCP6294 opamp. This has a GBW of 10 MHz),  all 'slow' signals.
 
 
 ## FPGA
+^[Top](#top)
+
 Since the SPARTAN II is a old outdated FPGA, thus is very limited resource there was a need to split LF and HF functionality into two separate FPGA images.  Which are stored in ARM flash memory as bitstreams.
 
 We swap between these images by flashing fpga from ARM on the go.  It takes about 1sec.   Hence its usually a bad idea to program your device to continuously execute LF alt HF commands.
@@ -50,19 +73,22 @@ In order to save space,  these fpga images are LZ4 compressed and included in th
 This means we save some precious space on the ARM but its a bit more complex when flashing to fpga since it has to decompress on the fly.  
 
 
-### FPGA modes.
+### FPGA modes
+^[Top](#top)
+
  - Major modes
  - Minor modes
 
-## ARM FPGA communications.
+## ARM FPGA communications
+^[Top](#top)
 
 The ARM talks with FPGA over the Synchronous Serial Port (SSC)  rx an tx.
 
 ARM, send a 16bit configuration with fits the select major mode.
- 
 
 
 ## ARM GPIO setup
+^[Top](#top)
 
 ```
     // First configure the GPIOs, and get ourselves a clock.
@@ -100,6 +126,8 @@ ARM, send a 16bit configuration with fits the select major mode.
 ```
 
 ## FPGA Setup
+^[Top](#top)
+
 
 // Set up DMA to receive samples from the FPGA. We will use the PDC, with
 // a single buffer as a circular buffer (so that we just chain back to
@@ -107,13 +135,19 @@ ARM, send a 16bit configuration with fits the select major mode.
 
 
 # HARDWARE OVERVIEW
+^[Top](#top)
+
 
 ## ADC (ANALOG TO DIGITAL CONVERTER)
+^[Top](#top)
+
 The analogue signal that comes from the antenna circuit is fed into an 8-bit Analogue to Digital Converter
 (ADC). This delivers 8 output bits in parallel which represent the current voltage retrieved from the field.
 
 
 ## FIELD PROGRAMMABLE GATE ARRAY, FPGA
+^[Top](#top)
+
 The 8 output pins from the ADC are connected to 8 pins of the Field Programmable Gate Array (FPGA). An
 FPGA has a great advantage over a normal microcontroller in the sense that it emulates hardware. A
 hardware description can be compiled and flashed into an FPGA.
@@ -141,6 +175,8 @@ FPGA generates an electromagnetic field on power hi and drops the amplitude for
 
 
 ## MICROCONTROLLER
+^[Top](#top)
+
 The microcontroller is responsible for the protocol management. It receives the digital encoded signals
 from the FPGA and decodes them. The decoded signals can just be copied to a buffer in the EEPROM
 memory. Additionally, an answer to the received message can be send by encoding a reply and
@@ -185,13 +221,17 @@ Problems:
 
 ## 
 
-## To behave like a READER.
+## To behave like a READER
+^[Top](#top)
+
 By driving all of the buffers LOW, it is possible to make the antenna
 look to the receive path like a parallel LC circuit; this provides a
 high-voltage output signal. This is typically what will be done when we
 are not actively transmitting a carrier (i.e., behaving as a reader).
 
 ## To behave like a TAG
+^[Top](#top)
+
 On the receive side, there are two possibilities, which are selected by
 RLY1. A mechanical relay is used, because the signal from the antenna is
 likely to be more positive or negative than the highest or lowest supply
@@ -222,10 +262,13 @@ is the master) or its generic synchronous serial port (again, the ARM
 is the master). The ARM connects to the outside world over USB.
 
 ## To sniff traffic
+^[Top](#top)
 
 
 
 ##  FPGA purpose
+^[Top](#top)
+
 Digital signal processing.
 In short,  apply low pass / hi pass filtering,   peak detect,  correlate signal meaning IQ pair collecting.
 
diff --git a/doc/jooki_notes.md b/doc/jooki_notes.md
index 5217ea3e8..aa950fdfa 100644
--- a/doc/jooki_notes.md
+++ b/doc/jooki_notes.md
@@ -1,15 +1,35 @@
 # Jooki Figurine Notes
+<a id="top"></a>
+
+# Table of Contents
+- [Jooki Figurine Notes](#jooki-figurine-notes)
+- [Table of Contents](#table-of-contents)
+  - [Jooki proxmark commands](#jooki-proxmark-commands)
+    - [Decoding NDEF URL parameter](#decoding-ndef-url-parameter)
+    - [Encoding NDEF record](#encoding-ndef-record)
+    - [Simulation](#simulation)
+    - [Cloning to a NTAG213 tag](#cloning-to-a-ntag213-tag)
+    - [List of known figurine types](#list-of-known-figurine-types)
+
+
+
 - NTAG213 (Should be tested if other NTAG2xx work)
 - A single NDEF record of type URL
 - Physical figurines are Fox, Dragon, Knight, Ghost, Whale, Generic Flat. Than there are variations of those figures with different colors.
 
 ## Jooki proxmark commands
+^[Top](#top)
+
 You can `encode`, `decode` a NDEF record, write with `clone` a record to a card or simulate with`sim`.
 
 ### Decoding NDEF URL parameter
+^[Top](#top)
+
 `hf jooki decode -d g+t07s57aX1bB6tk`
 
 ### Encoding NDEF record
+^[Top](#top)
+
 You can either use figurine abbreviation arguments:
 ```
     --dragon 
@@ -49,6 +69,8 @@ Output:
 Use `-r` parameter to read UID directly from tag.
 
 ### Simulation
+^[Top](#top)
+
 To simulate the above figurine use the encoded URL parameter given in `encode` output and type following command into your proxmark:
  
 `hf jooki sim -b g+t07s57aX1bB6tk`
@@ -56,6 +78,8 @@ To simulate the above figurine use the encoded URL parameter given in `encode` o
 If no parameter is given to the simulation command, last loaded dump is used.
 
 ### Cloning to a NTAG213 tag
+^[Top](#top)
+
 ```
     hf jooki clone [-h] [-b <base64>] [-d <hex>] [-p <hex>]
 
@@ -81,6 +105,8 @@ or use the base64 encoded parameter to clone:
 Note: Jooki doesn't like more than one NDEF record, so make sure you just have one. Check with `hf mfu ndefread`
 
 ### List of known figurine types
+^[Top](#top)
+
 `Value`|`Figurine Type`|
 |------|---------------|
 **01** | Stones |
