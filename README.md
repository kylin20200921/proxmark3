commit 26603ad80a93223167d36117f366ef17028075a5
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Apr 2 15:44:55 2022 +0200

    text

diff --git a/client/src/cmdhfntag424.c b/client/src/cmdhfntag424.c
index af475b1be..e41516ca1 100644
--- a/client/src/cmdhfntag424.c
+++ b/client/src/cmdhfntag424.c
@@ -68,7 +68,7 @@ static int CmdHF_ntag424_view(const char *Cmd) {
                 PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
                 return PM3_EMALLOC;
             }
-            res = loadFileJSON(filename, (void *)dump, MFU_MAX_BYTES + MFU_DUMP_PREFIX_LENGTH, &bytes_read, NULL);
+            res = loadFileJSON(filename, (void *)dump, NTAG424_MAX_BYTES, &bytes_read, NULL);
             break;
         }
         case EML:
@@ -96,7 +96,9 @@ static int CmdHF_ntag424_view(const char *Cmd) {
     return PM3_SUCCESS;
 }
 
-
+//
+// Original from  https://github.com/rfidhacking/node-sdm/
+//
 typedef struct sdm_picc_s {
     uint8_t tag;
     uint8_t uid[7];
