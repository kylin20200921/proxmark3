commit 7573205a935cbe84e573b4cee3664bb344e3eddf
Author: iceman1001 <iceman@iuse.se>
Date:   Tue May 10 07:34:17 2022 +0200

    fix iclass simulation

diff --git a/armsrc/appmain.c b/armsrc/appmain.c
index e329b2e23..e0dabebd0 100644
--- a/armsrc/appmain.c
+++ b/armsrc/appmain.c
@@ -1712,8 +1712,12 @@ static void PacketReceived(PacketCommandNG *packet) {
             break;
         }
         case CMD_HF_ICLASS_EML_MEMSET: {
-            //iceman, should call FPGADOWNLOAD before, since it corrupts BigBuf
-            FpgaDownloadAndGo(FPGA_BITSTREAM_HF);
+            //-----------------------------------------------------------------------------
+            // Note: we call FpgaDownloadAndGo(FPGA_BITSTREAM_HF_15) here although FPGA is not
+            // involved in dealing with emulator memory. But if it is called later, it might
+            // destroy the Emulator Memory.
+            //-----------------------------------------------------------------------------
+            FpgaDownloadAndGo(FPGA_BITSTREAM_HF_15);
             struct p {
                 uint16_t offset;
                 uint16_t len;
