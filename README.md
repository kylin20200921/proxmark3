commit d5205e5a3401d6a6f5df47f0d230afacfb23e1be
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Mar 28 19:30:55 2022 +0200

    fix #1640 the btaddon compilation leaves the bigbuff malloc around 38kb.  By lessen the fpga ring buffer to be 30kb instead leaves some room for a smaller bigbuff

diff --git a/common_fpga/fpga.h b/common_fpga/fpga.h
index 293a7963a..b7e017d68 100644
--- a/common_fpga/fpga.h
+++ b/common_fpga/fpga.h
@@ -27,7 +27,7 @@
 #else
 #define FPGA_CONFIG_SIZE                    42336L  // our current fpga_[lh]f.bit files are 42175 bytes. Rounded up to next multiple of FPGA_INTERLEAVE_SIZE
 #endif
-#define FPGA_RING_BUFFER_BYTES              (1024 * 39)
+#define FPGA_RING_BUFFER_BYTES              (1024 * 30)
 #define FPGA_TRACE_SIZE                     3072
 
 static const uint8_t bitparse_fixed_header[] = {0x00, 0x09, 0x0f, 0xf0, 0x0f, 0xf0, 0x0f, 0xf0, 0x0f, 0xf0, 0x00, 0x00, 0x01};
