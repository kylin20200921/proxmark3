commit 3491207890bcbd31fff5c8afa194924d03cde928
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Dec 27 17:39:56 2021 +0100

    text

diff --git a/armsrc/iso15693.c b/armsrc/iso15693.c
index 27c87c871..5a03c19e3 100644
--- a/armsrc/iso15693.c
+++ b/armsrc/iso15693.c
@@ -1967,6 +1967,7 @@ void DirectTag15693Command(uint32_t datalen, uint32_t speed, uint32_t recv, uint
         }
     }
     // note: this prevents using hf 15 cmd with s option - which isn't implemented yet anyway
+    // also prevents hf 15 raw -k  keep_field on ...
     FpgaWriteConfWord(FPGA_MAJOR_MODE_OFF);
     LED_D_OFF();
 }
