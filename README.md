commit 70709ca0ed82af6c0c737343452438b155de3da8
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jun 20 11:13:49 2021 +0200

    fix coverity CID 349306 - resource leak

diff --git a/tools/fpga_compress/fpga_compress.c b/tools/fpga_compress/fpga_compress.c
index 005f805c4..1726117fd 100644
--- a/tools/fpga_compress/fpga_compress.c
+++ b/tools/fpga_compress/fpga_compress.c
@@ -179,6 +179,9 @@ static int zlib_decompress(FILE *infile, FILE *outfiles[], uint8_t num_outfiles,
                 fclose(outfiles[j]);
             }
         }
+        if (outbufall) {
+            free(outbufall);
+        }
         free(inbuf);
         return (EXIT_FAILURE);
     }
@@ -247,6 +250,9 @@ static int zlib_decompress(FILE *infile, FILE *outfiles[], uint8_t num_outfiles,
             fclose(outfiles[j]);
         }
     }
+    if (outbufall) {
+        free(outbufall);
+    }
     return (EXIT_SUCCESS);
 }
 
