commit 57d039743664aa6e7bcbeb9024c5e40b444265dc
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jan 30 21:18:59 2022 +0100

    added trace extract,  it extracts auth challengens in MFU-C/ICLASS/DESFIRE and prints it.

diff --git a/CHANGELOG.md b/CHANGELOG.md
index 69299a989..a9c40d876 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,7 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Added `trace extract` - extract authentication parts from trace (@iceman1001)
  - Added luascript `hf_mf_ultimatecard.lua` - Script for Ultimate Magic Card (GEN4) (@startrk1995)
  - Added new tool `brute_key` - MIFARE DESFire Telenot access AES recovery (@x41sec)
  - Fixed `hf mfu dump -k` - insert PWD in dump (@doegox)
diff --git a/client/src/cmdtrace.c b/client/src/cmdtrace.c
index d44552bfa..52aa1f62c 100644
--- a/client/src/cmdtrace.c
+++ b/client/src/cmdtrace.c
@@ -79,6 +79,299 @@ static bool merge_topaz_reader_frames(uint32_t timestamp, uint32_t *duration, ui
 
     return true;
 }
+static uint8_t calc_pos(uint8_t *d){
+    // PCB [CID] [NAD] [INF] CRC CRC
+    uint8_t pos = 1;
+    if ((d[0] & 0x08) == 0x08)  // cid byte following
+        pos++;
+
+    if ((d[0] & 0x04) == 0x04)  // nad byte following
+        pos++;
+
+    return pos;    
+}
+
+static uint8_t extract_uid[10] = {0};
+static uint8_t extract_uidlen = 0;
+static uint8_t extract_epurse[8] = {0};
+
+#define SKIP_TO_NEXT(a)  (TRACELOG_HDR_LEN + (a)->data_len + TRACELOG_PARITY_LEN((a)))
+
+static uint16_t extractChallenges(uint16_t tracepos, uint16_t traceLen, uint8_t *trace) {
+
+    // sanity check 
+    if (is_last_record(tracepos, traceLen)) {
+        return traceLen;
+    }
+
+    tracelog_hdr_t *hdr = (tracelog_hdr_t *)(trace + tracepos);
+    uint16_t data_len = hdr->data_len;
+    uint8_t *frame = hdr->frame;
+
+    // sanity check tracking position is less then available trace size
+    if (tracepos + TRACELOG_HDR_LEN + data_len + TRACELOG_PARITY_LEN(hdr) > traceLen) {
+        PrintAndLogEx(DEBUG, "trace pos offset %"PRIu64 " larger than reported tracelen %u",
+            tracepos + TRACELOG_HDR_LEN + data_len + TRACELOG_PARITY_LEN(hdr),
+            traceLen
+            );
+        return traceLen;
+    }
+
+    // set trace position
+    tracepos += SKIP_TO_NEXT(hdr);
+
+    // sanity check data frame length
+    if (data_len == 0) {
+        return tracepos;
+    }
+
+    // extract MFC
+    switch (frame[0]) {
+        case MIFARE_AUTH_KEYA: {
+            if (data_len > 3) {
+            }
+            break;
+        }
+        case MIFARE_AUTH_KEYB: {
+            if (data_len > 3) {
+            }
+            break;
+        }
+    }
+
+    // extract MFU-C       
+    switch (frame[0]) {
+        case MIFARE_ULC_AUTH_1: {
+            if (data_len != 4) {
+                break;
+            }
+
+            // time to skip to next
+            tracelog_hdr_t *next_hdr = (tracelog_hdr_t *)(trace + tracepos);
+            tracepos += SKIP_TO_NEXT(next_hdr);
+            if (next_hdr->data_len != 11) {
+                break;
+            }
+
+            if (next_hdr->frame[0] != MIFARE_ULC_AUTH_2) {
+                break;                        
+            }
+
+            PrintAndLogEx(INFO, "MFU-C AUTH");
+            PrintAndLogEx(INFO, "3DES %s " NOLF, sprint_hex_inrow(next_hdr->frame + 1, 8));
+
+            next_hdr = (tracelog_hdr_t *)(trace + tracepos);
+            tracepos += SKIP_TO_NEXT(next_hdr);
+
+            if (next_hdr->frame[0] == MIFARE_ULC_AUTH_2 && next_hdr->data_len == 19) {
+                PrintAndLogEx(NORMAL, "%s", sprint_hex_inrow(next_hdr->frame + 1, 16));
+            }
+            
+            return tracepos;
+        }
+    }
+
+    // extract iCLASS
+    // --csn 9655a400f8ff12e0 --epurse f0ffffffffffffff --macs 0000000089cb984b
+    
+    if (hdr->isResponse == false)  {
+       
+        uint8_t c = frame[0] & 0x0F;
+        switch (c) {
+            case ICLASS_CMD_SELECT: {
+
+                tracelog_hdr_t *next_hdr = (tracelog_hdr_t *)(trace + tracepos);
+                tracepos += SKIP_TO_NEXT(next_hdr);
+                if (next_hdr->data_len != 10) {
+                    break;
+                }
+                memcpy(extract_uid, next_hdr->frame, 8);
+                extract_uidlen = 8;
+                break;
+            }
+            case ICLASS_CMD_READCHECK: {
+
+                // get epurse
+                if (frame[1] == 2 && data_len == 2) {
+                    tracelog_hdr_t *next_hdr = (tracelog_hdr_t *)(trace + tracepos);
+                    tracepos += SKIP_TO_NEXT(next_hdr);
+                    if (next_hdr->data_len < 8) {
+                        break;
+                    }
+                    memcpy(extract_epurse, next_hdr->frame, 8);
+                }
+                break;
+            }
+            case ICLASS_CMD_CHECK: {
+                // get macs
+                if (data_len == 9) {
+                    if (extract_uidlen == 8) {
+                        PrintAndLogEx(INFO, "hf iclass lookup --csn %s " NOLF, sprint_hex_inrow(extract_uid, extract_uidlen));
+                        PrintAndLogEx(NORMAL, "--epurse %s " NOLF, sprint_hex_inrow(extract_epurse, 8));
+                        PrintAndLogEx(NORMAL, "--macs %s " NOLF, sprint_hex_inrow(frame + 1, 8) );
+                        PrintAndLogEx(NORMAL, "-f iclass_default_keys.dic");
+                        return tracepos;
+                    }
+                }
+                break;
+            }
+        }
+    }
+
+    // extract UID
+    switch (frame[0]) {
+        case ISO14443A_CMD_ANTICOLL_OR_SELECT: {
+            // 93 20 = Anticollision (usage: 9320 - answer: 4bytes UID+1byte UID-bytes-xor)
+            // 93 50 = Bit oriented anti-collision (usage: 9350+ up to 5bytes, 9350 answer - up to 5bytes UID+BCC)
+            // 93 70 = Select (usage: 9370+5bytes 9370 answer - answer: 1byte SAK)
+            if (frame[1] == 0x70) {
+                if (frame[2] == 0x88) {
+                    memcpy(extract_uid, frame + 3, 3);
+                    extract_uidlen = 3;
+                } else {
+                    memcpy(extract_uid, frame + 2, 4);
+                    extract_uidlen = 4;
+                    PrintAndLogEx(INFO, "UID... " _YELLOW_("%s"), sprint_hex_inrow(extract_uid, extract_uidlen));
+                }
+            }
+            break;
+        }
+        case ISO14443A_CMD_ANTICOLL_OR_SELECT_2: {
+            // 95 20 = Anticollision of cascade level2
+            // 95 50 = Bit oriented anti-collision level2
+            // 95 70 = Select of cascade level2
+            if (frame[1] == 0x70) {
+                if (frame[2] == 0x88) {
+                    memcpy(extract_uid + extract_uidlen, frame + 3, 3);
+                    extract_uidlen += 3;
+                } else {
+                    memcpy(extract_uid + extract_uidlen, frame + 2, 4);
+                    extract_uidlen += 4;
+                    PrintAndLogEx(INFO, "UID... " _YELLOW_("%s"), sprint_hex_inrow(extract_uid, extract_uidlen));
+                }   
+            }
+            break;
+        }
+        case ISO14443A_CMD_ANTICOLL_OR_SELECT_3: {
+            // 97 20 = Anticollision of cascade level3
+            // 97 50 = Bit oriented anti-collision level3
+            // 97 70 = Select of cascade level3
+            if (frame[1] == 0x70) {
+                memcpy(extract_uid + extract_uidlen, frame + 2, 4);
+                extract_uidlen += 4;
+                PrintAndLogEx(INFO, "UID... " _YELLOW_("%s"), sprint_hex_inrow(extract_uid, extract_uidlen));
+            }
+            break;
+        }
+    }
+
+    // extract DESFIRE
+    if ((frame[0] & 0xC0) != 0x00) {
+        return tracepos;
+    }
+
+    // PCB [CID] [NAD] [INF] CRC CRC
+    uint8_t pos = calc_pos(frame);
+    uint8_t long_jmp = (data_len > 6) ? 4 : 1;
+
+    for (uint8_t i = 0; i < 2; i++, pos++) {
+
+        switch (frame[pos]) {
+
+            case MFDES_AUTHENTICATE: {
+                // Assume wrapped or unwrapped 
+                PrintAndLogEx(INFO, "AUTH NATIVE (keyNo %d)", frame[pos + long_jmp]);
+                
+                if (hdr->isResponse == false && next_record_is_response(tracepos, trace)) {
+
+                    tracelog_hdr_t *next_hdr = (tracelog_hdr_t *)(trace + tracepos);
+                    tracepos += TRACELOG_HDR_LEN + next_hdr->data_len + TRACELOG_PARITY_LEN(next_hdr);
+                    if (next_hdr->data_len < 7) {
+                        break;
+                    }
+
+                    PrintAndLogEx(INFO, "DES 1499999999 %s " NOLF, sprint_hex_inrow(next_hdr->frame + 1, 8));
+
+                    next_hdr = (tracelog_hdr_t *)(trace + tracepos);
+                    tracepos += TRACELOG_HDR_LEN + next_hdr->data_len + TRACELOG_PARITY_LEN(next_hdr);
+
+                    if (next_hdr->frame[pos] == MFDES_ADDITIONAL_FRAME) {
+                        PrintAndLogEx(NORMAL, "%s", sprint_hex_inrow(next_hdr->frame + pos + long_jmp, 16));
+                    }
+                    return tracepos;
+                }    
+                break;  // AUTHENTICATE_NATIVE
+            }
+            case MFDES_AUTHENTICATE_ISO: {
+
+                // Assume wrapped or unwrapped 
+                PrintAndLogEx(INFO, "AUTH ISO (keyNo %d)", frame[pos + long_jmp]);
+                if (hdr->isResponse == false && next_record_is_response(tracepos, trace)) {
+
+                    tracelog_hdr_t *next_hdr = (tracelog_hdr_t *)(trace + tracepos);
+                    tracepos += TRACELOG_HDR_LEN + next_hdr->data_len + TRACELOG_PARITY_LEN(next_hdr);
+                    if (next_hdr->data_len < 7) {
+                        break;
+                    }
+
+                    uint8_t tdea = 8;
+                    if (next_hdr->data_len > 20) {
+                        tdea = 16;
+                        PrintAndLogEx(INFO, "3TDEA 1499999999 %s " NOLF, sprint_hex_inrow(next_hdr->frame + 1, tdea));
+                    } else {
+                        PrintAndLogEx(INFO, "2TDEA 1499999999 %s " NOLF, sprint_hex_inrow(next_hdr->frame + 1, tdea));
+                    }
+
+                    next_hdr = (tracelog_hdr_t *)(trace + tracepos);
+                    tracepos += TRACELOG_HDR_LEN + next_hdr->data_len + TRACELOG_PARITY_LEN(next_hdr);
+
+                    if (next_hdr->frame[pos] == MFDES_ADDITIONAL_FRAME) {
+                        PrintAndLogEx(NORMAL, "%s", sprint_hex_inrow(next_hdr->frame + pos + long_jmp, (tdea<<1)));
+                    }
+                    return tracepos;
+                }
+
+                break;  // AUTHENTICATE_STANDARD
+            }
+            case MFDES_AUTHENTICATE_AES: {
+                // Assume wrapped or unwrapped 
+                PrintAndLogEx(INFO, "AUTH AES (keyNo %d)", frame[pos + long_jmp]);
+                if (hdr->isResponse == false && next_record_is_response(tracepos, trace)) {
+
+                    tracelog_hdr_t *next_hdr = (tracelog_hdr_t *)(trace + tracepos);
+                    tracepos += TRACELOG_HDR_LEN + next_hdr->data_len + TRACELOG_PARITY_LEN(next_hdr);
+                    if (next_hdr->data_len < 7) {
+                        break;
+                    }
+                    PrintAndLogEx(INFO, "AES 1499999999 %s " NOLF, sprint_hex_inrow(next_hdr->frame + 1, 8));
+
+                    next_hdr = (tracelog_hdr_t *)(trace + tracepos);
+                    tracepos += TRACELOG_HDR_LEN + next_hdr->data_len + TRACELOG_PARITY_LEN(next_hdr);
+
+                    if (next_hdr->frame[pos] == MFDES_ADDITIONAL_FRAME) {
+                        PrintAndLogEx(NORMAL, "%s", sprint_hex_inrow(next_hdr->frame + pos + long_jmp, 16));
+                    }
+                    return tracepos;
+                }
+                break;
+            }
+            case MFDES_AUTHENTICATE_EV2F: {
+                if (hdr->isResponse == false) {
+                    PrintAndLogEx(INFO, "AUTH EV2 First");
+                }
+                break;
+            }
+            case MFDES_AUTHENTICATE_EV2NF: {
+                if (hdr->isResponse == false) {
+                    PrintAndLogEx(INFO, "AUTH EV2 Non First");
+                }
+                break;
+            }
+        }
+    } 
+
+    return tracepos;
+}
 
 static uint16_t printHexLine(uint16_t tracepos, uint16_t traceLen, uint8_t *trace, uint8_t protocol) {
     // sanity check
@@ -150,8 +443,6 @@ static uint16_t printTraceLine(uint16_t tracepos, uint16_t traceLen, uint8_t *tr
     }
 
     uint32_t end_of_transmission_timestamp = 0;
-    uint32_t duration;
-    uint16_t data_len;
     uint8_t topaz_reader_command[9];
     char explanation[40] = {0};
     uint8_t mfData[32] = {0};
@@ -159,11 +450,14 @@ static uint16_t printTraceLine(uint16_t tracepos, uint16_t traceLen, uint8_t *tr
     tracelog_hdr_t *first_hdr = (tracelog_hdr_t *)(trace);
     tracelog_hdr_t *hdr = (tracelog_hdr_t *)(trace + tracepos);
 
-    duration = hdr->duration;
-    data_len = hdr->data_len;
+    uint32_t duration = hdr->duration;
+    uint16_t data_len = hdr->data_len;
 
     if (tracepos + TRACELOG_HDR_LEN + data_len + TRACELOG_PARITY_LEN(hdr) > traceLen) {
-        PrintAndLogEx(DEBUG, "trace pos offset %"PRIu64 " larger than reported tracelen %u", tracepos + TRACELOG_HDR_LEN + data_len + TRACELOG_PARITY_LEN(hdr), traceLen);
+        PrintAndLogEx(DEBUG, "trace pos offset %"PRIu64 " larger than reported tracelen %u",
+            tracepos + TRACELOG_HDR_LEN + data_len + TRACELOG_PARITY_LEN(hdr),
+            traceLen
+            );
         return traceLen;
     }
 
@@ -591,6 +885,50 @@ static int SanityOfflineCheck( bool useTraceBuffer ){
 }
 */
 
+static int CmdTraceExtract(const char *Cmd) {
+    CLIParserContext *ctx;
+    CLIParserInit(&ctx, "trace extract",
+                  "Extracts protocol authentication challenges from trace buffer\n",
+                  "trace extract\n"
+                  "trace extract -1\n"
+                 );
+
+    void *argtable[] = {
+        arg_param_begin,
+        arg_lit0("1", "buffer", "use data from trace buffer"),        
+        arg_param_end
+    };
+    CLIExecWithReturn(ctx, Cmd, argtable, true);
+    bool use_buffer = arg_get_lit(ctx, 1);
+    CLIParserFree(ctx);
+
+    clearCommandBuffer();
+
+    if (use_buffer == false) {
+        download_trace();
+    } else if (gs_traceLen == 0) {
+        PrintAndLogEx(FAILED, "You requested a trace list in offline mode but there is no trace.");
+        PrintAndLogEx(FAILED, "Consider using " _YELLOW_("`trace load`") " or removing parameter " _YELLOW_("`-1`"));
+        return PM3_EINVARG;
+    }
+
+    PrintAndLogEx(SUCCESS, "Recorded activity (trace len = " _YELLOW_("%lu") " bytes)", gs_traceLen);
+    if (gs_traceLen == 0) {
+        return PM3_SUCCESS;
+    }
+
+    uint16_t tracepos = 0;
+
+    while (tracepos < gs_traceLen) {
+        tracepos = extractChallenges(tracepos, gs_traceLen, gs_trace);
+
+        if (kbd_enter_pressed())
+            break;
+    }
+
+    return PM3_SUCCESS;
+}
+
 static int CmdTraceLoad(const char *Cmd) {
 
     CLIParserContext *ctx;
@@ -800,7 +1138,8 @@ int CmdTraceList(const char *Cmd) {
     if (use_buffer == false) {
         download_trace();
     } else if (gs_traceLen == 0) {
-        PrintAndLogEx(FAILED, "You requested a trace list in offline mode but there is no trace, consider using 'trace load' or removing parameter '-1'");
+        PrintAndLogEx(FAILED, "You requested a trace list in offline mode but there is no trace.");
+        PrintAndLogEx(FAILED, "Consider using " _YELLOW_("`trace load`") " or removing parameter " _YELLOW_("`-1`"));
         return PM3_EINVARG;
     }
 
@@ -939,6 +1278,7 @@ int CmdTraceList(const char *Cmd) {
 
 static command_t CommandTable[] = {
     {"help",    CmdHelp,          AlwaysAvailable, "This help"},
+    {"extract", CmdTraceExtract,  AlwaysAvailable, "Extract authentication challenges found in trace"},
     {"list",    CmdTraceList,     AlwaysAvailable, "List protocol data in trace buffer"},
     {"load",    CmdTraceLoad,     AlwaysAvailable, "Load trace from file"},
     {"save",    CmdTraceSave,     AlwaysAvailable, "Save trace buffer to file"},
