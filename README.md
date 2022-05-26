commit 2dd250ab805f54424a223b18c9dd69e10b46de5a
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Apr 10 13:15:24 2022 +0200

    fix initialization of bitstream buffer

diff --git a/armsrc/lfsampling.c b/armsrc/lfsampling.c
index ee9f8d772..a1ff37356 100644
--- a/armsrc/lfsampling.c
+++ b/armsrc/lfsampling.c
@@ -173,7 +173,11 @@ void initSampleBufferEx(uint32_t *sample_size, bool use_malloc) {
         data.buffer = BigBuf_get_addr();
     }
 
-    //
+    // reset data stream
+    data.numbits = 0;
+    data.position = 0;
+
+    // reset samples
     samples.dec_counter = 0;
     samples.sum = 0;
     samples.counter = *sample_size;
