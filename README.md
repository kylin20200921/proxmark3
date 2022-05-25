commit 4d95cc2fdba6ac52c10a3812fb91570283490339
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jun 20 11:15:48 2021 +0200

    fix coverity CID 349307

diff --git a/client/src/cmdhfemrtd.c b/client/src/cmdhfemrtd.c
index 45ea6887b..7791233dc 100644
--- a/client/src/cmdhfemrtd.c
+++ b/client/src/cmdhfemrtd.c
@@ -1034,8 +1034,8 @@ int dumpHF_EMRTD(char *documentnumber, char *dob, char *expiry, bool BAC_availab
 
     // Dump EF_CardAccess (if available)
     if (!emrtd_dump_file(ks_enc, ks_mac, ssc, dg_table[EF_CardAccess].fileid, dg_table[EF_CardAccess].filename, BAC, path)) {
-        PrintAndLogEx(INFO, "Couldn't dump EF_CardAccess, card does not support PACE.");
-        PrintAndLogEx(HINT, "This is expected behavior for cards without PACE, and isn't something to be worried about.");
+        PrintAndLogEx(INFO, "Couldn't dump EF_CardAccess, card does not support PACE");
+        PrintAndLogEx(HINT, "This is expected behavior for cards without PACE, and isn't something to be worried about");
     }
 
     // Authenticate with the eMRTD
@@ -1046,7 +1046,7 @@ int dumpHF_EMRTD(char *documentnumber, char *dob, char *expiry, bool BAC_availab
 
     // Select EF_COM
     if (!emrtd_select_and_read(response, &resplen, dg_table[EF_COM].fileid, ks_enc, ks_mac, ssc, BAC)) {
-        PrintAndLogEx(ERR, "Failed to read EF_COM.");
+        PrintAndLogEx(ERR, "Failed to read EF_COM");
         DropField();
         return PM3_ESOFT;
     }
@@ -1055,11 +1055,12 @@ int dumpHF_EMRTD(char *documentnumber, char *dob, char *expiry, bool BAC_availab
     char *filepath = calloc(strlen(path) + 100, sizeof(char));
     if (filepath == NULL)
         return PM3_EMALLOC;
+
     strcpy(filepath, path);
     strncat(filepath, PATHSEP, 2);
     strcat(filepath, dg_table[EF_COM].filename);
 
-    PrintAndLogEx(INFO, "Read EF_COM, len: %i.", resplen);
+    PrintAndLogEx(INFO, "Read EF_COM, len: %zu", resplen);
     PrintAndLogEx(DEBUG, "Contents (may be incomplete over 2k chars): %s", sprint_hex_inrow(response, resplen));
     saveFile(filepath, ".BIN", response, resplen);
 
@@ -1069,7 +1070,7 @@ int dumpHF_EMRTD(char *documentnumber, char *dob, char *expiry, bool BAC_availab
     size_t filelistlen = 0;
 
     if (emrtd_lds_get_data_by_tag(response, resplen, filelist, &filelistlen, 0x5c, 0x00, false, true, 0) == false) {
-        PrintAndLogEx(ERR, "Failed to read file list from EF_COM.");
+        PrintAndLogEx(ERR, "Failed to read file list from EF_COM");
         DropField();
         return PM3_ESOFT;
     }
