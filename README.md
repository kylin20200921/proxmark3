commit 326d2cf5d34b9822e3cf7311ac7d9a9c737d3e06
Author: iceman1001 <iceman@iuse.se>
Date:   Sat May 22 07:36:14 2021 +0200

    resplen is a pointer and we want its value

diff --git a/client/src/cmdhfmfdes.c b/client/src/cmdhfmfdes.c
index 22242e67e..7b6d3e26c 100644
--- a/client/src/cmdhfmfdes.c
+++ b/client/src/cmdhfmfdes.c
@@ -1969,7 +1969,7 @@ static int handler_desfire_getvalue(mfdes_value_t *value, uint32_t *resplen, uin
         DropFieldDesfire();
         return res;
     }
-    size_t dlen = (size_t)resplen;
+    size_t dlen = (size_t)*resplen;
     p = mifare_cryto_postprocess_data(tag, value->value, &dlen, cs | CMAC_COMMAND | CMAC_VERIFY | MAC_VERIFY);
     (void)p;
     return res;
