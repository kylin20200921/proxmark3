commit f63bc96980679716dabe23d36d8234334573f3da
Author: iceman1001 <iceman@iuse.se>
Date:   Thu May 20 11:34:56 2021 +0200

    seems cppchecker cant make up its mind

diff --git a/client/src/cmdhficlass.c b/client/src/cmdhficlass.c
index dc4d16a33..08c4e9360 100644
--- a/client/src/cmdhficlass.c
+++ b/client/src/cmdhficlass.c
@@ -301,7 +301,7 @@ static int generate_config_card(const iclass_config_card_item_t *o,  uint8_t *ke
             uint8_t *p = realloc(data, tot_bytes);
             if (p == NULL) {
                 PrintAndLogEx(FAILED, "failed to allocate memory");
-                //free(data);
+                free(data);
                 return PM3_EMALLOC;
             }
             data = p;
