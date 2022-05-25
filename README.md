commit 228668e7a3a99c791420b7cb252b0866f0d7be79
Author: iceman1001 <iceman@iuse.se>
Date:   Sat May 15 15:20:10 2021 +0200

    mark as unsigned

diff --git a/include/proxmark3_arm.h b/include/proxmark3_arm.h
index de3ae9caa..0cee0f80d 100644
--- a/include/proxmark3_arm.h
+++ b/include/proxmark3_arm.h
@@ -41,12 +41,12 @@
 #define SSC_CLOCK_MODE_SELECT(x)                ((x) << 0)
 #define SSC_FRAME_MODE_BITS_IN_WORD(x)          (((x)-1) << 0)
 
-#define MC_FLASH_COMMAND_KEY                    ((0x5a) << 24)
+#define MC_FLASH_COMMAND_KEY                    ((0x5A) << 24)
 #define MC_FLASH_MODE_FLASH_WAIT_STATES(x)      ((x) << 8)
 #define MC_FLASH_MODE_MASTER_CLK_IN_MHZ(x)      (((x)+((x)/2)) << 16)
 #define MC_FLASH_COMMAND_PAGEN(x)               ((x) << 8)
 
-#define RST_CONTROL_KEY                         (0xa5 << 24)
+#define RST_CONTROL_KEY                         (0xA5U << 24)
 
 #define PMC_MAIN_OSC_STARTUP_DELAY(x)           ((x) << 8)
 #define PMC_PLL_DIVISOR(x)                      (x)
