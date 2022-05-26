commit 1e35567e153fd47c205eafa2666690cde19368cb
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Feb 6 20:56:33 2022 +0100

    extra ev2 challenges

diff --git a/client/src/cmdtrace.c b/client/src/cmdtrace.c
index dfe7146ea..0ddd6cf71 100644
--- a/client/src/cmdtrace.c
+++ b/client/src/cmdtrace.c
@@ -97,6 +97,27 @@ static uint8_t extract_epurse[8] = {0};
 
 #define SKIP_TO_NEXT(a)  (TRACELOG_HDR_LEN + (a)->data_len + TRACELOG_PARITY_LEN((a)))
 
+static uint16_t extractChall_ev2(uint16_t tracepos, uint8_t *trace, uint8_t cmdpos, uint8_t long_jmp) {
+    tracelog_hdr_t *next_hdr = (tracelog_hdr_t *)(trace + tracepos);
+    if (next_hdr->data_len != 21) {
+        return 0;
+    }
+
+    tracepos += TRACELOG_HDR_LEN + next_hdr->data_len + TRACELOG_PARITY_LEN(next_hdr);
+
+    PrintAndLogEx(INFO, "1499999999 %s " NOLF, sprint_hex_inrow(next_hdr->frame + 1, 16));
+
+    next_hdr = (tracelog_hdr_t *)(trace + tracepos);
+    tracepos += TRACELOG_HDR_LEN + next_hdr->data_len + TRACELOG_PARITY_LEN(next_hdr);
+
+    if (next_hdr->frame[cmdpos] == MFDES_ADDITIONAL_FRAME) {
+        PrintAndLogEx(NORMAL, "%s", sprint_hex_inrow(next_hdr->frame + cmdpos + long_jmp, 32));
+    } else {
+        PrintAndLogEx(NORMAL, "");
+    }
+    return tracepos;
+}
+
 static uint16_t extractChallenges(uint16_t tracepos, uint16_t traceLen, uint8_t *trace) {
 
     // sanity check
@@ -270,6 +291,10 @@ static uint16_t extractChallenges(uint16_t tracepos, uint16_t traceLen, uint8_t
         return tracepos;
     }
 
+    if (hdr->isResponse) {
+        return tracepos;
+    }
+
     // PCB [CID] [NAD] [INF] CRC CRC
     uint8_t pos = calc_pos(frame);
     uint8_t long_jmp = (data_len > 6) ? 4 : 1;
@@ -279,93 +304,103 @@ static uint16_t extractChallenges(uint16_t tracepos, uint16_t traceLen, uint8_t
         switch (frame[pos]) {
 
             case MFDES_AUTHENTICATE: {
+
                 // Assume wrapped or unwrapped
                 PrintAndLogEx(INFO, "AUTH NATIVE (keyNo %d)", frame[pos + long_jmp]);
+                if (next_record_is_response(tracepos, trace) == false) {
+                    break;
+                }
 
-                if (hdr->isResponse == false && next_record_is_response(tracepos, trace)) {
-
-                    tracelog_hdr_t *next_hdr = (tracelog_hdr_t *)(trace + tracepos);
-                    tracepos += TRACELOG_HDR_LEN + next_hdr->data_len + TRACELOG_PARITY_LEN(next_hdr);
-                    if (next_hdr->data_len < 7) {
-                        break;
-                    }
+                tracelog_hdr_t *next_hdr = (tracelog_hdr_t *)(trace + tracepos);
+                if (next_hdr->data_len < 7) {
+                    break;
+                }
+                tracepos += TRACELOG_HDR_LEN + next_hdr->data_len + TRACELOG_PARITY_LEN(next_hdr);
 
-                    PrintAndLogEx(INFO, "DES 1499999999 %s " NOLF, sprint_hex_inrow(next_hdr->frame + 1, 8));
+                PrintAndLogEx(INFO, "DES 1499999999 %s " NOLF, sprint_hex_inrow(next_hdr->frame + 1, 8));
 
-                    next_hdr = (tracelog_hdr_t *)(trace + tracepos);
-                    tracepos += TRACELOG_HDR_LEN + next_hdr->data_len + TRACELOG_PARITY_LEN(next_hdr);
+                next_hdr = (tracelog_hdr_t *)(trace + tracepos);
+                tracepos += TRACELOG_HDR_LEN + next_hdr->data_len + TRACELOG_PARITY_LEN(next_hdr);
 
-                    if (next_hdr->frame[pos] == MFDES_ADDITIONAL_FRAME) {
-                        PrintAndLogEx(NORMAL, "%s", sprint_hex_inrow(next_hdr->frame + pos + long_jmp, 16));
-                    }
-                    return tracepos;
+                if (next_hdr->frame[pos] == MFDES_ADDITIONAL_FRAME) {
+                    PrintAndLogEx(NORMAL, "%s", sprint_hex_inrow(next_hdr->frame + pos + long_jmp, 16));
+                } else {
+                    PrintAndLogEx(NORMAL, "");
                 }
-                break;  // AUTHENTICATE_NATIVE
+                return tracepos;   // AUTHENTICATE_NATIVE
             }
             case MFDES_AUTHENTICATE_ISO: {
-
                 // Assume wrapped or unwrapped
                 PrintAndLogEx(INFO, "AUTH ISO (keyNo %d)", frame[pos + long_jmp]);
-                if (hdr->isResponse == false && next_record_is_response(tracepos, trace)) {
+                if (next_record_is_response(tracepos, trace) == false) {
+                    break;
+                }
 
-                    tracelog_hdr_t *next_hdr = (tracelog_hdr_t *)(trace + tracepos);
-                    tracepos += TRACELOG_HDR_LEN + next_hdr->data_len + TRACELOG_PARITY_LEN(next_hdr);
-                    if (next_hdr->data_len < 7) {
-                        break;
-                    }
+                tracelog_hdr_t *next_hdr = (tracelog_hdr_t *)(trace + tracepos);
+                tracepos += TRACELOG_HDR_LEN + next_hdr->data_len + TRACELOG_PARITY_LEN(next_hdr);
+                if (next_hdr->data_len < 7) {
+                    break;
+                }
 
-                    uint8_t tdea = 8;
-                    if (next_hdr->data_len > 20) {
-                        tdea = 16;
-                        PrintAndLogEx(INFO, "3TDEA 1499999999 %s " NOLF, sprint_hex_inrow(next_hdr->frame + 1, tdea));
-                    } else {
-                        PrintAndLogEx(INFO, "2TDEA 1499999999 %s " NOLF, sprint_hex_inrow(next_hdr->frame + 1, tdea));
-                    }
+                uint8_t tdea = 8;
+                if (next_hdr->data_len > 20) {
+                    tdea = 16;
+                    PrintAndLogEx(INFO, "3TDEA 1499999999 %s " NOLF, sprint_hex_inrow(next_hdr->frame + 1, tdea));
+                } else {
+                    PrintAndLogEx(INFO, "2TDEA 1499999999 %s " NOLF, sprint_hex_inrow(next_hdr->frame + 1, tdea));
+                }
 
-                    next_hdr = (tracelog_hdr_t *)(trace + tracepos);
-                    tracepos += TRACELOG_HDR_LEN + next_hdr->data_len + TRACELOG_PARITY_LEN(next_hdr);
+                next_hdr = (tracelog_hdr_t *)(trace + tracepos);
+                tracepos += TRACELOG_HDR_LEN + next_hdr->data_len + TRACELOG_PARITY_LEN(next_hdr);
 
-                    if (next_hdr->frame[pos] == MFDES_ADDITIONAL_FRAME) {
-                        PrintAndLogEx(NORMAL, "%s", sprint_hex_inrow(next_hdr->frame + pos + long_jmp, (tdea << 1)));
-                    }
-                    return tracepos;
+                if (next_hdr->frame[pos] == MFDES_ADDITIONAL_FRAME) {
+                    PrintAndLogEx(NORMAL, "%s", sprint_hex_inrow(next_hdr->frame + pos + long_jmp, (tdea << 1)));
+                } else {
+                    PrintAndLogEx(NORMAL, "");
                 }
-
-                break;  // AUTHENTICATE_STANDARD
+                return tracepos;  // AUTHENTICATE_STANDARD
             }
             case MFDES_AUTHENTICATE_AES: {
                 // Assume wrapped or unwrapped
                 PrintAndLogEx(INFO, "AUTH AES (keyNo %d)", frame[pos + long_jmp]);
-                if (hdr->isResponse == false && next_record_is_response(tracepos, trace)) {
+                if (next_record_is_response(tracepos, trace)) {
+                    break;
+                }
 
-                    tracelog_hdr_t *next_hdr = (tracelog_hdr_t *)(trace + tracepos);
-                    tracepos += TRACELOG_HDR_LEN + next_hdr->data_len + TRACELOG_PARITY_LEN(next_hdr);
-                    if (next_hdr->data_len < 7) {
-                        break;
-                    }
-                    PrintAndLogEx(INFO, "AES 1499999999 %s " NOLF, sprint_hex_inrow(next_hdr->frame + 1, 8));
+                tracelog_hdr_t *next_hdr = (tracelog_hdr_t *)(trace + tracepos);
+                tracepos += TRACELOG_HDR_LEN + next_hdr->data_len + TRACELOG_PARITY_LEN(next_hdr);
+                if (next_hdr->data_len < 7) {
+                    break;
+                }
 
-                    next_hdr = (tracelog_hdr_t *)(trace + tracepos);
-                    tracepos += TRACELOG_HDR_LEN + next_hdr->data_len + TRACELOG_PARITY_LEN(next_hdr);
+                PrintAndLogEx(INFO, "AES 1499999999 %s " NOLF, sprint_hex_inrow(next_hdr->frame + 1, 8));
 
-                    if (next_hdr->frame[pos] == MFDES_ADDITIONAL_FRAME) {
-                        PrintAndLogEx(NORMAL, "%s", sprint_hex_inrow(next_hdr->frame + pos + long_jmp, 16));
-                    }
-                    return tracepos;
+                next_hdr = (tracelog_hdr_t *)(trace + tracepos);
+                tracepos += TRACELOG_HDR_LEN + next_hdr->data_len + TRACELOG_PARITY_LEN(next_hdr);
+
+                if (next_hdr->frame[pos] == MFDES_ADDITIONAL_FRAME) {
+                    PrintAndLogEx(NORMAL, "%s", sprint_hex_inrow(next_hdr->frame + pos + long_jmp, 16));
+                } else {
+                    PrintAndLogEx(NORMAL, "");
                 }
-                break;
+                return tracepos;
             }
             case MFDES_AUTHENTICATE_EV2F: {
-                if (hdr->isResponse == false) {
-                    PrintAndLogEx(INFO, "AUTH EV2 First");
-                }
-                break;
+                PrintAndLogEx(INFO, "AUTH EV2 First");
+                uint16_t tmp = extractChall_ev2(tracepos, trace, pos, long_jmp);
+                if (tmp == 0)
+                    break;
+                else
+                    return tmp;
+
             }
             case MFDES_AUTHENTICATE_EV2NF: {
-                if (hdr->isResponse == false) {
-                    PrintAndLogEx(INFO, "AUTH EV2 Non First");
-                }
-                break;
+                PrintAndLogEx(INFO, "AUTH EV2 Non First");
+                uint16_t tmp = extractChall_ev2(tracepos, trace, pos, long_jmp);
+                if (tmp == 0)
+                    break;
+                else
+                    return tmp;
             }
         }
     }
