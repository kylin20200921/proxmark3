commit ecf58de32bbccde74e6b99f664fb27871f25e853
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Feb 14 21:41:33 2022 +0100

    hf legic restore/eload - now acceps bin/eml/json dump files."

diff --git a/client/src/cmdhflegic.c b/client/src/cmdhflegic.c
index e791c4ecc..d0c6f80f6 100644
--- a/client/src/cmdhflegic.c
+++ b/client/src/cmdhflegic.c
@@ -30,7 +30,9 @@
 
 static int CmdHelp(const char *Cmd);
 
-#define LEGIC_PRIME_MAX_LENGTH 1024
+#define LEGIC_PRIME_MIM22   22
+#define LEGIC_PRIME_MIM256  256
+#define LEGIC_PRIME_MIM1024 1024
 
 static bool legic_xor(uint8_t *data, uint16_t cardsize) {
 
@@ -42,14 +44,14 @@ static bool legic_xor(uint8_t *data, uint16_t cardsize) {
     uint8_t crc = data[4];
     uint32_t calc_crc = CRC8Legic(data, 4);
     if (crc != calc_crc) {
-        PrintAndLogEx(INFO, "Crc mismatch, obsfuscation not possible");
+        PrintAndLogEx(INFO, "CRC mismatch, obsfuscation not possible");
         return false;
     }
 
     for (uint16_t i = 22; i < cardsize; i++) {
         data[i] ^= crc;
     }
-    PrintAndLogEx(SUCCESS, "applying xoring of data done!");
+    PrintAndLogEx(SUCCESS, "Applying xoring of data done!");
     return true;
 }
 
@@ -415,8 +417,8 @@ static int CmdLegicRdbl(const char *Cmd) {
     CLIParserFree(ctx);
 
     // sanity checks
-    if (len + offset >= LEGIC_PRIME_MAX_LENGTH) {
-        PrintAndLogEx(WARNING, "Out-of-bounds, Cardsize = %d, [offset+len = %d ]", LEGIC_PRIME_MAX_LENGTH, len + offset);
+    if (len + offset >= LEGIC_PRIME_MIM1024) {
+        PrintAndLogEx(WARNING, "Out-of-bounds, Cardsize = %d, [offset+len = %d ]", LEGIC_PRIME_MIM1024, len + offset);
         return PM3_EOUTOFBOUND;
     }
 
@@ -443,17 +445,31 @@ static int CmdLegicRdbl(const char *Cmd) {
 static int CmdLegicSim(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf legic sim",
-                  "Simulates a LEGIC Prime tag. MIM22, MIM256, MIM1024 types can be emulated",
-                  "hf legic sim -t 0   -> Simulate Type MIM22\n"
-                  "hf legic sim -t 1   -> Simulate Type MIM256 (default)\n"
-                  "hf legic sim -t 2   -> Simulate Type MIM1024");
+                  "Simulates a LEGIC Prime tag.\n"
+                  "Following types supported (MIM22, MIM256, MIM1024)",
+                  "hf legic sim --22\n"
+                  );
 
     void *argtable[] = {
         arg_param_begin,
-        arg_int0("t", "type", "<dec>", "Tag type to simulate."),
+        arg_lit0(NULL, "22", "LEGIC Prime MIM22"),
+        arg_lit0(NULL, "256", "LEGIC Prime MIM256 (def)"),
+        arg_lit0(NULL, "1024", "LEGIC Prime MIM1024"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
+    bool m1 = arg_get_lit(ctx, 1);
+    bool m2 = arg_get_lit(ctx, 2);
+    bool m3 = arg_get_lit(ctx, 3);
+    CLIParserFree(ctx);
+
+    // validations
+    if (m1 + m2 + m3 > 1) {
+        PrintAndLogEx(WARNING, "Only specify one LEGIC Prime Type");
+        return PM3_EINVARG;
+    } else if (m1 + m2 + m3 == 0) {
+        m2 = true;
+    }
 
     struct {
         uint8_t tagtype;
@@ -461,14 +477,12 @@ static int CmdLegicSim(const char *Cmd) {
     } PACKED payload;
 
     payload.send_reply = true;
-    payload.tagtype = arg_get_int_def(ctx, 1, 1);
-
-    CLIParserFree(ctx);
-
-    if (payload.tagtype > 2) {
-        PrintAndLogEx(ERR, "Invalid tag type selected.");
-        return PM3_EINVARG;
-    }
+    if (m1)
+        payload.tagtype = 0;
+    else if (m2)
+        payload.tagtype = 1;
+    else if (m3)
+        payload.tagtype = 2;
 
     clearCommandBuffer();
     SendCommandNG(CMD_HF_LEGIC_SIMULATE, (uint8_t *)&payload, sizeof(payload));
@@ -510,7 +524,7 @@ static int CmdLegicWrbl(const char *Cmd) {
     int offset = arg_get_int_def(ctx, 1, 0);
 
     int dlen = 0;
-    uint8_t data[LEGIC_PRIME_MAX_LENGTH] = {0};
+    uint8_t data[LEGIC_PRIME_MIM1024] = {0};
     CLIGetHexWithReturn(ctx, 2, data, &dlen);
 
     bool autoconfirm = arg_get_lit(ctx, 3);
@@ -573,7 +587,7 @@ static int CmdLegicWrbl(const char *Cmd) {
     while (WaitForResponseTimeout(CMD_HF_LEGIC_WRITER, &resp, 2000) == false) {
         ++timeout;
         PrintAndLogEx(NORMAL, "." NOLF);
-        if (timeout > 7) {
+        if (timeout > 10) {
             PrintAndLogEx(WARNING, "\ncommand execution time out");
             return PM3_ETIMEOUT;
         }
@@ -678,11 +692,11 @@ int legic_print_type(uint32_t tagtype, uint8_t spaces) {
     spc[10] = 0x00;
     char *spacer = spc + (10 - spaces);
 
-    if (tagtype == 22)
+    if (tagtype == LEGIC_PRIME_MIM22)
         PrintAndLogEx(SUCCESS, "%sTYPE: " _YELLOW_("MIM%d card (outdated)"), spacer, tagtype);
-    else if (tagtype == 256)
+    else if (tagtype == LEGIC_PRIME_MIM256)
         PrintAndLogEx(SUCCESS, "%sTYPE: " _YELLOW_("MIM%d card (234 bytes)"), spacer, tagtype);
-    else if (tagtype == 1024)
+    else if (tagtype == LEGIC_PRIME_MIM1024)
         PrintAndLogEx(SUCCESS, "%sTYPE: " _YELLOW_("MIM%d card (1002 bytes)"), spacer, tagtype);
     else
         PrintAndLogEx(INFO, "%sTYPE: " _YELLOW_("Unknown %06x"), spacer, tagtype);
@@ -766,10 +780,10 @@ static int CmdLegicReader(const char *Cmd) {
 static int CmdLegicDump(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf legic dump",
-                  "Read all memory from LEGIC Prime MIM22, MIM256, MIM1024 and saves bin/eml/json dump file\n"
-                  "It autodetects card type.",
+                  "Read all memory from LEGIC Prime tags and saves to (bin/eml/json) dump file\n"
+                  "It autodetects card type (MIM22, MIM256, MIM1024)",
                   "hf legic dump             --> use UID as filename\n"
-                  "hf legic dump -f myfile   --> use user specified filename\n"
+                  "hf legic dump -f myfile \n"
                   "hf legic dump --de        --> use UID as filename and deobfuscate data");
 
     void *argtable[] = {
@@ -811,7 +825,7 @@ static int CmdLegicDump(const char *Cmd) {
     while (WaitForResponseTimeout(CMD_HF_LEGIC_READER, &resp, 2000) == false) {
         ++timeout;
         PrintAndLogEx(NORMAL, "." NOLF);
-        if (timeout > 7) {
+        if (timeout > 10) {
             PrintAndLogEx(WARNING, "\ncommand execution time out");
             return PM3_ETIMEOUT;
         }
@@ -868,7 +882,7 @@ static int CmdLegicDump(const char *Cmd) {
 static int CmdLegicRestore(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf legic restore",
-                  "Reads binary file and it autodetects card type and verifies that the file has the same size\n"
+                  "Reads (bin/eml/json) file and it autodetects card type and verifies that the file has the same size\n"
                   "Then write the data back to card. All bytes except the first 7bytes [UID(4) MCC(1) DCF(2)]",
                   "hf legic restore -f myfile        --> use user specified filename\n"
                   "hf legic restore -f myfile --ob   --> use UID as filename and obfuscate data");
@@ -898,21 +912,43 @@ static int CmdLegicRestore(const char *Cmd) {
     legic_print_type(card.cardsize, 0);
 
     // set up buffer
-    uint8_t *data = calloc(card.cardsize, sizeof(uint8_t));
-    if (!data) {
-        PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
-        return PM3_EMALLOC;
+    uint8_t *data = NULL;
+    size_t bytes_read = 0;
+    int res = 0;
+    DumpFileType_t dftype = getfiletype(filename);
+    switch (dftype) {
+        case BIN: {
+            res = loadFile_safe(filename, ".bin", (void **)&data, &bytes_read);
+            break;
+        }
+        case EML: {
+            res = loadFileEML_safe(filename, (void **)&data, &bytes_read);
+            break;
+        }
+        case JSON: {
+            data = calloc(LEGIC_PRIME_MIM1024, sizeof(uint8_t));
+            if (data == NULL) {
+                PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
+                return PM3_EMALLOC;
+            }
+            res = loadFileJSON(filename, (void *)data, LEGIC_PRIME_MIM1024, &bytes_read, NULL);
+            break;
+        }
+        case DICTIONARY: {
+            PrintAndLogEx(ERR, "Error: Only BIN/JSON/EML formats allowed");
+            free(data);
+            return PM3_EINVARG;
+        }
     }
 
-    size_t numofbytes;
-    if (loadFile_safe(filename, ".bin", (void **)&data, &numofbytes) != PM3_SUCCESS) {
+    if (res != PM3_SUCCESS) {
         free(data);
-        PrintAndLogEx(WARNING, "Error, reading file");
         return PM3_EFILE;
     }
 
-    if (card.cardsize != numofbytes) {
-        PrintAndLogEx(WARNING, "Fail, filesize and cardsize is not equal. [%u != %zu]", card.cardsize, numofbytes);
+    // validation
+    if (card.cardsize != bytes_read) {
+        PrintAndLogEx(WARNING, "Fail, filesize and cardsize is not equal. [%u != %zu]", card.cardsize, bytes_read);
         free(data);
         return PM3_EFILE;
     }
@@ -933,10 +969,11 @@ static int CmdLegicRestore(const char *Cmd) {
 
     // transfer to device
     PacketResponseNG resp;
-    for (size_t i = 7; i < numofbytes; i += PM3_CMD_DATA_SIZE) {
+    // 7 = skip UID bytes and MCC
+    for (size_t i = 7; i < bytes_read; i += PM3_CMD_DATA_SIZE) {
 
-        size_t len = MIN((numofbytes - i), PM3_CMD_DATA_SIZE);
-        if (len == numofbytes - i) {
+        size_t len = MIN((bytes_read - i), PM3_CMD_DATA_SIZE);
+        if (len == bytes_read - i) {
             // Disable fast mode on last packet
             g_conn.block_after_ACK = false;
         }
@@ -955,7 +992,7 @@ static int CmdLegicRestore(const char *Cmd) {
         while (WaitForResponseTimeout(CMD_HF_LEGIC_WRITER, &resp, 2000) == false) {
             ++timeout;
             PrintAndLogEx(NORMAL, "." NOLF);
-            if (timeout > 7) {
+            if (timeout > 10) {
                 PrintAndLogEx(WARNING, "\ncommand execution time out");
                 free(data);
                 return PM3_ETIMEOUT;
@@ -964,7 +1001,7 @@ static int CmdLegicRestore(const char *Cmd) {
         PrintAndLogEx(NORMAL, "");
 
         if (resp.status != PM3_SUCCESS) {
-            PrintAndLogEx(WARNING, "Failed writing tag [msg = %u]", (uint8_t)(resp.oldarg[1] & 0xFF));
+            PrintAndLogEx(WARNING, "Failed writing tag");
             free(data);
             return PM3_ERFTRANS;
         }
@@ -979,15 +1016,14 @@ static int CmdLegicRestore(const char *Cmd) {
 static int CmdLegicELoad(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf legic eload",
-                  "Loads a LEGIC binary dump into emulator memory",
-                  "hf legic eload -f myfile -t 0  -> Simulate Type MIM22\n"
-                  "hf legic eload -f myfile -t 1  -> Simulate Type MIM256 (def)\n"
-                  "hf legic eload -f myfile -t 2  -> Simulate Type MIM1024");
+                  "Loads a LEGIC Prime dump file into emulator memory",
+                  "hf legic eload -f myfile\n"
+                  "hf legic eload -f myfile --obfuscate\n"
+                );
 
     void *argtable[] = {
         arg_param_begin,
-        arg_str1("f", "file", "<fn>", "Filename to restore"),
-        arg_int0("t", "type", "<dec>", "Tag type to simulate."),
+        arg_str1("f", "file", "<fn>", "Filename to load"),
         arg_lit0(NULL, "obfuscate", "Obfuscate dump data (xor with MCC)"),
         arg_param_end
     };
@@ -997,67 +1033,81 @@ static int CmdLegicELoad(const char *Cmd) {
     char filename[FILE_PATH_SIZE] = {0};
     CLIParamStrToBuf(arg_get_str(ctx, 1), (uint8_t *)filename, FILE_PATH_SIZE, &fnlen);
 
-    size_t numofbytes = 0;
+    bool shall_obsfuscate = arg_get_lit(ctx, 2);
+    CLIParserFree(ctx);
 
-    switch (arg_get_int_def(ctx, 2, 1)) {
-        case 0:
-            numofbytes = 22;
+    // set up buffer
+    uint8_t *data = NULL;
+    size_t bytes_read = 0;
+    int res = 0;
+    DumpFileType_t dftype = getfiletype(filename);
+    switch (dftype) {
+        case BIN: {
+            res = loadFile_safe(filename, ".bin", (void **)&data, &bytes_read);
             break;
-        case 1:
-            numofbytes = 256;
+        }
+        case EML: {
+            res = loadFileEML_safe(filename, (void **)&data, &bytes_read);
             break;
-        case 2:
-            numofbytes = 1024;
+        }
+        case JSON: {
+            data = calloc(LEGIC_PRIME_MIM1024, sizeof(uint8_t));
+            if (data == NULL) {
+                PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
+                return PM3_EMALLOC;
+            }
+            res = loadFileJSON(filename, (void *)data, LEGIC_PRIME_MIM1024, &bytes_read, NULL);
             break;
-        default:
-            PrintAndLogEx(ERR, "Unknown tag type");
-            CLIParserFree(ctx);
+        }
+        case DICTIONARY: {
+            PrintAndLogEx(ERR, "Error: Only BIN/JSON/EML formats allowed");
+            free(data);
             return PM3_EINVARG;
+        }
     }
 
-    bool shall_obsfuscate = arg_get_lit(ctx, 3);
-
-    CLIParserFree(ctx);
-
-    // set up buffer
-    uint8_t *data = calloc(numofbytes, sizeof(uint8_t));
-    if (!data) {
-        PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
-        return PM3_EMALLOC;
+    if (res != PM3_SUCCESS) {
+        free(data);
+        return PM3_EFILE;
     }
 
-    if (loadFile_safe(filename, ".bin", (void **)&data, &numofbytes) != PM3_SUCCESS) {
+    // validation
+    if (bytes_read != LEGIC_PRIME_MIM22 &&
+        bytes_read != LEGIC_PRIME_MIM256 &&
+        bytes_read != LEGIC_PRIME_MIM1024) {
+        PrintAndLogEx(ERR, "File content error. Read %zu bytes", bytes_read);
         free(data);
-        PrintAndLogEx(WARNING, "Error, reading file");
         return PM3_EFILE;
     }
 
     if (shall_obsfuscate) {
-        legic_xor(data, numofbytes);
+        legic_xor(data, bytes_read);
     }
 
     PrintAndLogEx(SUCCESS, "Uploading to emulator memory");
-    legic_seteml(data, 0, numofbytes);
+    legic_seteml(data, 0, bytes_read);
 
     free(data);
-    PrintAndLogEx(SUCCESS, "Done");
+    PrintAndLogEx(SUCCESS, "Done!");
     return PM3_SUCCESS;
 }
 
 static int CmdLegicESave(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf legic esave",
-                  "Saves bin/eml/json dump file of emulator memory",
-                  "hf legic esave                  --> uses UID as filename\n"
-                  "hf legic esave -f myfile -t 0   --> Type MIM22\n"
-                  "hf legic esave -f myfile -t 1   --> Type MIM256 (def)\n"
-                  "hf legic esave -f myfile -t 2   --> Type MIM1024");
+                  "Saves a (bin/eml/json) dump file of emulator memory",
+                  "hf legic esave                    --> uses UID as filename\n"
+                  "hf legic esave -f myfile --22\n"
+                  "hf legic esave -f myfile --22 --de\n"
+                );
 
     void *argtable[] = {
         arg_param_begin,
         arg_str0("f", "file", "<fn>", "Filename to save"),
-        arg_int0("t", "type", "<dec>", "Tag type"),
-        arg_lit0(NULL, "deobfuscate", "De-obfuscate dump data (xor with MCC)"),
+        arg_lit0(NULL, "22", "LEGIC Prime MIM22"),
+        arg_lit0(NULL, "256", "LEGIC Prime MIM256 (def)"),
+        arg_lit0(NULL, "1024", "LEGIC Prime MIM1024"),
+        arg_lit0(NULL, "de", "De-obfuscate dump data (xor with MCC)"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
@@ -1066,27 +1116,27 @@ static int CmdLegicESave(const char *Cmd) {
     char filename[FILE_PATH_SIZE] = {0};
     CLIParamStrToBuf(arg_get_str(ctx, 1), (uint8_t *)filename, FILE_PATH_SIZE, &fnlen);
 
-    size_t numofbytes = 0;
+    bool m1 = arg_get_lit(ctx, 2);
+    bool m2 = arg_get_lit(ctx, 3);
+    bool m3 = arg_get_lit(ctx, 4);
+    bool shall_deobsfuscate = arg_get_lit(ctx, 5);
+    CLIParserFree(ctx);
 
-    switch (arg_get_int_def(ctx, 2, 1)) {
-        case 0:
-            numofbytes = 22;
-            break;
-        case 1:
-            numofbytes = 256;
-            break;
-        case 2:
-            numofbytes = 1024;
-            break;
-        default:
-            PrintAndLogEx(ERR, "Unknown tag type");
-            CLIParserFree(ctx);
-            return PM3_EINVARG;
+    // validations
+    if (m1 + m2 + m3 > 1) {
+        PrintAndLogEx(WARNING, "Only specify one LEGIC Prime Type");
+        return PM3_EINVARG;
+    } else if (m1 + m2 + m3 == 0) {
+        m2 = true;
     }
 
-    bool shall_deobsfuscate = arg_get_lit(ctx, 3);
-
-    CLIParserFree(ctx);
+    size_t numofbytes = LEGIC_PRIME_MIM256;
+    if (m1)
+        numofbytes = LEGIC_PRIME_MIM22;
+    else if (m2)
+        numofbytes = LEGIC_PRIME_MIM256;
+    else if (m3)
+        numofbytes = LEGIC_PRIME_MIM1024;
 
     // set up buffer
     uint8_t *data = calloc(numofbytes, sizeof(uint8_t));
@@ -1126,38 +1176,37 @@ static int CmdLegicEView(const char *Cmd) {
     CLIParserInit(&ctx, "hf legic eview",
                   "It displays emulator memory",
                   "hf legic eview\n"
-                  "hf legic eview -t 0  -> MIM22\n"
-                  "hf legic eview -t 1  -> MIM256 (def)\n"
-                  "hf legic eview -t 2  -> MIM1024"
+                  "hf legic eview --22\n"
                  );
     void *argtable[] = {
         arg_param_begin,
-        arg_int0("t", "type", "<dec>", "Tag type to simulate."),        
+        arg_lit0(NULL, "22", "LEGIC Prime MIM22"),
+        arg_lit0(NULL, "256", "LEGIC Prime MIM256 (def)"),
+        arg_lit0(NULL, "1024", "LEGIC Prime MIM1024"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
 
-    size_t bytes = 0;
-
-    switch (arg_get_int_def(ctx, 1, 1)) {
-        case 0:
-            bytes = 22;
-            break;
-        case 1:
-            bytes = 256;
-            break;
-        case 2:
-            bytes = LEGIC_PRIME_MAX_LENGTH;
-            break;
-        default:
-            PrintAndLogEx(ERR, "Unknown tag type");
-            CLIParserFree(ctx);
-            return PM3_EINVARG;
-    }
-
+    bool m1 = arg_get_lit(ctx, 1);
+    bool m2 = arg_get_lit(ctx, 2);
+    bool m3 = arg_get_lit(ctx, 3);
     CLIParserFree(ctx);
 
     // validations
+    if (m1 + m2 + m3 > 1) {
+        PrintAndLogEx(WARNING, "Only specify one LEGIC Prime Type");
+        return PM3_EINVARG;
+    } else if (m1 + m2 + m3 == 0) {
+        m2 = true;
+    }
+
+    size_t bytes = LEGIC_PRIME_MIM256;
+    if (m1)
+        bytes = LEGIC_PRIME_MIM22;
+    else if (m2)
+        bytes = LEGIC_PRIME_MIM256;
+    else if (m3)
+        bytes = LEGIC_PRIME_MIM1024;
 
     uint8_t *dump = calloc(bytes, sizeof(uint8_t));
     if (dump == NULL) {
@@ -1240,7 +1289,7 @@ static int CmdLegicWipe(const char *Cmd) {
         while (WaitForResponseTimeout(CMD_HF_LEGIC_WRITER, &resp, 2000) == false) {
             ++timeout;
             PrintAndLogEx(NORMAL, "." NOLF);
-            if (timeout > 7) {
+            if (timeout > 10) {
                 PrintAndLogEx(WARNING, "\ncommand execution time out");
                 free(data);
                 return PM3_ETIMEOUT;
@@ -1291,12 +1340,12 @@ static int CmdLegicView(const char *Cmd) {
             break;
         }
         case JSON: {
-            dump = calloc(LEGIC_PRIME_MAX_LENGTH, sizeof(uint8_t));
+            dump = calloc(LEGIC_PRIME_MIM1024, sizeof(uint8_t));
             if (dump == NULL) {
                 PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
                 return PM3_EMALLOC;
             }
-            res = loadFileJSON(filename, (void *)dump, LEGIC_PRIME_MAX_LENGTH, &bytes_read, NULL);
+            res = loadFileJSON(filename, (void *)dump, LEGIC_PRIME_MIM1024, &bytes_read, NULL);
             break;
         }
         case EML:
