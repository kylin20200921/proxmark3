commit 16c43bea2deef0449537b83be731ae17337db1aa
Author: iceman1001 <iceman@iuse.se>
Date:   Wed May 19 10:13:37 2021 +0200

    fix coverity CID 322671, 322668,  and time now is zero padded

diff --git a/tools/fpga_compress/fpga_compress.c b/tools/fpga_compress/fpga_compress.c
index 859a2b858..f5f5fde49 100644
--- a/tools/fpga_compress/fpga_compress.c
+++ b/tools/fpga_compress/fpga_compress.c
@@ -97,7 +97,10 @@ static int zlib_compress(FILE *infile[], uint8_t num_infiles, FILE *outfile) {
 
         memcpy(ring_buffer, fpga_config + current_in, bytes_to_copy);
         int cmp_bytes = LZ4_compress_HC_continue(lz4_streamhc, ring_buffer, outbuf, bytes_to_copy, outsize_max);
-
+        if (cmp_bytes < 0 ){
+            fprintf(stderr, "(lz4 - zlib_compress) error,  got negative number of bytes from LZ4_compress_HC_continue call. got %d ", cmp_bytes);
+            return (EXIT_FAILURE);
+        }
         fwrite(&cmp_bytes, sizeof(int), 1, outfile);
         fwrite(outbuf, sizeof(char), cmp_bytes, outfile);
 
@@ -118,7 +121,7 @@ static int zlib_compress(FILE *infile[], uint8_t num_infiles, FILE *outfile) {
     fprintf(stdout, "compressed %u input bytes to %d output bytes\n", total_size, current_out);
 
     if (current_out == 0) {
-        fprintf(stderr, "Error in lz4");
+        fprintf(stderr, "error in lz4");
         return (EXIT_FAILURE);
     }
     return (EXIT_SUCCESS);
@@ -204,18 +207,27 @@ static int bitparse_find_section(FILE *infile, char section_name, unsigned int *
             break;
         }
         unsigned int current_length = 0;
+        int tmp;
         switch (current_name) {
             case 'e':
                 /* Four byte length field */
-                current_length += fgetc(infile) << 24;
-                current_length += fgetc(infile) << 16;
-                current_length += fgetc(infile) << 8;
-                current_length += fgetc(infile) << 0;
+                for (int i = 0; i < 4; i++) {
+                    tmp = fgetc(infile);
+                    if (tmp < 0 ) {
+                        break;
+                    }
+                    current_length += tmp << (24 - (i * 8));
+                }
                 numbytes += 4;
                 break;
             default: /* Fall through, two byte length field */
-                current_length += fgetc(infile) << 8;
-                current_length += fgetc(infile) << 0;
+                for (int i = 0; i < 2; i++) {
+                    tmp = fgetc(infile);
+                    if (tmp < 0 ) {
+                        break;
+                    }
+                    current_length += tmp << (8 - (i * 8));
+                }
                 numbytes += 2;
                 break;
         }
@@ -290,6 +302,7 @@ static int FpgaGatherVersion(FILE *infile, char *infile_name, char *dst, int len
         for (uint16_t i = 0; i < fpga_info_len; i++) {
             char c = (char)fgetc(infile);
             if (i < sizeof(tempstr)) {
+                if (c == ' ') c = '0';
                 tempstr[i] = c;
             }
         }
