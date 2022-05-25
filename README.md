commit 24c835f02490174ccd495a5b6fa79a21cb527eda
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Dec 31 10:01:02 2021 +0100

    added ToC

diff --git a/doc/clocks.md b/doc/clocks.md
index c1d046f06..4dfe72e82 100644
--- a/doc/clocks.md
+++ b/doc/clocks.md
@@ -1,3 +1,18 @@
+# Notes on device side clocks
+The device side firmware uses a range of different clocks.  Here is an attempt to document the clocks in use and for what they are used.
+
+- [Notes on device side clocks](#notes-on-device-side-clocks)
+  - [Slow clock](#slow-clock)
+  - [Main Oscillator / MAINCK](#main-oscillator--mainck)
+  - [PLL clock](#pll-clock)
+  - [Master Clock MCK, Processor Clock PCK, USB clock UDPCK](#master-clock-mck-processor-clock-pck-usb-clock-udpck)
+  - [Peripheral clocks](#peripheral-clocks)
+  - [1 kHz RTC: TickCount functions](#1-khz-rtc-tickcount-functions)
+  - [Occasional PWM timer](#occasional-pwm-timer)
+  - [Occasional TC0+TC1 / CountUS functions](#occasional-tc0tc1--countus-functions)
+  - [Occasional TC0+TC1+TC2 SSP_CLK from FPGA / CountSspClk functions](#occasional-tc0tc1tc2-ssp_clk-from-fpga--countsspclk-functions)
+  - [Occasional TC0+TC1 / Ticks functions](#occasional-tc0tc1--ticks-functions)
+
 ## Slow clock
 
 ~32kHz internal RC clock
