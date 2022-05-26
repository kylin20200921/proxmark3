commit fb5ba835690f3136f56967bb8e7f422a576c4380
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Apr 2 14:06:16 2022 +0200

    added skeleton for HF NTAG424 category of commands

diff --git a/CHANGELOG.md b/CHANGELOG.md
index 5dfd44d96..93cec3998 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,7 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Added `hf ntag424` - skeleton with SDM (@iceman1001)
  - Updated hf_14a_i2crevive.lua - fixed broken apdus (@Equipter)
  - Use 128 bit slices whenever ARM NEON is available to speed up hardnested (@Doridian)
  - Fixed compilation of mfd_aes_brute on Apple M1 based Macs (@Doridian)
diff --git a/client/CMakeLists.txt b/client/CMakeLists.txt
index 4c5e834a1..c837a3380 100644
--- a/client/CMakeLists.txt
+++ b/client/CMakeLists.txt
@@ -288,6 +288,7 @@ set (TARGET_SOURCES
         ${PM3_ROOT}/client/src/cmdhfmfhard.c
         ${PM3_ROOT}/client/src/cmdhfmfp.c
         ${PM3_ROOT}/client/src/cmdhfmfu.c
+        ${PM3_ROOT}/client/src/cmdhfntag424.c
         ${PM3_ROOT}/client/src/cmdhfseos.c
         ${PM3_ROOT}/client/src/cmdhfst.c
         ${PM3_ROOT}/client/src/cmdhfst25ta.c
diff --git a/client/Makefile b/client/Makefile
index a93ab5409..4941ca97c 100644
--- a/client/Makefile
+++ b/client/Makefile
@@ -566,6 +566,7 @@ SRCS =  mifare/aiddesfire.c \
 		cmdhfmfhard.c \
 		cmdhfmfu.c \
 		cmdhfmfp.c \
+		cmdhfntag424.c \
 		cmdhfseos.c \
 		cmdhfst.c \
 		cmdhfst25ta.c \
diff --git a/client/experimental_lib/CMakeLists.txt b/client/experimental_lib/CMakeLists.txt
index e82f73953..5c64fd135 100644
--- a/client/experimental_lib/CMakeLists.txt
+++ b/client/experimental_lib/CMakeLists.txt
@@ -289,6 +289,7 @@ set (TARGET_SOURCES
         ${PM3_ROOT}/client/src/cmdhfmfhard.c
         ${PM3_ROOT}/client/src/cmdhfmfp.c
         ${PM3_ROOT}/client/src/cmdhfmfu.c
+        ${PM3_ROOT}/client/src/cmdhfntag424.c
         ${PM3_ROOT}/client/src/cmdhfseos.c
         ${PM3_ROOT}/client/src/cmdhfst.c
         ${PM3_ROOT}/client/src/cmdhfst25ta.c
diff --git a/client/src/cmdhf.c b/client/src/cmdhf.c
index 5b0dbba39..cc31d9222 100644
--- a/client/src/cmdhf.c
+++ b/client/src/cmdhf.c
@@ -34,6 +34,7 @@
 #include "cmdhfmfu.h"       // ULTRALIGHT/NTAG etc
 #include "cmdhfmfp.h"       // Mifare Plus
 #include "cmdhfmfdes.h"     // DESFIRE
+#include "cmdhfntag424.h"   // NTAG 424 DNA
 #include "cmdhftopaz.h"     // TOPAZ
 #include "cmdhffelica.h"    // ISO18092 / FeliCa
 #include "cmdhffido.h"      // FIDO authenticators
@@ -429,6 +430,7 @@ static command_t CommandTable[] = {
     {"mfp",         CmdHFMFP,         AlwaysAvailable, "{ MIFARE Plus RFIDs...                }"},
     {"mfu",         CmdHFMFUltra,     AlwaysAvailable, "{ MIFARE Ultralight RFIDs...          }"},
     {"mfdes",       CmdHFMFDes,       AlwaysAvailable, "{ MIFARE Desfire RFIDs...             }"},
+    {"ntag424",     CmdHF_ntag424,    AlwaysAvailable, "{ NXP NTAG 4242 DNA RFIDs...          }"},    
     {"seos",        CmdHFSeos,        AlwaysAvailable, "{ SEOS RFIDs...                       }"},
     {"st25ta",      CmdHFST25TA,      AlwaysAvailable, "{ ST25TA RFIDs...                     }"},
     {"thinfilm",    CmdHFThinfilm,    AlwaysAvailable, "{ Thinfilm RFIDs...                   }"},
diff --git a/client/src/cmdhfntag424.c b/client/src/cmdhfntag424.c
new file mode 100644
index 000000000..af475b1be
--- /dev/null
+++ b/client/src/cmdhfntag424.c
@@ -0,0 +1,226 @@
+//-----------------------------------------------------------------------------
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
+//
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
+//-----------------------------------------------------------------------------
+// High frequency ISO14443A / NTAG424 DNA Commands
+//-----------------------------------------------------------------------------
+
+#include "cmdhfntag424.h"
+#include <ctype.h>
+#include "cmdparser.h"
+#include "commonutil.h"
+#include "protocols.h"
+#include "cliparser.h"
+#include "cmdmain.h"
+#include "fileutils.h"          // saveFile
+#include "crypto/libpcrypto.h"  // aes_decode
+#include "cmac.h"
+
+
+#define NTAG424_MAX_BYTES  412
+
+static int CmdHelp(const char *Cmd);
+
+static int CmdHF_ntag424_view(const char *Cmd) {
+
+    CLIParserContext *ctx;
+    CLIParserInit(&ctx, "hf ntag424 view",
+                  "Print a NTAG 424 DNA dump file (bin/eml/json)",
+                  "hf ntag424 view -f hf-ntag424-01020304-dump.bin"
+                 );
+    void *argtable[] = {
+        arg_param_begin,
+        arg_str1("f", "file", "<fn>", "Filename of dump"),
+        arg_lit0("v", "verbose", "Verbose output"),
+        arg_param_end
+    };
+    CLIExecWithReturn(ctx, Cmd, argtable, false);
+    int fnlen = 0;
+    char filename[FILE_PATH_SIZE];
+    CLIParamStrToBuf(arg_get_str(ctx, 1), (uint8_t *)filename, FILE_PATH_SIZE, &fnlen);
+    bool verbose = arg_get_lit(ctx, 2);
+    CLIParserFree(ctx);
+
+    // reserve memory
+    uint8_t *dump = NULL;
+    size_t bytes_read = 0;
+    int res = 0;
+    DumpFileType_t dftype = getfiletype(filename);
+    switch (dftype) {
+        case BIN: {
+            res = loadFile_safe(filename, ".bin", (void **)&dump, &bytes_read);
+            break;
+        }
+        case JSON: {
+            dump = calloc(NTAG424_MAX_BYTES, sizeof(uint8_t));
+            if (dump == NULL) {
+                PrintAndLogEx(WARNING, "Fail, cannot allocate memory");
+                return PM3_EMALLOC;
+            }
+            res = loadFileJSON(filename, (void *)dump, MFU_MAX_BYTES + MFU_DUMP_PREFIX_LENGTH, &bytes_read, NULL);
+            break;
+        }
+        case EML:
+            res = loadFileEML_safe(filename, (void **)&dump, &bytes_read);
+            break;
+        case DICTIONARY: {
+            PrintAndLogEx(ERR, "Error: Only BIN/EML/JSON formats allowed");
+            free(dump);
+            return PM3_EINVARG;
+        }
+    }
+
+    if (res != PM3_SUCCESS) {
+        PrintAndLogEx(FAILED, "File: " _YELLOW_("%s") ": not found or locked.", filename);
+        free(dump);
+        return PM3_EFILE;
+    }
+
+    if (verbose) {
+        PrintAndLogEx(INFO, "File: " _YELLOW_("%s"), filename);
+        PrintAndLogEx(INFO, "File size %zu bytes", bytes_read);
+    }
+
+    free(dump);
+    return PM3_SUCCESS;
+}
+
+
+typedef struct sdm_picc_s {
+    uint8_t tag;
+    uint8_t uid[7];
+    uint8_t cnt[3];
+    uint32_t cnt_int;
+} sdm_picc_t;
+
+static int sdm_generator(void) {
+
+    // NXP Secure Dynamic Messaging (SDM)  with Secure Unique NFC message (SUN)
+    // Where do they come up with these names?
+    //
+    // ref:
+    // https://www.nxp.com/docs/en/application-note/AN12196.pdf
+
+    // SMD / CMAC
+    uint8_t iv[16] = {0};
+    uint8_t aeskey[16]  = {0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00};
+    // uint8_t enc_txt[16] = {0xEF, 0x96, 0x3F, 0xF7, 0x82, 0x86, 0x58, 0xA5, 0x99, 0xF3, 0x04, 0x15, 0x10, 0x67, 0x1E, 0x88};
+    uint8_t enc_txt[16] = {0xe6, 0x45, 0xb6, 0x15, 0x4e, 0x8f, 0x32, 0x7d, 0xfb, 0xab, 0x93, 0x4d, 0x4c, 0x66, 0x46, 0x14};
+    uint8_t dec_txt[16] = {0};
+
+    aes_decode(iv, aeskey, enc_txt, dec_txt, sizeof(enc_txt));
+
+    PrintAndLogEx(INFO, "Ntag424 SUN message validation and encryption");
+    PrintAndLogEx(INFO, "Enc text... %s", sprint_hex(enc_txt, sizeof(enc_txt)));
+    PrintAndLogEx(INFO, "Dec text... %s", sprint_hex(dec_txt, sizeof(dec_txt)));
+
+    sdm_picc_t o = {0};
+    o.tag  = dec_txt[0];
+    memcpy(o.uid, dec_txt + 1, 8);
+    memcpy(o.cnt, dec_txt + 8, 3);
+    o.cnt_int = MemLeToUint3byte(o.cnt);
+
+    PrintAndLogEx(INFO, "Decypted text");
+    PrintAndLogEx(INFO, "  Tag........... 0x%02X", o.tag);
+    PrintAndLogEx(INFO, "  UID........... %s", sprint_hex(o.uid, sizeof(o.uid)));
+    PrintAndLogEx(INFO, "  Count bytes... %s", sprint_hex(o.cnt, sizeof(o.cnt)));
+    PrintAndLogEx(INFO, "  Count value... 0x%X ( %u )", o.cnt_int, o.cnt_int);
+
+    // SV2 as per NXP DS465430 (NT4H2421Gx Data sheet)
+    uint8_t sv2data[16] = {0x3C, 0xC3, 0x00, 0x01, 0x00, 0x80};
+
+    memcpy(sv2data + 6, o.uid, sizeof(o.uid));
+    memcpy(sv2data + 6 + sizeof(o.uid), o.cnt, sizeof(o.cnt));
+
+    uint8_t cmackey[16] = {0};
+    mbedtls_aes_cmac_prf_128(aeskey, 16, sv2data, sizeof(sv2data), cmackey);
+
+    uint8_t zero[16] = {0};
+    uint8_t full_cmac[16] = {0};
+    mbedtls_aes_cmac_prf_128(cmackey, 16, zero, 0, full_cmac);
+
+    uint8_t cmac[8] = {0};
+    for (int i = 0, j = 1; i < 8; ++i, j += 2) {
+        cmac[i] = full_cmac[j];
+    }
+
+    //uint8_t veri[] = {0x94, 0xee, 0xd9, 0xee, 0x65, 0x33, 0x70, 0x86};
+    uint8_t veri[] = {0x8b, 0xa1, 0xfb, 0x47, 0x0d, 0x63, 0x39, 0xe8 };
+    uint8_t is_ok = (memcmp(cmac, veri, 8) == 0);
+
+    PrintAndLogEx(INFO, "SDM cmac... %s ( %s )",
+                  sprint_hex(cmac, sizeof(cmac)),
+                  (is_ok) ? _GREEN_("ok") : _RED_("fail")
+                 );
+
+    return PM3_SUCCESS;
+}
+static int CmdHF_ntag424_sdm(const char *Cmd) {
+
+    CLIParserContext *ctx;
+    CLIParserInit(&ctx, "hf ntag424 sdm",
+                  "Validate a SDM message",
+                  "hf ntag424 sdm"
+                 );
+    void *argtable[] = {
+        arg_param_begin,
+        arg_param_end
+    };
+    CLIExecWithReturn(ctx, Cmd, argtable, false);
+    CLIParserFree(ctx);
+
+    return sdm_generator();
+}
+
+static int CmdHF_ntag424_info(const char *Cmd) {
+
+    CLIParserContext *ctx;
+    CLIParserInit(&ctx, "hf ntag424 info",
+                  "Get info about NXP NTAG424 DNA Family styled tag.",
+                  "hf ntag424 info"
+                 );
+    void *argtable[] = {
+        arg_param_begin,
+        arg_param_end
+    };
+    CLIExecWithReturn(ctx, Cmd, argtable, false);
+    CLIParserFree(ctx);
+
+    PrintAndLogEx(INFO, "not implemented yet");
+    PrintAndLogEx(INFO, "Feel free to contribute!");
+    return PM3_SUCCESS;
+}
+
+//------------------------------------
+// Menu Stuff
+//------------------------------------
+static command_t CommandTable[] = {
+    {"help",     CmdHelp,                AlwaysAvailable,  "This help"},
+    {"-----------", CmdHelp,             IfPm3Iso14443a,   "----------------------- " _CYAN_("operations") " -----------------------"},
+    {"info",     CmdHF_ntag424_info,     IfPm3Iso14443a,   "Tag information"},
+    {"ndefread", CmdHF_ntag424_sdm,      IfPm3Iso14443a,   "Prints NDEF records from card"},
+    {"view",     CmdHF_ntag424_view,     AlwaysAvailable,  "Display content from tag dump file"},
+    {NULL, NULL, NULL, NULL}
+};
+
+static int CmdHelp(const char *Cmd) {
+    (void)Cmd; // Cmd is not used so far
+    CmdsHelp(CommandTable);
+    return PM3_SUCCESS;
+}
+
+int CmdHF_ntag424(const char *Cmd) {
+    clearCommandBuffer();
+    return CmdsParse(CommandTable, Cmd);
+}
diff --git a/client/src/cmdhfntag424.h b/client/src/cmdhfntag424.h
new file mode 100644
index 000000000..b2da31c8c
--- /dev/null
+++ b/client/src/cmdhfntag424.h
@@ -0,0 +1,27 @@
+//-----------------------------------------------------------------------------
+// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
+//
+// This program is free software: you can redistribute it and/or modify
+// it under the terms of the GNU General Public License as published by
+// the Free Software Foundation, either version 3 of the License, or
+// (at your option) any later version.
+//
+// This program is distributed in the hope that it will be useful,
+// but WITHOUT ANY WARRANTY; without even the implied warranty of
+// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
+// GNU General Public License for more details.
+//
+// See LICENSE.txt for the text of the license.
+//-----------------------------------------------------------------------------
+// High frequency ISO14443A / NTAG424 DNA Commands
+//-----------------------------------------------------------------------------
+
+#ifndef CMDHFNTAG424_H__
+#define CMDHFNTAG424_H__
+
+
+#include "common.h"
+
+int CmdHF_ntag424(const char *Cmd);
+
+#endif
