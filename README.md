commit a7f69b5f1300dcb0fd7ffa9683fb35fb3ba8ab91
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Feb 27 16:32:42 2022 +0100

    style

diff --git a/client/src/cmdhfcipurse.c b/client/src/cmdhfcipurse.c
index a1c1c3eb9..83dc300db 100644
--- a/client/src/cmdhfcipurse.c
+++ b/client/src/cmdhfcipurse.c
@@ -1407,10 +1407,10 @@ static int CmdHFCipurseDeleteFile(const char *Cmd) {
         res = CIPURSEDeleteFile(childFileId, buf, sizeof(buf), &len, &sw);
         if (res != 0 || sw != 0x9000) {
             PrintAndLogEx(ERR, "Delete child file " _CYAN_("%04x ") " %s", childFileId, _RED_("ERROR"));
-            PrintAndLogEx(ERR, "0x%04x - %s", 
-                    sw,
-                    GetSpecificAPDUCodeDesc(DeleteAPDUCodeDescriptions, ARRAYLEN(DeleteAPDUCodeDescriptions), sw)
-                );
+            PrintAndLogEx(ERR, "0x%04x - %s",
+                          sw,
+                          GetSpecificAPDUCodeDesc(DeleteAPDUCodeDescriptions, ARRAYLEN(DeleteAPDUCodeDescriptions), sw)
+                         );
             DropField();
             return PM3_ESOFT;
         }
@@ -1420,9 +1420,9 @@ static int CmdHFCipurseDeleteFile(const char *Cmd) {
         if (res != 0 || sw != 0x9000) {
             PrintAndLogEx(ERR, "Delete file " _CYAN_("%04x ") " %s", fileId, _RED_("ERROR"));
             PrintAndLogEx(ERR, "0x%04x - %s",
-                    sw,
-                    GetSpecificAPDUCodeDesc(DeleteAPDUCodeDescriptions, ARRAYLEN(DeleteAPDUCodeDescriptions), sw)
-                );
+                          sw,
+                          GetSpecificAPDUCodeDesc(DeleteAPDUCodeDescriptions, ARRAYLEN(DeleteAPDUCodeDescriptions), sw)
+                         );
             DropField();
             return PM3_ESOFT;
         }
@@ -1432,9 +1432,9 @@ static int CmdHFCipurseDeleteFile(const char *Cmd) {
         if (res != 0 || sw != 0x9000) {
             PrintAndLogEx(ERR, "Delete application " _CYAN_("%s ") " %s", sprint_hex_inrow(aid, aidLen), _RED_("ERROR"));
             PrintAndLogEx(ERR, "0x%04x - %s",
-                    sw,
-                    GetSpecificAPDUCodeDesc(DeleteAPDUCodeDescriptions, ARRAYLEN(DeleteAPDUCodeDescriptions), sw)
-                );
+                          sw,
+                          GetSpecificAPDUCodeDesc(DeleteAPDUCodeDescriptions, ARRAYLEN(DeleteAPDUCodeDescriptions), sw)
+                         );
             DropField();
             return PM3_ESOFT;
         }
diff --git a/client/src/cmdhfmfdes.c b/client/src/cmdhfmfdes.c
index 0a194c5c0..5bb289432 100644
--- a/client/src/cmdhfmfdes.c
+++ b/client/src/cmdhfmfdes.c
@@ -2185,30 +2185,30 @@ static int CmdHF14ADesSetConfiguration(const char *Cmd) {
     if (verbose) {
         if (DesfireMFSelected(selectway, id)) {
             PrintAndLogEx(INFO, _CYAN_("PICC") " param ID: 0x%02x param[%d]: %s",
-                    paramid,
-                    paramlen,
-                    sprint_hex(param, paramlen)
-                );
+                          paramid,
+                          paramlen,
+                          sprint_hex(param, paramlen)
+                         );
         } else {
-            PrintAndLogEx(INFO, _CYAN_("%s %06x") " param ID: 0x%02x param[%d]: %s", 
-                    DesfireSelectWayToStr(selectway),
-                    id,
-                    paramid,
-                    paramlen,
-                    sprint_hex(param, paramlen)
-                );
+            PrintAndLogEx(INFO, _CYAN_("%s %06x") " param ID: 0x%02x param[%d]: %s",
+                          DesfireSelectWayToStr(selectway),
+                          id,
+                          paramid,
+                          paramlen,
+                          sprint_hex(param, paramlen)
+                         );
         }
     }
 
     res = DesfireSelectAndAuthenticateAppW(&dctx, securechann, selectway, id, false, verbose);
     if (res != PM3_SUCCESS) {
         DropField();
-        PrintAndLogEx(FAILED, "Select or authentication ( %s ) Result [%d] %s %s", 
-                DesfireWayIDStr(selectway, id),
-                res,
-                DesfireAuthErrorToStr(res),
-                _RED_("failed")
-            );
+        PrintAndLogEx(FAILED, "Select or authentication ( %s ) Result [%d] %s %s",
+                      DesfireWayIDStr(selectway, id),
+                      res,
+                      DesfireAuthErrorToStr(res),
+                      _RED_("failed")
+                     );
         return res;
     }
 
diff --git a/client/src/flash.c b/client/src/flash.c
index 373f3b785..ad627ffb6 100644
--- a/client/src/flash.c
+++ b/client/src/flash.c
@@ -258,7 +258,7 @@ int flash_load(flash_file_t *ctx, bool force) {
     Elf32_Ehdr_t *ehdr;
     Elf32_Shdr_t *shdrs = NULL;
     uint8_t *shstr = NULL;
-     struct version_information_t *vi = NULL;
+    struct version_information_t *vi = NULL;
     int res = PM3_EUNDEF;
 
     fd = fopen(ctx->filename, "rb");
@@ -346,7 +346,7 @@ int flash_load(flash_file_t *ctx, bool force) {
         }
 
         if (strcmp(((char *)shstr) + shdrs[i].sh_name, ".bootphase1") == 0) {
-            uint32_t offset = *(uint32_t*)(ctx->elf + le32(shdrs[i].sh_offset) + le32(shdrs[i].sh_size) - 4);
+            uint32_t offset = *(uint32_t *)(ctx->elf + le32(shdrs[i].sh_offset) + le32(shdrs[i].sh_size) - 4);
             if (offset >= le32(shdrs[i].sh_addr)) {
                 offset -= le32(shdrs[i].sh_addr);
                 if (offset < le32(shdrs[i].sh_size)) {
diff --git a/doc/commands.json b/doc/commands.json
index 6fa197fd8..1faa02809 100644
--- a/doc/commands.json
+++ b/doc/commands.json
@@ -10910,6 +10910,6 @@
     "metadata": {
         "commands_extracted": 688,
         "extracted_by": "PM3Help2JSON v1.00",
-        "extracted_on": "2022-02-24T18:33:33"
+        "extracted_on": "2022-02-27T15:27:46"
     }
 }
\ No newline at end of file
