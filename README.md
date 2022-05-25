commit 7c7dfa7d392fde020cebd2380eb3961f62f6a794
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 8 15:05:03 2022 +0100

    cppcheck fix

diff --git a/tools/fpga_compress/fpga_compress.c b/tools/fpga_compress/fpga_compress.c
index 1726117fd..04e0dbc9f 100644
--- a/tools/fpga_compress/fpga_compress.c
+++ b/tools/fpga_compress/fpga_compress.c
@@ -250,9 +250,8 @@ static int zlib_decompress(FILE *infile, FILE *outfiles[], uint8_t num_outfiles,
             fclose(outfiles[j]);
         }
     }
-    if (outbufall) {
-        free(outbufall);
-    }
+
+    free(outbufall);
     return (EXIT_SUCCESS);
 }
 
