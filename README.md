commit 501a848034b8d0289dc9b1ec64c288b4d9bd5b2f
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jun 20 08:05:21 2021 +0200

    fix coverity CID 349311

diff --git a/client/src/cmdhfemrtd.c b/client/src/cmdhfemrtd.c
index 6f635fdb0..fb56aa4f6 100644
--- a/client/src/cmdhfemrtd.c
+++ b/client/src/cmdhfemrtd.c
@@ -843,9 +843,13 @@ static bool emrtd_dump_file(uint8_t *ks_enc, uint8_t *ks_mac, uint8_t *ssc, uint
     strncat(filepath, PATHSEP, 2);
     strcat(filepath, name);
 
-    PrintAndLogEx(INFO, "Read %s, len: %i.", name, resplen);
-    PrintAndLogEx(DEBUG, "Contents (may be incomplete over 2k chars): %s", sprint_hex_inrow(response, resplen));
+    PrintAndLogEx(INFO, "Read " _YELLOW_("%s") " , len %zu", name, resplen);
+    PrintAndLogEx(DEBUG, "Contents (may be incomplete over 2k chars)");
+    PrintAndLogEx(DEBUG, "------------------------------------------");
+    PrintAndLogEx(DEBUG, "%s", sprint_hex_inrow(response, resplen));
+    PrintAndLogEx(DEBUG, "------------------------------------------");
     saveFile(filepath, ".BIN", response, resplen);
+
     emrtd_dg_t *dg = emrtd_fileid_to_dg(file);
     if ((dg != NULL) && (dg->dumper != NULL)) {
         dg->dumper(response, resplen, path);
