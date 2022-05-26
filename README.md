commit f38a3ad762128ac46cb150ac58015cdf2ac2b929
Author: iceman1001 <iceman@iuse.se>
Date:   Sun May 15 23:56:35 2022 +0200

    adapt load dump file refactoring else where

diff --git a/client/src/cmddata.c b/client/src/cmddata.c
index 02cbcb914..180106851 100644
--- a/client/src/cmddata.c
+++ b/client/src/cmddata.c
@@ -2957,77 +2957,25 @@ static int CmdDiff(const char *Cmd) {
         width = 16;
     }
 
+    // if user supplied dump file,  time to load it
     int res = PM3_SUCCESS;
     uint8_t *inA = NULL, *inB = NULL;
     size_t datalenA = 0, datalenB = 0;
     // read file A
     if (fnlenA) {
-
-        // if user supplied dump file,  time to load it
-        DumpFileType_t dftype = getfiletype(filenameA);
-        switch (dftype) {
-            case BIN: {
-                res = loadFile_safe(filenameA, ".bin", (void **)&inA, &datalenA);
-                break;
-            }
-            case EML: {
-                res = loadFileEML_safe(filenameA, (void **)&inA, &datalenA);
-                break;
-            }
-            case JSON: {
-                inA =  calloc(2048, sizeof(uint8_t));
-                if (inA == NULL) {
-                    PrintAndLogEx(ERR, "error, cannot allocate memory ");
-                    return PM3_EMALLOC;
-                }
-                res = loadFileJSON(filenameA, (void *)inA, 2048, &datalenA, NULL);
-                break;
-            }
-            case DICTIONARY: {
-                free(inA);
-                PrintAndLogEx(ERR, "Error: Only BIN/JSON/EML formats allowed");
-                return PM3_EINVARG;
-            }
-        }
-
+        // read dump file
+        res = pm3_load_dump(filenameA, (void **)&inA, &datalenA, 2048);
         if (res != PM3_SUCCESS) {
-            free(inA);
-            return PM3_EFILE;
+            return res;
         }
     }
 
     // read file B
     if (fnlenB) {
-        // if user supplied dump file,  time to load it
-        DumpFileType_t dftype = getfiletype(filenameB);
-        switch (dftype) {
-            case BIN: {
-                res = loadFile_safe(filenameB, ".bin", (void **)&inB, &datalenB);
-                break;
-            }
-            case EML: {
-                res = loadFileEML_safe(filenameB, (void **)&inB, &datalenB);
-                break;
-            }
-            case JSON: {
-                inB = calloc(2048, sizeof(uint8_t));
-                if (inB == NULL) {
-                    PrintAndLogEx(ERR, "error, cannot allocate memory ");
-                    return PM3_EMALLOC;
-                }
-                res = loadFileJSON(filenameB, (void *)inB, 2048, &datalenB, NULL);
-                break;
-            }
-            case DICTIONARY: {
-                free(inB);
-                PrintAndLogEx(ERR, "Error: Only BIN/JSON/EML formats allowed");
-                return PM3_EINVARG;
-            }
-        }
-
+        // read dump file
+        res = pm3_load_dump(filenameB, (void **)&inB, &datalenB, 2048);
         if (res != PM3_SUCCESS) {
-            free(inB);
-            return PM3_EFILE;
+            return res;
         }
     }
 
