commit 08970f51c22eb312c9b6aeb5bc60e2aa46823681
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Feb 5 09:18:24 2022 +0100

    hf mf restore - now takes bin/eml/json as dump files.  It also checks for ALL ZERO accessrights in dump and replaces them with default configuration instead to prevent unwanted experiences

diff --git a/CHANGELOG.md b/CHANGELOG.md
index a9c40d876..5a4e9cc5e 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,7 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Fixes `hf mf restore` - now takes bin/eml/json as dump files (@iceman1001)
  - Added `trace extract` - extract authentication parts from trace (@iceman1001)
  - Added luascript `hf_mf_ultimatecard.lua` - Script for Ultimate Magic Card (GEN4) (@startrk1995)
  - Added new tool `brute_key` - MIFARE DESFire Telenot access AES recovery (@x41sec)
diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index 961c7146f..f501aab28 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -204,22 +204,6 @@ static uint8_t NumOfSectors(char card) {
     }
 }
 
-static uint8_t FirstBlockOfSector(uint8_t sectorNo) {
-    if (sectorNo < 32) {
-        return sectorNo * 4;
-    } else {
-        return 32 * 4 + (sectorNo - 32) * 16;
-    }
-}
-
-static uint8_t NumBlocksPerSector(uint8_t sectorNo) {
-    if (sectorNo < 32) {
-        return 4;
-    } else {
-        return 16;
-    }
-}
-
 static uint8_t GetSectorFromBlockNo(uint8_t blockNo) {
     if (blockNo < 32 * 4)
         return blockNo / 4;
@@ -227,8 +211,8 @@ static uint8_t GetSectorFromBlockNo(uint8_t blockNo) {
         return 32 + ((blockNo - (32 * 4)) / 16);
 }
 
-static char GetFormatFromSector(uint8_t sectorNo) {
-    switch (sectorNo) {
+static char GetFormatFromSector(uint8_t sectors) {
+    switch (sectors) {
         case MIFARE_MINI_MAXSECTOR:
             return '0';
         case MIFARE_1K_MAXSECTOR:
@@ -550,8 +534,8 @@ static int CmdHF14AMfRdSc(const char *Cmd) {
     int res =  mfReadSector(sector, keytype, key, data);
     if (res == PM3_SUCCESS) {
 
-        uint8_t blocks = NumBlocksPerSector(sector);
-        uint8_t start = FirstBlockOfSector(sector);
+        uint8_t blocks = mfNumBlocksPerSector(sector);
+        uint8_t start = mfFirstBlockOfSector(sector);
 
         mf_print_sector_hdr(sector);
         for (int i = 0; i < blocks; i++) {
@@ -747,7 +731,7 @@ static int CmdHF14AMfDump(const char *Cmd) {
             PrintAndLogEx(NORMAL, "." NOLF);
             fflush(stdout);
 
-            payload.blockno = FirstBlockOfSector(sectorNo) + NumBlocksPerSector(sectorNo) - 1;
+            payload.blockno = mfFirstBlockOfSector(sectorNo) + mfNumBlocksPerSector(sectorNo) - 1;
             payload.keytype = MF_KEY_A;
             memcpy(payload.key, keyA[sectorNo], sizeof(payload.key));
 
@@ -780,13 +764,13 @@ static int CmdHF14AMfDump(const char *Cmd) {
     PrintAndLogEx(INFO, "Dumping all blocks from card...");
 
     for (sectorNo = 0; sectorNo < numSectors; sectorNo++) {
-        for (blockNo = 0; blockNo < NumBlocksPerSector(sectorNo); blockNo++) {
+        for (blockNo = 0; blockNo < mfNumBlocksPerSector(sectorNo); blockNo++) {
             bool received = false;
 
             for (tries = 0; tries < MIFARE_SECTOR_RETRY; tries++) {
-                if (blockNo == NumBlocksPerSector(sectorNo) - 1) { // sector trailer. At least the Access Conditions can always be read with key A.
+                if (blockNo == mfNumBlocksPerSector(sectorNo) - 1) { // sector trailer. At least the Access Conditions can always be read with key A.
 
-                    payload.blockno = FirstBlockOfSector(sectorNo) + blockNo;
+                    payload.blockno = mfFirstBlockOfSector(sectorNo) + blockNo;
                     payload.keytype = MF_KEY_A;
                     memcpy(payload.key, keyA[sectorNo], sizeof(payload.key));
 
@@ -797,7 +781,7 @@ static int CmdHF14AMfDump(const char *Cmd) {
                     uint8_t data_area = (sectorNo < 32) ? blockNo : blockNo / 5;
                     if ((rights[sectorNo][data_area] == 0x03) || (rights[sectorNo][data_area] == 0x05)) { // only key B would work
 
-                        payload.blockno = FirstBlockOfSector(sectorNo) + blockNo;
+                        payload.blockno = mfFirstBlockOfSector(sectorNo) + blockNo;
                         payload.keytype = 1;
                         memcpy(payload.key, keyB[sectorNo], sizeof(payload.key));
 
@@ -810,7 +794,7 @@ static int CmdHF14AMfDump(const char *Cmd) {
                         break;
                     } else {                                                                              // key A would work
 
-                        payload.blockno = FirstBlockOfSector(sectorNo) + blockNo;
+                        payload.blockno = mfFirstBlockOfSector(sectorNo) + blockNo;
                         payload.keytype = MF_KEY_A;
                         memcpy(payload.key, keyA[sectorNo], sizeof(payload.key));
 
@@ -829,13 +813,14 @@ static int CmdHF14AMfDump(const char *Cmd) {
 
             if (received) {
                 uint8_t *data  = resp.data.asBytes;
-                if (blockNo == NumBlocksPerSector(sectorNo) - 1) { // sector trailer. Fill in the keys.
+                if (blockNo == mfNumBlocksPerSector(sectorNo) - 1) { // sector trailer. Fill in the keys.
                     data[0]  = (keyA[sectorNo][0]);
                     data[1]  = (keyA[sectorNo][1]);
                     data[2]  = (keyA[sectorNo][2]);
                     data[3]  = (keyA[sectorNo][3]);
                     data[4]  = (keyA[sectorNo][4]);
                     data[5]  = (keyA[sectorNo][5]);
+
                     data[10] = (keyB[sectorNo][0]);
                     data[11] = (keyB[sectorNo][1]);
                     data[12] = (keyB[sectorNo][2]);
@@ -844,7 +829,7 @@ static int CmdHF14AMfDump(const char *Cmd) {
                     data[15] = (keyB[sectorNo][5]);
                 }
                 if (resp.status == PM3_SUCCESS) {
-                    memcpy(carddata[FirstBlockOfSector(sectorNo) + blockNo], data, 16);
+                    memcpy(carddata[mfFirstBlockOfSector(sectorNo) + blockNo], data, 16);
                     PrintAndLogEx(SUCCESS, "successfully read block %2d of sector %2d.", blockNo, sectorNo);
                 } else {
                     PrintAndLogEx(FAILED, "could not read block %2d of sector %2d", blockNo, sectorNo);
@@ -870,7 +855,7 @@ static int CmdHF14AMfDump(const char *Cmd) {
         free(fptr);
     }
 
-    uint16_t bytes = 16 * (FirstBlockOfSector(numSectors - 1) + NumBlocksPerSector(numSectors - 1));
+    uint16_t bytes = 16 * (mfFirstBlockOfSector(numSectors - 1) + mfNumBlocksPerSector(numSectors - 1));
 
     saveFile(dataFilename, ".bin", (uint8_t *)carddata, bytes);
     saveFileEML(dataFilename, (uint8_t *)carddata, bytes, MFBLOCK_SIZE);
@@ -887,10 +872,11 @@ static int CmdHF14AMfRestore(const char *Cmd) {
 
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mf restore",
-                  "Restore MIFARE Classic binary file to tag.\n"
+                  "Restore MIFARE Classic dump file to tag.\n"
                   "\n"
-                  "The key file and data file will program the card sector trailers.\n"
+                  "The key file and dump file will program the card sector trailers.\n"
                   "By default we authenticate to card with key B 0xFFFFFFFFFFFF.\n"
+                  "If access rights in dump file is all zeros,  it will be replaced with default values\n"
                   "\n"
                   "`--uid` param is used for filename templates `hf-mf-<uid>-dump.bin` and `hf-mf-<uid>-key.bin.\n"
                   "        If not specified, it will read the card uid instead.\n"
@@ -909,7 +895,7 @@ static int CmdHF14AMfRestore(const char *Cmd) {
         arg_lit0(NULL, "2k", "MIFARE Classic/Plus 2k"),
         arg_lit0(NULL, "4k", "MIFARE Classic 4k / S70"),
         arg_str0("u", "uid",  "<hex>", "uid, 6 hex bytes"),
-        arg_str0("f", "file", "<fn>", "data filename"),
+        arg_str0("f", "file", "<fn>", "specify dump filename (bin/eml/json)"),
         arg_str0("k", "kfn",  "<fn>", "key filename"),
         arg_lit0(NULL, "ka",  "use specified keyfile to authenticate"),
         arg_param_end
@@ -944,15 +930,6 @@ static int CmdHF14AMfRestore(const char *Cmd) {
         m1 = true;
     }
 
-    if(datafnlen > 4 && !strcmp(datafilename + strlen(datafilename) - 4, ".eml")) {
-        PrintAndLogEx(WARNING, "File must be a binary dump, not a .eml");
-        return PM3_EINVARG;
-    }
-    if(datafnlen > 5 && !strcmp(datafilename + strlen(datafilename) - 5, ".json")) {
-        PrintAndLogEx(WARNING, "File must be a binary dump, not a .json");
-        return PM3_EINVARG;
-    }
-
     uint8_t sectors = MIFARE_1K_MAXSECTOR;
 
     if (m0) {
@@ -1022,7 +999,6 @@ static int CmdHF14AMfRestore(const char *Cmd) {
         }
     }
     fclose(f);
-    f = NULL;
 
     // try reading card uid and create filename
     if (datafnlen == 0) {
@@ -1035,9 +1011,38 @@ static int CmdHF14AMfRestore(const char *Cmd) {
     }
 
     // read dump file
-    if ((f = fopen(datafilename, "rb")) == NULL) {
-        PrintAndLogEx(WARNING, "Could not find file " _YELLOW_("%s"), datafilename);
-        return PM3_EINVARG;
+    bytes_read = 0;
+    uint8_t *dump = NULL;
+    int res = 0;
+    DumpFileType_t dftype = getfiletype(datafilename);
+    switch (dftype) {
+        case BIN: {
+            res = loadFile_safe(datafilename, ".bin", (void **)&dump, &bytes_read);
+            break;
+        }
+        case EML: {
+            res = loadFileEML_safe(datafilename, (void **)&dump, &bytes_read);
+            break;
+        }
+        case JSON: {
+            dump = calloc(MFBLOCK_SIZE * MIFARE_4K_MAXBLOCK, sizeof(uint8_t));
+            if (dump == NULL) {
+                PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
+                return PM3_EMALLOC;
+            }
+            res = loadFileJSON(datafilename, (void *)dump, MIFARE_4K_MAXBLOCK * MFBLOCK_SIZE, &bytes_read, NULL);
+            break;
+        }
+        case DICTIONARY: {
+            PrintAndLogEx(ERR, "Error: Only BIN/JSON/EML formats allowed");
+            free(dump);
+            return PM3_EINVARG;
+        }
+    }
+
+    if (res != PM3_SUCCESS) {
+        free(dump);
+        return PM3_EFILE;
     }
 
     // default authentication key
@@ -1045,30 +1050,31 @@ static int CmdHF14AMfRestore(const char *Cmd) {
 
     PrintAndLogEx(INFO, "Restoring " _YELLOW_("%s")" to card", datafilename);
 
+    // main loop for restoreing. 
+    // a bit more complicated than needed
+    // this is because of two things.
+    // 1. we are setting keys from a key file or using the existing ones in the dump
+    // 2. we need to authenticate against a card which might not have default keys.
+    uint8_t *ref_dump = dump;
     for (uint8_t s = 0; s < sectors; s++) {
-        for (uint8_t b = 0; b < NumBlocksPerSector(s); b++) {
+        for (uint8_t b = 0; b < mfNumBlocksPerSector(s); b++) {
 
-            uint8_t data[26];
             uint8_t bldata[MFBLOCK_SIZE] = {0x00};
-
-            bytes_read = fread(bldata, 1, MFBLOCK_SIZE, f);
-            if (bytes_read != sizeof(bldata)) {
-                PrintAndLogEx(ERR, "File reading error " _YELLOW_("%s"), datafilename);
-                fclose(f);
-                f = NULL;
-                return PM3_EFILE;
-            }
+            
+            memcpy(bldata, dump, MFBLOCK_SIZE);
 
             if (use_keyfile_for_auth == false) {
 
-                // sector trailer
-                if (b == NumBlocksPerSector(s) - 1) {
+                // if sector trailer replace key 
+                if (mfNumBlocksPerSector(s)-1 == b) {
+                    // replace KEY A
                     bldata[0]  = (keyA[s][0]);
                     bldata[1]  = (keyA[s][1]);
                     bldata[2]  = (keyA[s][2]);
                     bldata[3]  = (keyA[s][3]);
                     bldata[4]  = (keyA[s][4]);
                     bldata[5]  = (keyA[s][5]);
+                    // replace KEY B
                     bldata[10] = (keyB[s][0]);
                     bldata[11] = (keyB[s][1]);
                     bldata[12] = (keyB[s][2]);
@@ -1078,21 +1084,34 @@ static int CmdHF14AMfRestore(const char *Cmd) {
                 }
             }
 
-            memcpy(data + 10, bldata, sizeof(bldata));
+            if (mfNumBlocksPerSector(s)-1 == b) {
+                // ensure accessright isn't messed up.
+                if (memcmp(bldata + 6, "\x00\x00\x00\x00", 4) == 0) {
+                    memcpy(bldata + 6, "\xFF\x07\x80\x69", 4);
+                }
+            }
+
+            if (bytes_read) {
+                dump += MFBLOCK_SIZE;
+                bytes_read -= MFBLOCK_SIZE;
+            }
+
+            uint8_t wdata[26];
+            memcpy(wdata + 10, bldata, sizeof(bldata));
 
             if (use_keyfile_for_auth) {
                 for (int8_t kt = MF_KEY_B; kt > -1; kt--) {
 
                     if (kt == MF_KEY_A)
-                        memcpy(data, keyA[s], 6);
+                        memcpy(wdata, keyA[s], 6);
                     else
-                        memcpy(data, keyB[s], 6);
+                        memcpy(wdata, keyB[s], 6);
+
+                    PrintAndLogEx(INFO, "block %3d: %s", mfFirstBlockOfSector(s) + b, sprint_hex(bldata, sizeof(bldata)));
 
-                    PrintAndLogEx(INFO, "block %3d: %s", FirstBlockOfSector(s) + b, sprint_hex(bldata, sizeof(bldata)));
                     clearCommandBuffer();
-                    SendCommandMIX(CMD_HF_MIFARE_WRITEBL, FirstBlockOfSector(s) + b, kt, 0, data, sizeof(data));
+                    SendCommandMIX(CMD_HF_MIFARE_WRITEBL, mfFirstBlockOfSector(s) + b, kt, 0, wdata, sizeof(wdata));
                     PacketResponseNG resp;
-
                     if (WaitForResponseTimeout(CMD_ACK, &resp, 1500)) {
                         uint8_t isOK  = resp.oldarg[0] & 0xff;
                         if (isOK == 0) {
@@ -1110,11 +1129,12 @@ static int CmdHF14AMfRestore(const char *Cmd) {
                     }
                 }
             } else {
-                memcpy(data, default_key, 6);
-                PrintAndLogEx(INFO, "block %3d: %s", FirstBlockOfSector(s) + b, sprint_hex(bldata, sizeof(bldata)));
-                clearCommandBuffer();
-                SendCommandMIX(CMD_HF_MIFARE_WRITEBL, FirstBlockOfSector(s) + b, MF_KEY_B, 0, data, sizeof(data));
+                // use default key to authenticate for the write command
+                memcpy(wdata, default_key, 6);
+                PrintAndLogEx(INFO, "block %3d: %s", mfFirstBlockOfSector(s) + b, sprint_hex(bldata, sizeof(bldata)));
 
+                clearCommandBuffer();
+                SendCommandMIX(CMD_HF_MIFARE_WRITEBL, mfFirstBlockOfSector(s) + b, MF_KEY_B, 0, wdata, sizeof(wdata));
                 PacketResponseNG resp;
                 if (WaitForResponseTimeout(CMD_ACK, &resp, 1500)) {
                     uint8_t isOK  = resp.oldarg[0] & 0xff;
@@ -1128,10 +1148,11 @@ static int CmdHF14AMfRestore(const char *Cmd) {
                 } else {
                     PrintAndLogEx(WARNING, "Command execute timeout");
                 }
-            }
-        }
-    }
-    fclose(f);
+            } // end use_keyfile_for_auth
+        } // end loop B
+    } // end loop S
+
+    free(ref_dump);
     PrintAndLogEx(INFO, "Done!");
     return PM3_SUCCESS;
 }
@@ -1333,7 +1354,7 @@ static int CmdHF14AMfNested(const char *Cmd) {
 
                     if (e_sector[sectorNo].foundKey[trgKeyType]) continue;
 
-                    int16_t isOK = mfnested(blockNo, keyType, key, FirstBlockOfSector(sectorNo), trgKeyType, keyBlock, calibrate);
+                    int16_t isOK = mfnested(blockNo, keyType, key, mfFirstBlockOfSector(sectorNo), trgKeyType, keyBlock, calibrate);
                     switch (isOK) {
                         case PM3_ETIMEOUT:
                             PrintAndLogEx(ERR, "Command execute timeout\n");
@@ -1374,7 +1395,7 @@ static int CmdHF14AMfNested(const char *Cmd) {
             // KEY A but not KEY B
             if (e_sector[i].foundKey[0] && !e_sector[i].foundKey[1]) {
 
-                uint8_t sectrail = (FirstBlockOfSector(i) + NumBlocksPerSector(i) - 1);
+                uint8_t sectrail = (mfFirstBlockOfSector(i) + mfNumBlocksPerSector(i) - 1);
 
                 PrintAndLogEx(SUCCESS, "reading block %d", sectrail);
 
@@ -1415,7 +1436,7 @@ jumptoend:
             // fast push mode
             g_conn.block_after_ACK = true;
             for (int i = 0; i < SectorsCnt; i++) {
-                mfEmlGetMem(keyBlock, FirstBlockOfSector(i) + NumBlocksPerSector(i) - 1, 1);
+                mfEmlGetMem(keyBlock, mfFirstBlockOfSector(i) + mfNumBlocksPerSector(i) - 1, 1);
 
                 if (e_sector[i].foundKey[0])
                     num_to_bytes(e_sector[i].Key[0], 6, keyBlock);
@@ -1427,7 +1448,7 @@ jumptoend:
                     // Disable fast mode on last packet
                     g_conn.block_after_ACK = false;
                 }
-                mfEmlSetMem(keyBlock, FirstBlockOfSector(i) + NumBlocksPerSector(i) - 1, 1);
+                mfEmlSetMem(keyBlock, mfFirstBlockOfSector(i) + mfNumBlocksPerSector(i) - 1, 1);
             }
             PrintAndLogEx(SUCCESS, "keys transferred to emulator memory.");
         }
@@ -1586,7 +1607,7 @@ static int CmdHF14AMfNestedStatic(const char *Cmd) {
 
                 if (e_sector[sectorNo].foundKey[trgKeyType]) continue;
 
-                int16_t isOK = mfStaticNested(blockNo, keyType, key, FirstBlockOfSector(sectorNo), trgKeyType, keyBlock);
+                int16_t isOK = mfStaticNested(blockNo, keyType, key, mfFirstBlockOfSector(sectorNo), trgKeyType, keyBlock);
                 switch (isOK) {
                     case PM3_ETIMEOUT :
                         PrintAndLogEx(ERR, "Command execute timeout");
@@ -1621,7 +1642,7 @@ static int CmdHF14AMfNestedStatic(const char *Cmd) {
         // KEY A but not KEY B
         if (e_sector[i].foundKey[0] && !e_sector[i].foundKey[1]) {
 
-            uint8_t sectrail = (FirstBlockOfSector(i) + NumBlocksPerSector(i) - 1);
+            uint8_t sectrail = (mfFirstBlockOfSector(i) + mfNumBlocksPerSector(i) - 1);
 
             PrintAndLogEx(SUCCESS, "reading block %d", sectrail);
 
@@ -1662,7 +1683,7 @@ jumptoend:
         // fast push mode
         g_conn.block_after_ACK = true;
         for (int i = 0; i < SectorsCnt; i++) {
-            mfEmlGetMem(keyBlock, FirstBlockOfSector(i) + NumBlocksPerSector(i) - 1, 1);
+            mfEmlGetMem(keyBlock, mfFirstBlockOfSector(i) + mfNumBlocksPerSector(i) - 1, 1);
 
             if (e_sector[i].foundKey[0])
                 num_to_bytes(e_sector[i].Key[0], 6, keyBlock);
@@ -1674,7 +1695,7 @@ jumptoend:
                 // Disable fast mode on last packet
                 g_conn.block_after_ACK = false;
             }
-            mfEmlSetMem(keyBlock, FirstBlockOfSector(i) + NumBlocksPerSector(i) - 1, 1);
+            mfEmlSetMem(keyBlock, mfFirstBlockOfSector(i) + mfNumBlocksPerSector(i) - 1, 1);
         }
         PrintAndLogEx(SUCCESS, "keys transferred to emulator memory.");
     }
@@ -2139,7 +2160,7 @@ static int CmdHF14AMfAutoPWN(const char *Cmd) {
             PrintAndLogEx(INFO, "======================= " _YELLOW_("START KNOWN KEY ATTACK") " =======================");
         }
 
-        if (mfCheckKeys(FirstBlockOfSector(sectorno), keytype, true, 1, key, &key64) == PM3_SUCCESS) {
+        if (mfCheckKeys(mfFirstBlockOfSector(sectorno), keytype, true, 1, key, &key64) == PM3_SUCCESS) {
             PrintAndLogEx(INFO, "target sector %3u key type %c -- using valid key [ " _GREEN_("%s") "] (used for nested / hardnested attack)",
                           sectorno,
                           (keytype == MF_KEY_B) ? 'B' : 'A',
@@ -2165,7 +2186,7 @@ static int CmdHF14AMfAutoPWN(const char *Cmd) {
         for (int i = 0; i < sector_cnt; i++) {
             for (int j = MF_KEY_A; j <= MF_KEY_B; j++) {
                 if (e_sector[i].foundKey[j] == 0) {
-                    if (mfCheckKeys(FirstBlockOfSector(i), j, true, 1, key, &key64) == PM3_SUCCESS) {
+                    if (mfCheckKeys(mfFirstBlockOfSector(i), j, true, 1, key, &key64) == PM3_SUCCESS) {
                         e_sector[i].Key[j] = bytes_to_num(key, 6);
                         e_sector[i].foundKey[j] = 'U';
 
@@ -2240,7 +2261,7 @@ static int CmdHF14AMfAutoPWN(const char *Cmd) {
                         PrintAndLogEx(NORMAL, "." NOLF);
                         fflush(stdout);
 
-                        if (mfCheckKeys(FirstBlockOfSector(i), j, true, 1, (keyBlock + (6 * k)), &key64) == PM3_SUCCESS) {
+                        if (mfCheckKeys(mfFirstBlockOfSector(i), j, true, 1, (keyBlock + (6 * k)), &key64) == PM3_SUCCESS) {
                             e_sector[i].Key[j] = bytes_to_num((keyBlock + (6 * k)), 6);
                             e_sector[i].foundKey[j] = 'D';
                             ++num_found_keys;
@@ -2323,7 +2344,7 @@ static int CmdHF14AMfAutoPWN(const char *Cmd) {
             if (verbose) {
                 PrintAndLogEx(INFO, "======================= " _YELLOW_("START DARKSIDE ATTACK") " =======================");
             }
-            isOK = mfDarkside(FirstBlockOfSector(sectorno), keytype + 0x60, &key64);
+            isOK = mfDarkside(mfFirstBlockOfSector(sectorno), keytype + 0x60, &key64);
 
             switch (isOK) {
                 case -1 :
@@ -2389,7 +2410,7 @@ noValidKeyFound:
                                 continue;
 
                             // Check if the key works
-                            if (mfCheckKeys(FirstBlockOfSector(i), j, true, 1, tmp_key, &key64) == PM3_SUCCESS) {
+                            if (mfCheckKeys(mfFirstBlockOfSector(i), j, true, 1, tmp_key, &key64) == PM3_SUCCESS) {
                                 e_sector[i].Key[j] = bytes_to_num(tmp_key, 6);
                                 e_sector[i].foundKey[j] = 'R';
                                 PrintAndLogEx(SUCCESS, "target sector %3u key type %c -- found valid key [ " _GREEN_("%s") " ]",
@@ -2412,7 +2433,7 @@ noValidKeyFound:
                                           current_sector_i,
                                           (current_key_type_i == MF_KEY_B) ? 'B' : 'A');
                         }
-                        uint8_t sectrail = (FirstBlockOfSector(current_sector_i) + NumBlocksPerSector(current_sector_i) - 1);
+                        uint8_t sectrail = (mfFirstBlockOfSector(current_sector_i) + mfNumBlocksPerSector(current_sector_i) - 1);
 
                         mf_readblock_t payload;
                         payload.blockno = sectrail;
@@ -2468,7 +2489,7 @@ skipReadBKey:
                                           (current_key_type_i == MF_KEY_B) ? 'B' : 'A');
                         }
 tryNested:
-                        isOK = mfnested(FirstBlockOfSector(sectorno), keytype, key, FirstBlockOfSector(current_sector_i), current_key_type_i, tmp_key, calibrate);
+                        isOK = mfnested(mfFirstBlockOfSector(sectorno), keytype, key, mfFirstBlockOfSector(current_sector_i), current_key_type_i, tmp_key, calibrate);
 
                         switch (isOK) {
                             case PM3_ETIMEOUT: {
@@ -2526,7 +2547,7 @@ tryHardnested: // If the nested attack fails then we try the hardnested attack
                                           slow ? "Yes" : "No");
                         }
 
-                        isOK = mfnestedhard(FirstBlockOfSector(sectorno), keytype, key, FirstBlockOfSector(current_sector_i), current_key_type_i, NULL, false, false, slow, 0, &foundkey, NULL);
+                        isOK = mfnestedhard(mfFirstBlockOfSector(sectorno), keytype, key, mfFirstBlockOfSector(current_sector_i), current_key_type_i, NULL, false, false, slow, 0, &foundkey, NULL);
                         DropField();
                         if (isOK) {
                             switch (isOK) {
@@ -2562,7 +2583,7 @@ tryStaticnested:
                                           (current_key_type_i == MF_KEY_B) ? 'B' : 'A');
                         }
 
-                        isOK = mfStaticNested(sectorno, keytype, key, FirstBlockOfSector(current_sector_i), current_key_type_i, tmp_key);
+                        isOK = mfStaticNested(sectorno, keytype, key, mfFirstBlockOfSector(current_sector_i), current_key_type_i, tmp_key);
                         DropField();
                         switch (isOK) {
                             case PM3_ETIMEOUT: {
@@ -2629,7 +2650,7 @@ all_found:
         if (e_sector[current_sector_i].foundKey[1])
             num_to_bytes(e_sector[current_sector_i].Key[1], 6, block + 10);
 
-        mfEmlSetMem(block, FirstBlockOfSector(current_sector_i) + NumBlocksPerSector(current_sector_i) - 1, 1);
+        mfEmlSetMem(block, mfFirstBlockOfSector(current_sector_i) + mfNumBlocksPerSector(current_sector_i) - 1, 1);
     }
 
     // use ecfill trick
@@ -2935,7 +2956,7 @@ out:
             g_conn.block_after_ACK = true;
             uint8_t block[16] = {0x00};
             for (i = 0; i < sectorsCnt; ++i) {
-                uint8_t b = FirstBlockOfSector(i) + NumBlocksPerSector(i) - 1;
+                uint8_t b = mfFirstBlockOfSector(i) + mfNumBlocksPerSector(i) - 1;
                 mfEmlGetMem(block, b, 1);
 
                 if (e_sector[i].foundKey[0])
@@ -3238,8 +3259,8 @@ static int CmdHF14AMfChk(const char *Cmd) {
             if (e_sector[i].foundKey[0] && !e_sector[i].foundKey[1]) {
 
                 uint8_t s = GetSectorFromBlockNo(b);
-                uint8_t sectrail = (FirstBlockOfSector(s) + NumBlocksPerSector(s) - 1);
-                PrintAndLogEx(INFO, "Sector: %u, First block: %u, Last block: %u, Num of blocks: %u", s, FirstBlockOfSector(s), sectrail, NumBlocksPerSector(s));
+                uint8_t sectrail = (mfFirstBlockOfSector(s) + mfNumBlocksPerSector(s) - 1);
+                PrintAndLogEx(INFO, "Sector: %u, First block: %u, Last block: %u, Num of blocks: %u", s, mfFirstBlockOfSector(s), sectrail, mfNumBlocksPerSector(s));
                 PrintAndLogEx(INFO, "Reading sector trailer");
 
                 mf_readblock_t payload;
@@ -3284,7 +3305,7 @@ out:
         g_conn.block_after_ACK = true;
         uint8_t block[16] = {0x00};
         for (int i = 0; i < SectorsCnt; ++i) {
-            uint8_t blockno = FirstBlockOfSector(i) + NumBlocksPerSector(i) - 1;
+            uint8_t blockno = mfFirstBlockOfSector(i) + mfNumBlocksPerSector(i) - 1;
             mfEmlGetMem(block, blockno, 1);
 
             if (e_sector[i].foundKey[0])
@@ -3728,8 +3749,8 @@ static int CmdHF14AMfEGetSc(const char *Cmd) {
     uint8_t sector = (uint8_t)s;
     mf_print_sector_hdr(sector);
 
-    uint8_t blocks = NumBlocksPerSector(sector);
-    uint8_t start = FirstBlockOfSector(sector);
+    uint8_t blocks = mfNumBlocksPerSector(sector);
+    uint8_t start = mfFirstBlockOfSector(sector);
 
     uint8_t data[16] = {0};
     for (int i = 0; i < blocks; i++) {
@@ -4290,8 +4311,8 @@ static int CmdHF14AMfEKeyPrn(const char *Cmd) {
     // download keys from EMUL
     for (int i = 0; i < sectors_cnt; i++) {
 
-        if (mfEmlGetMem(data, FirstBlockOfSector(i) + NumBlocksPerSector(i) - 1, 1) != PM3_SUCCESS) {
-            PrintAndLogEx(WARNING, "error get block %d", FirstBlockOfSector(i) + NumBlocksPerSector(i) - 1);
+        if (mfEmlGetMem(data, mfFirstBlockOfSector(i) + mfNumBlocksPerSector(i) - 1, 1) != PM3_SUCCESS) {
+            PrintAndLogEx(WARNING, "error get block %d", mfFirstBlockOfSector(i) + mfNumBlocksPerSector(i) - 1);
             e_sector[i].foundKey[0] = false;
             e_sector[i].foundKey[1] = false;
         } else {
@@ -6023,7 +6044,7 @@ static int CmdHF14AMfWipe(const char *Cmd) {
     // time to wipe card
     for (uint8_t s = 0; s < num_sectors; s++) {
 
-        for (uint8_t b = 0; b < NumBlocksPerSector(s); b++) {
+        for (uint8_t b = 0; b < mfNumBlocksPerSector(s); b++) {
 
             // Skipp write to manufacture block if not enforced
             if (s == 0 && b == 0 && gen2 == false) {
@@ -6051,9 +6072,9 @@ static int CmdHF14AMfWipe(const char *Cmd) {
                 else
                     memcpy(data, keyB + (s * 6), 6);
 
-                PrintAndLogEx(INFO, "block %3d: %s" NOLF, FirstBlockOfSector(s) + b, sprint_hex(data + 10, MFBLOCK_SIZE));
+                PrintAndLogEx(INFO, "block %3d: %s" NOLF, mfFirstBlockOfSector(s) + b, sprint_hex(data + 10, MFBLOCK_SIZE));
                 clearCommandBuffer();
-                SendCommandMIX(CMD_HF_MIFARE_WRITEBL, FirstBlockOfSector(s) + b, kt, 0, data, sizeof(data));
+                SendCommandMIX(CMD_HF_MIFARE_WRITEBL, mfFirstBlockOfSector(s) + b, kt, 0, data, sizeof(data));
                 PacketResponseNG resp;
                 if (WaitForResponseTimeout(CMD_ACK, &resp, 1500)) {
                     uint8_t isOK  = resp.oldarg[0] & 0xff;
diff --git a/client/src/mifare/mifare4.c b/client/src/mifare/mifare4.c
index c580b9a57..81650c6fa 100644
--- a/client/src/mifare/mifare4.c
+++ b/client/src/mifare/mifare4.c
@@ -502,6 +502,7 @@ uint8_t mfFirstBlockOfSector(uint8_t sectorNo) {
         return 32 * 4 + (sectorNo - 32) * 16;
 }
 
+// assumes blockno is 0-255..
 uint8_t mfSectorTrailer(uint8_t blockNo) {
     if (blockNo < 32 * 4) {
         return (blockNo | 0x03);
@@ -510,10 +511,12 @@ uint8_t mfSectorTrailer(uint8_t blockNo) {
     }
 }
 
+// assumes blockno is 0-255..
 bool mfIsSectorTrailer(uint8_t blockNo) {
     return (blockNo == mfSectorTrailer(blockNo));
 }
 
+// assumes blockno is 0-255..
 uint8_t mfSectorNum(uint8_t blockNo) {
     if (blockNo < 32 * 4)
         return blockNo / 4;
