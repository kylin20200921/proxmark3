commit a2f6d0f65f44bc13d901a571e577f2162ab32cf9
Author: iceman1001 <iceman@iuse.se>
Date:   Tue May 10 07:35:57 2022 +0200

    swap to other fpga image, since iso15693.c uses it. Should fix the simulation

diff --git a/armsrc/Standalone/hf_iceclass.c b/armsrc/Standalone/hf_iceclass.c
index f900e9e12..8f9bf4c63 100644
--- a/armsrc/Standalone/hf_iceclass.c
+++ b/armsrc/Standalone/hf_iceclass.c
@@ -633,7 +633,7 @@ void RunMod(void) {
         mode = bb[0];
     }
 
-    FpgaDownloadAndGo(FPGA_BITSTREAM_HF);
+    FpgaDownloadAndGo(FPGA_BITSTREAM_HF_15);
     BigBuf_Clear_ext(false);
 
     StandAloneMode();