diff --git a/client/src/cmdhficlass.c b/client/src/cmdhficlass.c
index 849c16c76..cf17a2505 100644
--- a/client/src/cmdhficlass.c
+++ b/client/src/cmdhficlass.c
@@ -1000,38 +1000,12 @@ static int CmdHFiClassELoad(const char *Cmd) {
 
     CLIParserFree(ctx);
 
-    size_t bytes_read = 2048;
+    // read dump file
     uint8_t *dump = NULL;
-    int res = 0;
-    DumpFileType_t dftype = getfiletype(filename);
-    switch (dftype) {
-        case BIN: {
-            res = loadFile_safe(filename, ".bin", (void **)&dump, &bytes_read);
-            break;
-        }
-        case EML: {
-            res = loadFileEML_safe(filename, (void **)&dump, &bytes_read);
-            break;
-        }
-        case JSON: {
-            dump = calloc(2048, sizeof(uint8_t));
-            if (dump == NULL) {
-                PrintAndLogEx(ERR, "error, cannot allocate memory ");
-                return PM3_EMALLOC;
-            }
-            res = loadFileJSON(filename, (void *)dump, 2048, &bytes_read, NULL);
-            break;
-        }
-        case DICTIONARY: {
-            free(dump);
-            PrintAndLogEx(ERR, "Error: Only BIN/JSON/EML formats allowed");
-            return PM3_EINVARG;
-        }
-    }
-
+    size_t bytes_read = 2048;
+    int res = pm3_load_dump(filename, (void **)&dump, &bytes_read, 2048);
     if (res != PM3_SUCCESS) {
-        free(dump);
-        return PM3_EFILE;
+        return res;
     }
 
     uint8_t *newdump = realloc(dump, bytes_read);
@@ -1243,35 +1217,10 @@ static int CmdHFiClassDecrypt(const char *Cmd) {
     // if user supplied dump file,  time to load it
     if (fnlen > 0) {
 
-        DumpFileType_t dftype = getfiletype(filename);
-        switch (dftype) {
-            case BIN: {
-                res = loadFile_safe(filename, ".bin", (void **)&decrypted, &decryptedlen);
-                break;
-            }
-            case EML: {
-                res = loadFileEML_safe(filename, (void **)&decrypted, &decryptedlen);
-                break;
-            }
-            case JSON: {
-                decrypted =  calloc(2048, sizeof(uint8_t));
-                if (decrypted == NULL) {
-                    PrintAndLogEx(ERR, "error, cannot allocate memory ");
-                    return PM3_EMALLOC;
-                }
-                res = loadFileJSON(filename, (void *)decrypted, 2048, &decryptedlen, NULL);
-                break;
-            }
-            case DICTIONARY: {
-                free(decrypted);
-                PrintAndLogEx(ERR, "Error: Only BIN/JSON/EML formats allowed");
-                return PM3_EINVARG;
-            }
-        }
-
+        // read dump file
+        res = pm3_load_dump(filename, (void **)&decrypted, &decryptedlen, 2048);
         if (res != PM3_SUCCESS) {
-            free(decrypted);
-            return PM3_EFILE;
+            return res;
         }
 
         have_file = true;
@@ -2157,38 +2106,12 @@ static int CmdHFiClassRestore(const char *Cmd) {
         return PM3_EINVARG;
     }
 
-    size_t bytes_read = 2048;
+    // read dump file
     uint8_t *dump = NULL;
-    int res = PM3_SUCCESS;
-    DumpFileType_t dftype = getfiletype(filename);
-    switch (dftype) {
-        case BIN: {
-            res = loadFile_safe(filename, ".bin", (void **)&dump, &bytes_read);
-            break;
-        }
-        case EML: {
-            res = loadFileEML_safe(filename, (void **)&dump, &bytes_read);
-            break;
-        }
-        case JSON: {
-            dump =  calloc(2048, sizeof(uint8_t));
-            if (dump == NULL) {
-                PrintAndLogEx(ERR, "error, cannot allocate memory ");
-                return PM3_EMALLOC;
-            }
-            res = loadFileJSON(filename, (void *)dump, 2048, &bytes_read, NULL);
-            break;
-        }
-        case DICTIONARY: {
-            free(dump);
-            PrintAndLogEx(ERR, "Error: Only BIN/JSON/EML formats allowed");
-            return PM3_EINVARG;
-        }
-    }
-
+    size_t bytes_read = 2048;
+    int res = pm3_load_dump(filename, (void **)&dump, &bytes_read, 2048);
     if (res != PM3_SUCCESS) {
-        free(dump);
-        return PM3_EFILE;
+        return res;
     }
 
     if (bytes_read == 0) {
@@ -2693,38 +2616,12 @@ static int CmdHFiClassView(const char *Cmd) {
 
     CLIParserFree(ctx);
 
-    size_t bytes_read = 2048;
+    // read dump file
     uint8_t *dump = NULL;
-    int res = 0;
-    DumpFileType_t dftype = getfiletype(filename);
-    switch (dftype) {
-        case BIN: {
-            res = loadFile_safe(filename, ".bin", (void **)&dump, &bytes_read);
-            break;
-        }
-        case EML: {
-            res = loadFileEML_safe(filename, (void **)&dump, &bytes_read);
-            break;
-        }
-        case JSON: {
-            dump = calloc(2048, sizeof(uint8_t));
-            if (dump == NULL) {
-                PrintAndLogEx(ERR, "error, cannot allocate memory ");
-                return PM3_EMALLOC;
-            }
-            res = loadFileJSON(filename, (void *)dump, 2048, &bytes_read, NULL);
-            break;
-        }
-        case DICTIONARY: {
-            free(dump);
-            PrintAndLogEx(ERR, "Error: Only BIN/JSON/EML formats allowed");
-            return PM3_EINVARG;
-        }
-    }
-
+    size_t bytes_read = 2048;
+    int res = pm3_load_dump(filename, (void **)&dump, &bytes_read, 2048);
     if (res != PM3_SUCCESS) {
-        free(dump);
-        return PM3_EFILE;
+        return res;
     }
 
     if (verbose) {
diff --git a/client/src/cmdhflegic.c b/client/src/cmdhflegic.c
index da3f34a06..973f35ed5 100644
--- a/client/src/cmdhflegic.c
+++ b/client/src/cmdhflegic.c
@@ -912,53 +912,26 @@ static int CmdLegicRestore(const char *Cmd) {
 
     legic_print_type(card.cardsize, 0);
 
-    // set up buffer
-    uint8_t *data = NULL;
+    // read dump file
+    uint8_t *dump = NULL;
     size_t bytes_read = 0;
-    int res = 0;
-    DumpFileType_t dftype = getfiletype(filename);
-    switch (dftype) {
-        case BIN: {
-            res = loadFile_safe(filename, ".bin", (void **)&data, &bytes_read);
-            break;
-        }
-        case EML: {
-            res = loadFileEML_safe(filename, (void **)&data, &bytes_read);
-            break;
-        }
-        case JSON: {
-            data = calloc(LEGIC_PRIME_MIM1024, sizeof(uint8_t));
-            if (data == NULL) {
-                PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
-                return PM3_EMALLOC;
-            }
-            res = loadFileJSON(filename, (void *)data, LEGIC_PRIME_MIM1024, &bytes_read, NULL);
-            break;
-        }
-        case DICTIONARY: {
-            PrintAndLogEx(ERR, "Error: Only BIN/JSON/EML formats allowed");
-            free(data);
-            return PM3_EINVARG;
-        }
-    }
-
+    int res = pm3_load_dump(filename, (void **)&dump, &bytes_read, LEGIC_PRIME_MIM1024);
     if (res != PM3_SUCCESS) {
-        free(data);
-        return PM3_EFILE;
+        return res;
     }
 
     // validation
     if (card.cardsize != bytes_read) {
         PrintAndLogEx(WARNING, "Fail, filesize and cardsize is not equal. [%u != %zu]", card.cardsize, bytes_read);
-        free(data);
+        free(dump);
         return PM3_EFILE;
     }
 
     if (shall_obsfuscate) {
-        if (legic_xor(data, card.cardsize) == false) {
+        if (legic_xor(dump, card.cardsize) == false) {
             PrintAndLogEx(FAILED, "Obsfuscate failed, exiting...");
             PrintAndLogEx(HINT, "Try running command without `--ob` parameter");
-            free(data);
+            free(dump);
             return PM3_EFAILED;
         }
     }
@@ -983,7 +956,7 @@ static int CmdLegicRestore(const char *Cmd) {
         payload->offset = i;
         payload->iv = 0x55;
         payload->len = len;
-        memcpy(payload->data, data + i, len);
+        memcpy(payload->data, dump + i, len);
 
         clearCommandBuffer();
         SendCommandNG(CMD_HF_LEGIC_WRITER, (uint8_t *)payload, sizeof(legic_packet_t) + len);
@@ -995,7 +968,7 @@ static int CmdLegicRestore(const char *Cmd) {
             PrintAndLogEx(NORMAL, "." NOLF);
             if (timeout > 10) {
                 PrintAndLogEx(WARNING, "\ncommand execution time out");
-                free(data);
+                free(dump);
                 return PM3_ETIMEOUT;
             }
         }
@@ -1003,13 +976,13 @@ static int CmdLegicRestore(const char *Cmd) {
 
         if (resp.status != PM3_SUCCESS) {
             PrintAndLogEx(WARNING, "Failed writing tag");
-            free(data);
+            free(dump);
             return PM3_ERFTRANS;
         }
         PrintAndLogEx(SUCCESS, "Wrote chunk [offset %zu | len %zu | total %zu", i, len, i + len);
     }
 
-    free(data);
+    free(dump);
     PrintAndLogEx(SUCCESS, "Done!");
     return PM3_SUCCESS;
 }
@@ -1037,39 +1010,12 @@ static int CmdLegicELoad(const char *Cmd) {
     bool shall_obsfuscate = arg_get_lit(ctx, 2);
     CLIParserFree(ctx);
 
-    // set up buffer
-    uint8_t *data = NULL;
+    // read dump file
+    uint8_t *dump = NULL;
     size_t bytes_read = 0;
-    int res = 0;
-    DumpFileType_t dftype = getfiletype(filename);
-    switch (dftype) {
-        case BIN: {
-            res = loadFile_safe(filename, ".bin", (void **)&data, &bytes_read);
-            break;
-        }
-        case EML: {
-            res = loadFileEML_safe(filename, (void **)&data, &bytes_read);
-            break;
-        }
-        case JSON: {
-            data = calloc(LEGIC_PRIME_MIM1024, sizeof(uint8_t));
-            if (data == NULL) {
-                PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
-                return PM3_EMALLOC;
-            }
-            res = loadFileJSON(filename, (void *)data, LEGIC_PRIME_MIM1024, &bytes_read, NULL);
-            break;
-        }
-        case DICTIONARY: {
-            PrintAndLogEx(ERR, "Error: Only BIN/JSON/EML formats allowed");
-            free(data);
-            return PM3_EINVARG;
-        }
-    }
-
+    int res = pm3_load_dump(filename, (void **)&dump, &bytes_read, LEGIC_PRIME_MIM1024);
     if (res != PM3_SUCCESS) {
-        free(data);
-        return PM3_EFILE;
+        return res;
     }
 
     // validation
@@ -1077,18 +1023,18 @@ static int CmdLegicELoad(const char *Cmd) {
             bytes_read != LEGIC_PRIME_MIM256 &&
             bytes_read != LEGIC_PRIME_MIM1024) {
         PrintAndLogEx(ERR, "File content error. Read %zu bytes", bytes_read);
-        free(data);
+        free(dump);
         return PM3_EFILE;
     }
 
     if (shall_obsfuscate) {
-        legic_xor(data, bytes_read);
+        legic_xor(dump, bytes_read);
     }
 
     PrintAndLogEx(SUCCESS, "Uploading to emulator memory");
-    legic_seteml(data, 0, bytes_read);
+    legic_seteml(dump, 0, bytes_read);
 
-    free(data);
+    free(dump);
     PrintAndLogEx(SUCCESS, "Done!");
     return PM3_SUCCESS;
 }
@@ -1330,39 +1276,12 @@ static int CmdLegicView(const char *Cmd) {
     CLIParamStrToBuf(arg_get_str(ctx, 1), (uint8_t *)filename, FILE_PATH_SIZE, &fnlen);
     CLIParserFree(ctx);
 
-    // reserve memory
+    // read dump file
     uint8_t *dump = NULL;
     size_t bytes_read = 0;
-    int res = 0;
-    DumpFileType_t dftype = getfiletype(filename);
-    switch (dftype) {
-        case BIN: {
-            res = loadFile_safe(filename, ".bin", (void **)&dump, &bytes_read);
-            break;
-        }
-        case JSON: {
-            dump = calloc(LEGIC_PRIME_MIM1024, sizeof(uint8_t));
-            if (dump == NULL) {
-                PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
-                return PM3_EMALLOC;
-            }
-            res = loadFileJSON(filename, (void *)dump, LEGIC_PRIME_MIM1024, &bytes_read, NULL);
-            break;
-        }
-        case EML:
-            res = loadFileEML_safe(filename, (void **)&dump, &bytes_read);
-            break;
-        case DICTIONARY: {
-            PrintAndLogEx(ERR, "Error: Only BIN/EML/JSON formats allowed");
-            free(dump);
-            return PM3_EINVARG;
-        }
-    }
-
+    int res = pm3_load_dump(filename, (void **)&dump, &bytes_read, LEGIC_PRIME_MIM1024);
     if (res != PM3_SUCCESS) {
-        PrintAndLogEx(FAILED, "File: " _YELLOW_("%s") ": not found or locked.", filename);
-        free(dump);
-        return PM3_EFILE;
+        return res;
     }
 
     PrintAndLogEx(NORMAL, "");
diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index 71ec686ae..102289f8c 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -5345,7 +5345,7 @@ static int CmdHF14AMfMAD(const char *Cmd) {
 
     if (fnlen > 0) {
 
-        // reserve memory
+        // read dump file
         uint8_t *dump = NULL;
         size_t bytes_read = 0;
         int res = pm3_load_dump(filename, (void **)&dump, &bytes_read, (MFBLOCK_SIZE * MIFARE_4K_MAXBLOCK));
diff --git a/client/src/cmdhfmfu.c b/client/src/cmdhfmfu.c
index 306389f7e..202aa7444 100644
--- a/client/src/cmdhfmfu.c
+++ b/client/src/cmdhfmfu.c
@@ -2534,40 +2534,12 @@ static int CmdHF14AMfURestore(const char *Cmd) {
         free(fptr);
     }
 
-    // reserve memory
+    // read dump file
     uint8_t *dump = NULL;
     size_t bytes_read = 0;
-    int res = 0;
-    DumpFileType_t dftype = getfiletype(filename);
-    switch (dftype) {
-        case BIN: {
-            res = loadFile_safe(filename, ".bin", (void **)&dump, &bytes_read);
-            break;
-        }
-        case EML: {
-            res = loadFileEML_safe(filename, (void **)&dump, &bytes_read);
-            break;
-        }
-        case JSON: {
-            dump = calloc(MFU_MAX_BYTES + MFU_DUMP_PREFIX_LENGTH, sizeof(uint8_t));
-            if (dump == NULL) {
-                PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
-                return PM3_EMALLOC;
-            }
-            res = loadFileJSON(filename, (void *)dump, MFU_MAX_BYTES + MFU_DUMP_PREFIX_LENGTH, &bytes_read, NULL);
-            break;
-        }
-        case DICTIONARY: {
-            PrintAndLogEx(ERR, "Error: Only BIN/JSON formats allowed");
-            free(dump);
-            return PM3_EINVARG;
-        }
-    }
-
+    int res = pm3_load_dump(filename, (void **)&dump, &bytes_read, (MFU_MAX_BYTES + MFU_DUMP_PREFIX_LENGTH));
     if (res != PM3_SUCCESS) {
-        PrintAndLogEx(FAILED, "File: " _YELLOW_("%s") ": not found or locked.", filename);
-        free(dump);
-        return PM3_EFILE;
+        return res;
     }
 
     if (bytes_read < MFU_DUMP_PREFIX_LENGTH) {
@@ -4176,39 +4148,12 @@ static int CmdHF14AMfuView(const char *Cmd) {
     bool verbose = arg_get_lit(ctx, 2);
     CLIParserFree(ctx);
 
-    // reserve memory
+    // read dump file
     uint8_t *dump = NULL;
     size_t bytes_read = 0;
-    int res = 0;
-    DumpFileType_t dftype = getfiletype(filename);
-    switch (dftype) {
-        case BIN: {
-            res = loadFile_safe(filename, ".bin", (void **)&dump, &bytes_read);
-            break;
-        }
-        case JSON: {
-            dump = calloc(MFU_MAX_BYTES + MFU_DUMP_PREFIX_LENGTH, sizeof(uint8_t));
-            if (dump == NULL) {
-                PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
-                return PM3_EMALLOC;
-            }
-            res = loadFileJSON(filename, (void *)dump, MFU_MAX_BYTES + MFU_DUMP_PREFIX_LENGTH, &bytes_read, NULL);
-            break;
-        }
-        case EML:
-            res = loadFileEML_safe(filename, (void **)&dump, &bytes_read);
-            break;
-        case DICTIONARY: {
-            PrintAndLogEx(ERR, "Error: Only BIN/EML/JSON formats allowed");
-            free(dump);
-            return PM3_EINVARG;
-        }
-    }
-
+    int res = pm3_load_dump(filename, (void **)&dump, &bytes_read, (MFU_MAX_BYTES + MFU_DUMP_PREFIX_LENGTH));
     if (res != PM3_SUCCESS) {
-        PrintAndLogEx(FAILED, "File: " _YELLOW_("%s") ": not found or locked.", filename);
-        free(dump);
-        return PM3_EFILE;
+        return res;
     }
 
     if (bytes_read < MFU_DUMP_PREFIX_LENGTH) {
diff --git a/client/src/cmdhfntag424.c b/client/src/cmdhfntag424.c
index dfe118e02..49913c703 100644
--- a/client/src/cmdhfntag424.c
+++ b/client/src/cmdhfntag424.c
@@ -52,39 +52,12 @@ static int CmdHF_ntag424_view(const char *Cmd) {
     bool verbose = arg_get_lit(ctx, 2);
     CLIParserFree(ctx);
 
-    // reserve memory
+    // read dump file
     uint8_t *dump = NULL;
-    size_t bytes_read = 0;
-    int res = 0;
-    DumpFileType_t dftype = getfiletype(filename);
-    switch (dftype) {
-        case BIN: {
-            res = loadFile_safe(filename, ".bin", (void **)&dump, &bytes_read);
-            break;
-        }
-        case JSON: {
-            dump = calloc(NTAG424_MAX_BYTES, sizeof(uint8_t));
-            if (dump == NULL) {
-                PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
-                return PM3_EMALLOC;
-            }
-            res = loadFileJSON(filename, (void *)dump, NTAG424_MAX_BYTES, &bytes_read, NULL);
-            break;
-        }
-        case EML:
-            res = loadFileEML_safe(filename, (void **)&dump, &bytes_read);
-            break;
-        case DICTIONARY: {
-            PrintAndLogEx(ERR, "Error: Only BIN/EML/JSON formats allowed");
-            free(dump);
-            return PM3_EINVARG;
-        }
-    }
-
+    size_t bytes_read = NTAG424_MAX_BYTES;
+    int res = pm3_load_dump(filename, (void **)&dump, &bytes_read, NTAG424_MAX_BYTES);
     if (res != PM3_SUCCESS) {
-        PrintAndLogEx(FAILED, "File: " _YELLOW_("%s") ": not found or locked.", filename);
-        free(dump);
-        return PM3_EFILE;
+        return res;
     }
 
     if (verbose) {
diff --git a/client/src/cmdlfem4x50.c b/client/src/cmdlfem4x50.c
index 3c0bee80b..e35d0f141 100644
--- a/client/src/cmdlfem4x50.c
+++ b/client/src/cmdlfem4x50.c
@@ -134,35 +134,15 @@ static void print_info_result(uint8_t *data, bool verbose) {
 
 static int em4x50_load_file(const char *filename, uint8_t *data, size_t data_len, size_t *bytes_read) {
 
+    // read dump file
     uint8_t *dump = NULL;
-    int res = PM3_SUCCESS;
-    DumpFileType_t dftype = getfiletype(filename);
-    switch (dftype) {
-        case BIN: {
-            res = loadFile_safe(filename, ".bin", (void **)&dump, bytes_read);
-            break;
-        }
-        case EML: {
-            res = loadFileEML_safe(filename, (void **)&dump, bytes_read);
-            break;
-        }
-        case JSON: {
-            dump =  calloc(DUMP_FILESIZE, sizeof(uint8_t));
-            if (dump == NULL) {
-                PrintAndLogEx(ERR, "error, cannot allocate memory ");
-                return PM3_EMALLOC;
-            }
-            res = loadFileJSON(filename, (void *)dump, DUMP_FILESIZE, bytes_read, NULL);
-            break;
-        }
-        case DICTIONARY: {
-            free(dump);
-            PrintAndLogEx(ERR, "Error: Only BIN/JSON/EML formats allowed");
-            return PM3_EINVARG;
-        }
+    *bytes_read = 0;
+    int res = pm3_load_dump(filename, (void **)&dump, bytes_read, DUMP_FILESIZE);
+    if (res != PM3_SUCCESS) {
+        return res;
     }
 
-    if ((res != PM3_SUCCESS) && (*bytes_read != DUMP_FILESIZE)) {
+    if (*bytes_read != DUMP_FILESIZE) {
         free(dump);
         return PM3_EFILE;
     }
diff --git a/client/src/cmdlfhitag.c b/client/src/cmdlfhitag.c
index 33a49a406..62348672f 100644
--- a/client/src/cmdlfhitag.c
+++ b/client/src/cmdlfhitag.c
@@ -248,45 +248,18 @@ static int CmdLFHitagEload(const char *Cmd) {
         return PM3_EINVARG;
     }
 
-    DumpFileType_t dftype = getfiletype(filename);
-    size_t dumplen = 0;
+    // read dump file
     uint8_t *dump = NULL;
-    int res = 0;
-    switch (dftype) {
-        case BIN: {
-            res = loadFile_safe(filename, ".bin", (void **)&dump, &dumplen);
-            break;
-        }
-        case EML: {
-            res = loadFileEML_safe(filename, (void **)&dump, &dumplen);
-            break;
-        }
-        case JSON: {
-            dumplen = 4 * 64;
-            dump = calloc(dumplen, sizeof(uint8_t));
-            if (dump == NULL) {
-                PrintAndLogEx(ERR, "error, cannot allocate memory");
-                return PM3_EMALLOC;
-            }
-            res = loadFileJSON(filename, (void *)dump, dumplen, &dumplen, NULL);
-            break;
-        }
-        case DICTIONARY: {
-            PrintAndLogEx(ERR, "error, only BIN/JSON/EML formats allowed");
-            return PM3_EINVARG;
-        }
-    }
-
+    size_t bytes_read = (4 * 64);
+    int res = pm3_load_dump(filename, (void **)&dump, &bytes_read, (4 * 64));
     if (res != PM3_SUCCESS) {
-        PrintAndLogEx(ERR, "error, something went wrong when loading file");
-        free(dump);
-        return PM3_EFILE;
+        return res;
     }
 
     // check dump len..
-    if (dumplen == 48 ||  dumplen == 4 * 64) {
+    if (bytes_read == 48 ||  bytes_read == 4 * 64) {
 
-        lf_hitag_t *payload =  calloc(1, sizeof(lf_hitag_t) + dumplen);
+        lf_hitag_t *payload =  calloc(1, sizeof(lf_hitag_t) + bytes_read);
 
         if (use_ht1)
             payload->type = 1;
@@ -297,14 +270,14 @@ static int CmdLFHitagEload(const char *Cmd) {
         if (use_htm)
             payload->type = 4;
 
-        payload->len = dumplen;
-        memcpy(payload->data, dump, dumplen);
+        payload->len = bytes_read;
+        memcpy(payload->data, dump, bytes_read);
 
         clearCommandBuffer();
-        SendCommandNG(CMD_LF_HITAG_ELOAD, (uint8_t *)payload, 3 + dumplen);
+        SendCommandNG(CMD_LF_HITAG_ELOAD, (uint8_t *)payload, 3 + bytes_read);
         free(payload);
     } else {
-        PrintAndLogEx(ERR, "error, wrong dump file size. got %zu", dumplen);
+        PrintAndLogEx(ERR, "error, wrong dump file size. got %zu", bytes_read);
     }
 
     free(dump);
diff --git a/client/src/cmdlft55xx.c b/client/src/cmdlft55xx.c
index 8ee888a04..560582a11 100644
--- a/client/src/cmdlft55xx.c
+++ b/client/src/cmdlft55xx.c
@@ -2359,43 +2359,17 @@ static int CmdT55xxRestore(const char *Cmd) {
         return PM3_EINVARG;
     }
 
-    size_t dlen = 0;
-    void *dump = NULL;
-    DumpFileType_t dftype = getfiletype(filename);
-    switch (dftype) {
-        case BIN: {
-            res = loadFile_safe(filename, ".bin", (void **)&dump, &dlen);
-            break;
-        }
-        case EML: {
-            res = loadFileEML_safe(filename, (void **)&dump, &dlen);
-            break;
-        }
-        case JSON: {
-            dump = calloc(T55x7_BLOCK_COUNT * 4, sizeof(uint8_t));
-            if (dump == NULL) {
-                PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
-                return PM3_EMALLOC;
-            }
-            res = loadFileJSON(filename, dump, T55x7_BLOCK_COUNT * 4, &dlen, NULL);
-            break;
-        }
-        case DICTIONARY: {
-            PrintAndLogEx(ERR, "Error: Only BIN/EML/JSON formats allowed");
-            free(dump);
-            return PM3_EINVARG;
-        }
-    }
-
-    //sanity checks of file processing
+    // read dump file
+    uint8_t *dump = NULL;
+    size_t bytes_read = 0;
+    res = pm3_load_dump(filename, (void **)&dump, &bytes_read, (T55x7_BLOCK_COUNT * 4));
     if (res != PM3_SUCCESS) {
-        free(dump);
         return res;
     }
 
-    if (dlen != T55x7_BLOCK_COUNT * 4) {
+    if (bytes_read != (T55x7_BLOCK_COUNT * 4)) {
         free(dump);
-        PrintAndLogEx(FAILED, "wrong length of dump file. Expected 48 bytes, got %zu", dlen);
+        PrintAndLogEx(FAILED, "wrong length of dump file. Expected 48 bytes, got %zu", bytes_read);
         return PM3_EFILE;
     }
 
@@ -2407,8 +2381,9 @@ static int CmdT55xxRestore(const char *Cmd) {
     char wcmd[100];
     char pwdopt [14] = {0}; // p XXXXXXXX
 
-    if (usepwd)
+    if (usepwd) {
         snprintf(pwdopt, sizeof(pwdopt), "-p %08X", password);
+    }
 
     uint32_t *data = (uint32_t *) dump;
     uint8_t idx;
@@ -2423,7 +2398,7 @@ static int CmdT55xxRestore(const char *Cmd) {
     //    write blocks 1..3 page 1
     //    update downlink mode (if needed) and write b 0
     downlink_mode = 0;
-    if ((((data[11] >> 28) & 0xf) == 6) || (((data[11] >> 28) & 0xf) == 9))
+    if ((((data[11] >> 28) & 0xF) == 6) || (((data[11] >> 28) & 0xF) == 9))
         downlink_mode = (data[11] >> 10) & 3;
 
     // write out blocks 1-7 page 0
diff --git a/client/src/cmdnfc.c b/client/src/cmdnfc.c
index a87ec5797..a3f6ffd05 100644
--- a/client/src/cmdnfc.c
+++ b/client/src/cmdnfc.c
@@ -103,39 +103,22 @@ static int CmdNfcDecode(const char *Cmd) {
     int res = PM3_SUCCESS;
     if (fnlen != 0) {
 
+        // read dump file
         uint8_t *dump = NULL;
         size_t bytes_read = 4096;
-        DumpFileType_t dftype = getfiletype(filename);
-        switch (dftype) {
-            case BIN: {
-                res = loadFile_safe(filename, ".bin", (void **)&dump, &bytes_read);
-                break;
-            }
-            case EML: {
-                res = loadFileEML_safe(filename, (void **)&dump, &bytes_read);
-                break;
-            }
-            case JSON:
-            case DICTIONARY: {
-                free(dump);
-                PrintAndLogEx(ERR, "Error: Only BIN/EML formats allowed");
-                return PM3_EINVARG;
-            }
-        }
-
+        res = pm3_load_dump(filename, (void **)&dump, &bytes_read, 4096);
         if (res != PM3_SUCCESS) {
-            PrintAndLogEx(ERR, "error, something went wrong when loading file");
-            free(dump);
-            return PM3_EFILE;
+            return res;
         }
 
-
         res = NDEFDecodeAndPrint(dump, bytes_read, verbose);
         if (res != PM3_SUCCESS) {
             PrintAndLogEx(INFO, "Trying to parse NDEF records w/o NDEF header");
             res = NDEFRecordsDecodeAndPrint(dump, bytes_read);
         }
+
         free(dump);
+
     } else {
         res = NDEFDecodeAndPrint(data, datalen, verbose);
         if (res != PM3_SUCCESS) {
diff --git a/client/src/fileutils.c b/client/src/fileutils.c
index 3b9eaa263..49ea6c312 100644
--- a/client/src/fileutils.c
+++ b/client/src/fileutils.c
@@ -1911,7 +1911,6 @@ int pm3_load_dump(const char *fn, void **pdump, size_t *dumplen, size_t maxdumpl
 
     if (res != PM3_SUCCESS) {
         PrintAndLogEx(FAILED, "File: " _YELLOW_("%s") ": not found or locked.", fn);
-        PrintAndLogEx(INFO, "%d", res);
         free(*pdump);
         return PM3_EFILE;
     }
