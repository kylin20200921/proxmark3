commit b98d518fd727ea220a062677ca378cd418d911ea
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Jan 6 19:41:45 2022 +0100

    style

diff --git a/armsrc/Standalone/hf_bog.c b/armsrc/Standalone/hf_bog.c
index 5fa6fa247..2f28c409c 100644
--- a/armsrc/Standalone/hf_bog.c
+++ b/armsrc/Standalone/hf_bog.c
@@ -113,7 +113,7 @@ static void RAMFUNC SniffAndStore(uint8_t param) {
     SpinDelay(50);
 
     // loop and listen
-    while (!BUTTON_PRESS()) {
+    while (BUTTON_PRESS() == false) {
         WDT_HIT();
         LED_A_ON();
 
diff --git a/armsrc/Standalone/hf_legic.c b/armsrc/Standalone/hf_legic.c
index 3f332a7e1..58d958c14 100644
--- a/armsrc/Standalone/hf_legic.c
+++ b/armsrc/Standalone/hf_legic.c
@@ -140,7 +140,7 @@ void RunMod(void) {
             // we just read 1024 bytes
             read_success = LegicRfReaderEx(0, 1024, 0x55);
 
-        } while (read_success == PM3_ESOFT && !BUTTON_PRESS());
+        } while ((read_success == PM3_ESOFT) && (BUTTON_PRESS() == false));
 
         LEDsoff();
 
diff --git a/armsrc/felica.c b/armsrc/felica.c
index 6e5f836f1..ace2f5f31 100644
--- a/armsrc/felica.c
+++ b/armsrc/felica.c
@@ -830,7 +830,7 @@ void felica_dump_lite_s(void) {
     uint16_t cnt = 0, cntfails = 0;
     uint8_t *dest = BigBuf_get_addr();
 
-    while (!BUTTON_PRESS() && !data_available()) {
+    while ((BUTTON_PRESS() == false) && (data_available() == false)) {
         WDT_HIT();
         // polling?
         //TransmitFor18092_AsReader(poll, 10, GetCountSspClk()+512, 1, 0);
diff --git a/armsrc/hitagS.c b/armsrc/hitagS.c
index 7545c4d96..71e4264ad 100644
--- a/armsrc/hitagS.c
+++ b/armsrc/hitagS.c
@@ -808,7 +808,7 @@ void SimulateHitagSTag(bool tag_mem_supplied, uint8_t *data, bool ledcontrol) {
     // synchronized startup procedure
     while (AT91C_BASE_TC0->TC_CV > 0); // wait until TC0 returned to zero
 
-    while (!BUTTON_PRESS() && !data_available()) {
+    while ((BUTTON_PRESS() == false) && (data_available() == false)) {
 
         WDT_HIT();
 
@@ -1279,7 +1279,7 @@ void ReadHitagS(hitag_function htf, hitag_data *htd, bool ledcontrol) {
         return;
     }
     int pageNum = 0;
-    while (!BUTTON_PRESS() && !data_available())  {
+    while ((BUTTON_PRESS() == false) && (data_available() == false)) {
 
         WDT_HIT();
 
@@ -1457,7 +1457,7 @@ void Hitag_check_challenges(uint8_t *data, uint32_t datalen, bool ledcontrol) {
     uint8_t tx[HITAG_FRAME_LEN];
     int t_wait = HITAG_T_WAIT_MAX;
 
-    while (BUTTON_PRESS() == false && data_available() == false) {
+    while ((BUTTON_PRESS() == false) && (data_available() == false)) {
         // Watchdog hit
         WDT_HIT();
 
diff --git a/armsrc/mifaresim.c b/armsrc/mifaresim.c
index a4691ccb8..1a98d1738 100644
--- a/armsrc/mifaresim.c
+++ b/armsrc/mifaresim.c
@@ -532,7 +532,7 @@ void Mifare1ksim(uint16_t flags, uint8_t exitAfterNReads, uint8_t *datain, uint1
     int counter = 0;
     bool finished = false;
     bool button_pushed = BUTTON_PRESS();
-    while (!button_pushed && !finished) {
+    while ((button_pushed == false) && (finished == false)) {
 
         WDT_HIT();
 
diff --git a/armsrc/mifaresniff_disabled.c b/armsrc/mifaresniff_disabled.c
index 75a5b9524..342bc878a 100644
--- a/armsrc/mifaresniff_disabled.c
+++ b/armsrc/mifaresniff_disabled.c
@@ -96,7 +96,7 @@ void RAMFUNC SniffMifare(uint8_t param) {
 
     uint32_t sniffCounter = 0;
     // loop and listen
-    while (!BUTTON_PRESS()) {
+    while (BUTTON_PRESS() == false) {
         WDT_HIT();
         LED_A_ON();
         /*
diff --git a/armsrc/util.c b/armsrc/util.c
index 831be2b4d..fe40b6b43 100644
--- a/armsrc/util.c
+++ b/armsrc/util.c
@@ -204,7 +204,7 @@ int BUTTON_CLICKED(int ms) {
     int ticks = ((MCK / 1000) * (ms ? ms : 1000)) >> 10;
 
     // If we're not even pressed, forget about it!
-    if (!BUTTON_PRESS())
+    if (BUTTON_PRESS() == false)
         return BUTTON_NO_CLICK;
 
     // Borrow a PWM unit for my real-time clock
@@ -223,7 +223,7 @@ int BUTTON_CLICKED(int ms) {
         // We haven't let off the button yet
         if (!letoff) {
             // We just let it off!
-            if (!BUTTON_PRESS()) {
+            if (BUTTON_PRESS() == false) {
                 letoff = 1;
 
                 // reset our timer for 500ms
@@ -268,7 +268,7 @@ int BUTTON_HELD(int ms) {
     int ticks = (48000 * (ms ? ms : 1000)) >> 10;
 
     // If we're not even pressed, forget about it!
-    if (!BUTTON_PRESS())
+    if (BUTTON_PRESS() == false)
         return BUTTON_NO_CLICK;
 
     // Borrow a PWM unit for my real-time clock
@@ -284,7 +284,7 @@ int BUTTON_HELD(int ms) {
         uint16_t now = AT91C_BASE_PWMC_CH0->PWMC_CCNTR;
 
         // As soon as our button let go, we didn't hold long enough
-        if (!BUTTON_PRESS())
+        if (BUTTON_PRESS() == false)
             return BUTTON_SINGLE_CLICK;
 
         // Have we waited the full second?
diff --git a/bootrom/bootrom.c b/bootrom/bootrom.c
index f59f66ef9..93c4d605e 100644
--- a/bootrom/bootrom.c
+++ b/bootrom/bootrom.c
@@ -232,7 +232,7 @@ static void flash_mode(void) {
             }
         }
 
-        if (g_common_area.flags.button_pressed && !BUTTON_PRESS()) {
+        if (g_common_area.flags.button_pressed && BUTTON_PRESS() == false) {
             g_common_area.flags.button_pressed = 0;
         }
         if (!g_common_area.flags.button_pressed && BUTTON_PRESS()) {
