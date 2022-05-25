commit 49ee82be194d49826854a56091b1c9b12382225f
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Dec 24 15:24:32 2021 +0100

    zx now builds after fixing corrent header.

diff --git a/armsrc/appmain.c b/armsrc/appmain.c
index 71254684b..59597d497 100644
--- a/armsrc/appmain.c
+++ b/armsrc/appmain.c
@@ -48,7 +48,7 @@
 #include "ticks.h"
 #include "commonutil.h"
 #include "crc16.h"
-#include "zx8211.h"
+#include "lfzx.h"
 
 
 #ifdef WITH_LCD
diff --git a/armsrc/lfzx.c b/armsrc/lfzx.c
index 823903407..ac22807ab 100644
--- a/armsrc/lfzx.c
+++ b/armsrc/lfzx.c
@@ -11,14 +11,61 @@
 #define __LFOPS_H
 
 #include "lfzx.h"
+#include "fpgaloader.h"
+#include "ticks.h"
+#include "dbprint.h"
+#include "lfadc.h"
 #include "pm3_cmd.h" // struct
 #include "zx8211.h"
 
+
+static void zx8211_setup_read(void) {
+
+    FpgaDownloadAndGo(FPGA_BITSTREAM_LF);
+    FpgaWriteConfWord(FPGA_MAJOR_MODE_LF_ADC | FPGA_LF_ADC_READER_FIELD);
+
+    // 50ms for the resonant antenna to settle.
+    SpinDelay(50);
+
+    // Now set up the SSC to get the ADC samples that are now streaming at us.
+    FpgaSetupSsc(FPGA_MAJOR_MODE_LF_READER);
+
+    FpgaSendCommand(FPGA_CMD_SET_DIVISOR, LF_DIVISOR_125);
+
+    // Connect the A/D to the peak-detected low-frequency path.
+    SetAdcMuxFor(GPIO_MUXSEL_LOPKD);
+
+    // Steal this pin from the SSP (SPI communication channel with fpga) and
+    // use it to control the modulation
+    AT91C_BASE_PIOA->PIO_PER = GPIO_SSC_DOUT;
+    AT91C_BASE_PIOA->PIO_OER = GPIO_SSC_DOUT;
+
+    // Disable modulation at default, which means enable the field
+    LOW(GPIO_SSC_DOUT);
+
+    // Start the timer
+    StartTicks();
+
+    // Watchdog hit
+    WDT_HIT();
+}
+
+
 int zx8211_read(zx8211_data_t *zxd, bool ledcontrol) {
+    zx8211_setup_read();
+
+    StopTicks();
+    lf_finalize(ledcontrol);
+    //reply_ng(CMD_LF_ZX_READ, status, tag.data, sizeof(tag.data));
     return PM3_SUCCESS;
 }
 
 int zx8211_write(zx8211_data_t *zxd, bool ledcontrol) {
+    zx8211_setup_read();
+
+    StopTicks();
+    lf_finalize(ledcontrol);
+    //reply_ng(CMD_LF_ZX_WRITE, status, tag.data, sizeof(tag.data));
     return PM3_SUCCESS;
 }
 
diff --git a/armsrc/lfzx.h b/armsrc/lfzx.h
index 0dbfec491..6b80b1c8a 100644
--- a/armsrc/lfzx.h
+++ b/armsrc/lfzx.h
@@ -11,7 +11,6 @@
 #define __LFZX_H
 
 #include "common.h"
-#include "pm3_cmd.h" // struct
 #include "zx8211.h"
 
 int zx8211_read(zx8211_data_t *zxd, bool ledcontrol);
diff --git a/common_arm/Makefile.hal b/common_arm/Makefile.hal
index 15dd78686..e3d219348 100644
--- a/common_arm/Makefile.hal
+++ b/common_arm/Makefile.hal
@@ -64,6 +64,7 @@ SKIP_FELICA=1
 SKIP_NFCBARCODE=1
 SKIP_HFSNIFF=1
 SKIP_HFPLOT=1
+SKIP_ZX8211=1
 endef
 
 define KNOWN_DEFINITIONS
diff --git a/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md b/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md
index 458b8ea67..920974c51 100644
--- a/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md
+++ b/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md
@@ -139,7 +139,7 @@ a series of `SKIP_*` allow to skip some of the functionalities and to get a smal
 |SKIP_NFCBARCODE=1    | 1.4kb
 |SKIP_HFSNIFF=1       | 0.5kb
 |SKIP_HFPLOT=1        | 0.3kb
-
+|SKIP_ZX8211=1        | unknown yet
 
 So for example, at the time of writing, this is a valid `Makefile.platform` compiling an image for 256k:
 ```
