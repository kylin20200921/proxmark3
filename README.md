commit 436fedcbe41601cfdba1a2e29202f323af92b2b4
Author: iceman1001 <iceman@iuse.se>
Date:   Thu May 20 10:11:41 2021 +0200

    fix coverity CID 344485, 344482, 344481

diff --git a/tools/fpga_compress/fpga_compress.c b/tools/fpga_compress/fpga_compress.c
index f5f5fde49..cfbd30cbb 100644
--- a/tools/fpga_compress/fpga_compress.c
+++ b/tools/fpga_compress/fpga_compress.c
@@ -40,9 +40,13 @@ static bool all_feof(FILE *infile[], uint8_t num_infiles) {
 }
 
 static int zlib_compress(FILE *infile[], uint8_t num_infiles, FILE *outfile) {
-    uint8_t *fpga_config;
 
-    fpga_config = calloc(num_infiles * FPGA_CONFIG_SIZE, sizeof(uint8_t));
+    uint8_t *fpga_config = calloc(num_infiles * FPGA_CONFIG_SIZE, sizeof(uint8_t));
+    if (fpga_config == NULL) {
+        fprintf(stderr, "failed to allocate memory");
+        return (EXIT_FAILURE);
+    }
+
     // read the input files. Interleave them into fpga_config[]
     uint32_t total_size = 0;
     do {
@@ -99,6 +103,9 @@ static int zlib_compress(FILE *infile[], uint8_t num_infiles, FILE *outfile) {
         int cmp_bytes = LZ4_compress_HC_continue(lz4_streamhc, ring_buffer, outbuf, bytes_to_copy, outsize_max);
         if (cmp_bytes < 0 ){
             fprintf(stderr, "(lz4 - zlib_compress) error,  got negative number of bytes from LZ4_compress_HC_continue call. got %d ", cmp_bytes);
+            free(ring_buffer);
+            free(outbuf);
+            free(fpga_config);
             return (EXIT_FAILURE);
         }
         fwrite(&cmp_bytes, sizeof(int), 1, outfile);
