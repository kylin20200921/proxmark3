commit 0811ad2468e86cbb6c1a95c5dc93bf4aa6e0d416
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Feb 12 09:58:46 2022 +0100

    loading header data for MFU json dump files

diff --git a/client/src/fileutils.c b/client/src/fileutils.c
index b92c9991a..e6f9a35db 100644
--- a/client/src/fileutils.c
+++ b/client/src/fileutils.c
@@ -1111,6 +1111,21 @@ int loadFileJSONex(const char *preferredName, void *data, size_t maxdatalen, siz
     }
 
     if (!strcmp(ctype, "mfu")) {
+
+        mfu_dump_t* mem = (mfu_dump_t *)udata;
+
+        JsonLoadBufAsHex(root, "$.Card.Version", mem->version, sizeof(mem->version), datalen);
+        JsonLoadBufAsHex(root, "$.Card.TBO_0", mem->tbo, sizeof(mem->tbo), datalen);
+        JsonLoadBufAsHex(root, "$.Card.TBO_1", mem->tbo1, sizeof(mem->tbo1), datalen);
+        JsonLoadBufAsHex(root, "$.Card.Signature", mem->signature, sizeof(mem->signature), datalen);
+        JsonLoadBufAsHex(root, "$.Card.Counter0", &mem->counter_tearing[0][0], 3, datalen);
+        JsonLoadBufAsHex(root, "$.Card.Tearing0", &mem->counter_tearing[0][3], 1, datalen);
+        JsonLoadBufAsHex(root, "$.Card.Counter1", &mem->counter_tearing[1][0], 3, datalen);
+        JsonLoadBufAsHex(root, "$.Card.Tearing1", &mem->counter_tearing[1][3], 1, datalen);
+        JsonLoadBufAsHex(root, "$.Card.Counter2", &mem->counter_tearing[2][0], 3, datalen);
+        JsonLoadBufAsHex(root, "$.Card.Tearing2", &mem->counter_tearing[2][3], 1, datalen);
+        *datalen = MFU_DUMP_PREFIX_LENGTH;        
+
         size_t sptr = 0;
         for (int i = 0; i < 256; i++) {
             if (sptr + 4 > maxdatalen) {
@@ -1122,14 +1137,15 @@ int loadFileJSONex(const char *preferredName, void *data, size_t maxdatalen, siz
             sprintf(blocks, "$.blocks.%d", i);
 
             size_t len = 0;
-            JsonLoadBufAsHex(root, blocks, &udata[sptr], 4, &len);
+            JsonLoadBufAsHex(root, blocks, &mem->data[sptr], 4, &len);
             if (!len)
                 break;
 
             sptr += len;
+            mem->pages++;
         }
 
-        *datalen = sptr;
+        *datalen += sptr;
     }
 
     if (!strcmp(ctype, "hitag")) {
