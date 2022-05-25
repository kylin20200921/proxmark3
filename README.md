commit 30468731e143896fc2817165b6cb1fe75672c47f
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jan 23 01:37:30 2022 +0100

    restore amiibo detection

diff --git a/client/src/cmdhfmfu.c b/client/src/cmdhfmfu.c
index 012cce720..7dcb4e957 100644
--- a/client/src/cmdhfmfu.c
+++ b/client/src/cmdhfmfu.c
@@ -1157,6 +1157,12 @@ static mfu_identify_t mfu_ident_table[] = {
         NULL, NULL,
         "hf mfu dump -k %08x"
     },
+    {
+        "Amiibo", "0004040201001103",
+        9, 9, "480FE0F110FFEEA500",
+        ul_ev1_pwdgenB, ul_ev1_packgenB,
+        "hf mfu dump -k %08x"
+    },
     /*
     {
         "Xiaomi AIR Purifier", "0004040201000F03",
