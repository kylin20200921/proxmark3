commit cd764ac832ed10ea1cebf1e676e2f97798b1cba5
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Dec 31 10:13:22 2021 +0100

    text

diff --git a/doc/clocks.md b/doc/clocks.md
index fa7ba4f42..161d680fb 100644
--- a/doc/clocks.md
+++ b/doc/clocks.md
@@ -1,3 +1,5 @@
+<a id="Top"></a>
+
 # Notes on device side clocks
 The device side firmware uses a range of different clocks.  Here is an attempt to document the clocks in use and for what they are used.
 
@@ -17,24 +19,28 @@ The device side firmware uses a range of different clocks.  Here is an attempt t
   - [Occasional TC0+TC1 / Ticks functions](#occasional-tc0tc1--ticks-functions)
 
 ## Slow clock
+^[Top](#top)
 
 ~32kHz internal RC clock
 
 Can be between 22 and 42 kHz
 
 ## Main Oscillator / MAINCK
+^[Top](#top)
 
 cf `PMC_MOR` register
 
 16 MHz, based on external Xtal
 
 ## PLL clock
+^[Top](#top)
 
 cf `PMC_PLLR` register
 
 96 MHz (MAINCK * 12 / 2)
 
 ## Master Clock MCK, Processor Clock PCK, USB clock UDPCK
+^[Top](#top)
 
 cf `common_arm/clocks.c`
 
@@ -51,6 +57,7 @@ USB need to be clocked at 48 MHz from the PLL, so PLL / 2 (cf `CKGR_PLLR`).
 
 
 ## Peripheral clocks
+^[Top](#top)
 
 cf `bootrom.c`:
 
@@ -61,6 +68,7 @@ cf `appmain.c`
 Activate PCK0 pin as clock output, based on PLL / 4 = 24 MHz, for the FPGA.
 
 ## 1 kHz RTC: TickCount functions
+^[Top](#top)
 
 cf `armsrc/ticks.c`
 
@@ -91,6 +99,7 @@ Current usages:
 * USB connection speed measure
 
 ## Occasional PWM timer
+^[Top](#top)
 
 * `void SpinDelayUs(int us)`
 * `void SpinDelay(int ms)` based on SpinDelayUs
@@ -102,6 +111,7 @@ Busy wait based on 46.875 kHz PWM Channel 0
 * *Precision* variant: 0.7 us precision and maximum 43 ms
 
 ## Occasional TC0+TC1 / CountUS functions
+^[Top](#top)
 
 cf `armsrc/ticks.c`
 
@@ -118,6 +128,7 @@ Maximal value: 0x7fffffff = 2147 s
 Can't be used at the same time as CountSspClk or Ticks functions.
 
 ## Occasional TC0+TC1+TC2 SSP_CLK from FPGA / CountSspClk functions
+^[Top](#top)
 
 cf `armsrc/ticks.c`
 
@@ -139,6 +150,7 @@ Usage:
 Can't be used at the same time as CountUS or Ticks functions.
 
 ## Occasional TC0+TC1 / Ticks functions
+^[Top](#top)
 
 cf `armsrc/ticks.c`
 
