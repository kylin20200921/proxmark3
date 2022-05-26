commit 7aeb462e18055677b47e6a41c42f78b2cf554a21
Author: iceman1001 <iceman@iuse.se>
Date:   Wed Mar 23 17:37:40 2022 +0100

    convert 15readercommand to use NG frames. Removing the confusing of status and length of received package in same return param.  Now returns proper status and length is a ref param

diff --git a/armsrc/Standalone/hf_tmudford.c b/armsrc/Standalone/hf_tmudford.c
index 7fc277a47..158250a0c 100644
--- a/armsrc/Standalone/hf_tmudford.c
+++ b/armsrc/Standalone/hf_tmudford.c
@@ -61,7 +61,7 @@ void RunMod(void) {
                 break;
             else if (state == STATE_READ) {
                 Iso15693InitReader();
-                ReaderIso15693(0, &card);
+                ReaderIso15693(&card);
 
                 if (card.uidlen == 0) {
                     LED_D_OFF();
diff --git a/armsrc/appmain.c b/armsrc/appmain.c
index 01ba06a60..84a7ea5ff 100644
--- a/armsrc/appmain.c
+++ b/armsrc/appmain.c
@@ -1245,7 +1245,7 @@ static void PacketReceived(PacketCommandNG *packet) {
             break;
         }
         case CMD_HF_ISO15693_READER: {
-            ReaderIso15693(packet->oldarg[0], NULL);
+            ReaderIso15693(NULL);
             break;
         }
         case CMD_HF_ISO15693_SIMULATE: {
diff --git a/armsrc/iclass.c b/armsrc/iclass.c
index cabec62ca..326d77298 100644
--- a/armsrc/iclass.c
+++ b/armsrc/iclass.c
@@ -1256,6 +1256,9 @@ static void iclass_send_as_reader(uint8_t *frame, int len, uint32_t *start_time,
 static bool iclass_send_cmd_with_retries(uint8_t *cmd, size_t cmdsize, uint8_t *resp, size_t max_resp_size,
                                          uint8_t expected_size, uint8_t tries, uint32_t *start_time,
                                          uint16_t timeout, uint32_t *eof_time) {
+
+    uint16_t resp_len = 0;
+    int res;
     while (tries-- > 0) {
 
         iclass_send_as_reader(cmd, cmdsize, start_time, eof_time);
@@ -1264,7 +1267,8 @@ static bool iclass_send_cmd_with_retries(uint8_t *cmd, size_t cmdsize, uint8_t *
             return true;
         }
 
-        if (expected_size == GetIso15693AnswerFromTag(resp, max_resp_size, timeout, eof_time, false, true)) {
+        res = GetIso15693AnswerFromTag(resp, max_resp_size, timeout, eof_time, false, true, &resp_len);
+        if (res == PM3_SUCCESS && expected_size == resp_len) {
             return true;
         }
     }
@@ -1296,8 +1300,10 @@ static bool select_iclass_tag_ex(picopass_hdr_t *hdr, bool use_credit_key, uint3
     // wakeup
     uint32_t start_time = GetCountSspClk();
     iclass_send_as_reader(act_all, 1, &start_time, eof_time);
-    int len = GetIso15693AnswerFromTag(resp, sizeof(resp), ICLASS_READER_TIMEOUT_ACTALL, eof_time, false, true);
-    if (len < 0)
+    int res;
+    uint16_t resp_len = 0;
+    res = GetIso15693AnswerFromTag(resp, sizeof(resp), ICLASS_READER_TIMEOUT_ACTALL, eof_time, false, true, &resp_len);
+    if (res != PM3_SUCCESS)
         return false;
 
     // send Identify
@@ -1305,8 +1311,8 @@ static bool select_iclass_tag_ex(picopass_hdr_t *hdr, bool use_credit_key, uint3
     iclass_send_as_reader(identify, 1, &start_time, eof_time);
 
     // expect a 10-byte response here, 8 byte anticollision-CSN and 2 byte CRC
-    len = GetIso15693AnswerFromTag(resp, sizeof(resp), ICLASS_READER_TIMEOUT_OTHERS, eof_time, false, true);
-    if (len != 10)
+    res = GetIso15693AnswerFromTag(resp, sizeof(resp), ICLASS_READER_TIMEOUT_OTHERS, eof_time, false, true, &resp_len);
+    if (res != PM3_SUCCESS || resp_len != 10)
         return false;
 
     // copy the Anti-collision CSN to our select-packet
@@ -1317,8 +1323,8 @@ static bool select_iclass_tag_ex(picopass_hdr_t *hdr, bool use_credit_key, uint3
     iclass_send_as_reader(select, sizeof(select), &start_time, eof_time);
 
     // expect a 10-byte response here, 8 byte CSN and 2 byte CRC
-    len = GetIso15693AnswerFromTag(resp, sizeof(resp), ICLASS_READER_TIMEOUT_OTHERS, eof_time, false, true);
-    if (len != 10)
+    res = GetIso15693AnswerFromTag(resp, sizeof(resp), ICLASS_READER_TIMEOUT_OTHERS, eof_time, false, true, &resp_len);
+    if (res != PM3_SUCCESS || resp_len != 10)
         return false;
 
     // save CSN
@@ -1329,8 +1335,8 @@ static bool select_iclass_tag_ex(picopass_hdr_t *hdr, bool use_credit_key, uint3
     iclass_send_as_reader(read_conf, sizeof(read_conf), &start_time, eof_time);
 
     // expect a 8-byte response here
-    len = GetIso15693AnswerFromTag(resp, sizeof(resp), ICLASS_READER_TIMEOUT_OTHERS, eof_time, false, true);
-    if (len != 10)
+    res = GetIso15693AnswerFromTag(resp, sizeof(resp), ICLASS_READER_TIMEOUT_OTHERS, eof_time, false, true, &resp_len);
+    if (res != PM3_SUCCESS || resp_len != 10)
         return false;
 
     // save CONF
@@ -1347,8 +1353,8 @@ static bool select_iclass_tag_ex(picopass_hdr_t *hdr, bool use_credit_key, uint3
         iclass_send_as_reader(read_aia, sizeof(read_aia), &start_time, eof_time);
 
         // expect a 10-byte response here
-        len = GetIso15693AnswerFromTag(resp, sizeof(resp), ICLASS_READER_TIMEOUT_OTHERS, eof_time, false, true);
-        if (len != 10)
+        res = GetIso15693AnswerFromTag(resp, sizeof(resp), ICLASS_READER_TIMEOUT_OTHERS, eof_time, false, true, &resp_len);
+        if (res != PM3_SUCCESS || resp_len != 10)
             return false;
 
         if (status) {
@@ -1361,8 +1367,8 @@ static bool select_iclass_tag_ex(picopass_hdr_t *hdr, bool use_credit_key, uint3
         iclass_send_as_reader(read_check_cc, sizeof(read_check_cc), &start_time, eof_time);
 
         // expect a 8-byte response here
-        len = GetIso15693AnswerFromTag(resp, sizeof(resp), ICLASS_READER_TIMEOUT_OTHERS, eof_time, false, true);
-        if (len != 8)
+        res = GetIso15693AnswerFromTag(resp, sizeof(resp), ICLASS_READER_TIMEOUT_OTHERS, eof_time, false, true, &resp_len);
+        if (res != PM3_SUCCESS || resp_len != 8)
             return false;
 
         memcpy(hdr->epurse, resp, sizeof(hdr->epurse));
@@ -1383,8 +1389,8 @@ static bool select_iclass_tag_ex(picopass_hdr_t *hdr, bool use_credit_key, uint3
         iclass_send_as_reader(read_aia, sizeof(read_aia), &start_time, eof_time);
 
         // expect a 10-byte response here
-        len = GetIso15693AnswerFromTag(resp, sizeof(resp), ICLASS_READER_TIMEOUT_OTHERS, eof_time, false, true);
-        if (len != 10)
+        res = GetIso15693AnswerFromTag(resp, sizeof(resp), ICLASS_READER_TIMEOUT_OTHERS, eof_time, false, true, &resp_len);
+        if (res != PM3_SUCCESS || resp_len != 10)
             return false;
 
         if (status) {
@@ -1870,7 +1876,9 @@ void iClass_WriteBlock(uint8_t *msg) {
             return;
         } else {
 
-            if (GetIso15693AnswerFromTag(resp, sizeof(resp), ICLASS_READER_TIMEOUT_UPDATE, &eof_time, false, true) == 10) {
+            uint16_t resp_len = 0;
+            int res2 = GetIso15693AnswerFromTag(resp, sizeof(resp), ICLASS_READER_TIMEOUT_UPDATE, &eof_time, false, true, &resp_len);
+            if (res2 == PM3_SUCCESS && resp_len == 10) {
                 res = true;
                 break;
             }
diff --git a/armsrc/iso15693.c b/armsrc/iso15693.c
index 32a23d2e0..b5e67234a 100644
--- a/armsrc/iso15693.c
+++ b/armsrc/iso15693.c
@@ -396,13 +396,13 @@ typedef struct {
     } lastBit;
     uint16_t shiftReg;
     uint16_t max_len;
-    uint8_t *output;
-    int len;
+    uint16_t len;
     int sum1;
     int sum2;
     int threshold_sof;
     int threshold_half;
-    uint16_t  previous_amplitude;
+    uint16_t previous_amplitude;
+    uint8_t *output;
 } DecodeTag_t;
 
 //-----------------------------------------------------------------------------
@@ -964,10 +964,12 @@ static int RAMFUNC Handle15693FSKSamplesFromTag(uint8_t freq, DecodeTagFSK_t *De
 /*
  *  Receive and decode the tag response, also log to tracebuffer
  */
-int GetIso15693AnswerFromTag(uint8_t *response, uint16_t max_len, uint16_t timeout, uint32_t *eof_time, bool fsk, bool recv_speed) {
-
-    int samples = 0, ret = 0;
+int GetIso15693AnswerFromTag(uint8_t *response, uint16_t max_len, uint16_t timeout, uint32_t *eof_time, bool fsk, bool recv_speed, uint16_t *resp_len) {
 
+    int samples = 0, ret = PM3_SUCCESS;
+    if (resp_len) {
+        *resp_len = 0;
+    }
     // the Decoder data structure
     DecodeTag_t dtm = { 0 };
     DecodeTag_t *dt = &dtm;
@@ -975,10 +977,10 @@ int GetIso15693AnswerFromTag(uint8_t *response, uint16_t max_len, uint16_t timeo
     DecodeTagFSK_t dtfm = { 0 };
     DecodeTagFSK_t *dtf = &dtfm;
 
-    if (!fsk)
-        DecodeTagInit(dt, response, max_len);
-    else
+    if (fsk)
         DecodeTagFSKInit(dtf, response, max_len);
+    else
+        DecodeTagInit(dt, response, max_len);
 
     // wait for last transfer to complete
     while (!(AT91C_BASE_SSC->SSC_SR & AT91C_SSC_TXEMPTY));
@@ -995,7 +997,7 @@ int GetIso15693AnswerFromTag(uint8_t *response, uint16_t max_len, uint16_t timeo
     // Setup and start DMA.
     if (FpgaSetupSscDma((uint8_t *) dma->buf, DMA_BUFFER_SIZE) == false) {
         if (g_dbglevel > DBG_ERROR) Dbprintf("FpgaSetupSscDma failed. Exiting");
-        return -4;
+        return PM3_EINIT;
     }
 
     uint32_t dma_start_time = 0;
@@ -1034,51 +1036,54 @@ int GetIso15693AnswerFromTag(uint8_t *response, uint16_t max_len, uint16_t timeo
 
                 WDT_HIT();
                 if (BUTTON_PRESS()) {
-                    DbpString("stopped");
                     break;
                 }
             }
         }
 
-        if (!fsk) {
-            if (Handle15693SamplesFromTag(tagdata & 0x3FFF, dt, recv_speed)) {
+        if (fsk) {
+
+            if (Handle15693FSKSamplesFromTag(tagdata >> 14, dtf, recv_speed)) {
 
                 *eof_time = dma_start_time + (samples * 16) - DELAY_TAG_TO_ARM; // end of EOF
 
-                if (dt->lastBit == SOF_PART2) {
+                if (dtf->lastBit == SOF) {
                     *eof_time -= (8 * 16); // needed 8 additional samples to confirm single SOF (iCLASS)
                 }
-                if (dt->len > dt->max_len) {
-                    ret = -2; // buffer overflow
-                    Dbprintf("overflow (%d > %d", dt->len, dt->max_len);
+
+                if (dtf->len > dtf->max_len) {
+                    ret = PM3_EOVFLOW;
+                    Dbprintf("overflow (%d > %d", dtf->len, dtf->max_len);
                 }
                 break;
             }
 
             // timeout
-            if (samples > timeout && dt->state < STATE_TAG_RECEIVING_DATA) {
-                ret = -3;
+            if (samples > timeout && dtf->state < STATE_FSK_RECEIVING_DATA_484) {
+                ret = PM3_ETIMEOUT;
                 break;
             }
-        }
-        else {
-            if (Handle15693FSKSamplesFromTag(tagdata >> 14, dtf, recv_speed)) {
+
+        } else {
+
+            if (Handle15693SamplesFromTag(tagdata & 0x3FFF, dt, recv_speed)) {
 
                 *eof_time = dma_start_time + (samples * 16) - DELAY_TAG_TO_ARM; // end of EOF
 
-                if (dtf->lastBit == SOF) {
+                if (dt->lastBit == SOF_PART2) {
                     *eof_time -= (8 * 16); // needed 8 additional samples to confirm single SOF (iCLASS)
                 }
-                if (dtf->len > dtf->max_len) {
-                    ret = -2; // buffer overflow
-                    Dbprintf("overflow (%d > %d", dtf->len, dtf->max_len);
+
+                if (dt->len > dt->max_len) {
+                    ret = PM3_EOVFLOW;
+                    Dbprintf("overflow (%d > %d", dt->len, dt->max_len);
                 }
                 break;
             }
 
             // timeout
-            if (samples > timeout && dtf->state < STATE_FSK_RECEIVING_DATA_484) {
-                ret = -3;
+            if (samples > timeout && dt->state < STATE_TAG_RECEIVING_DATA) {
+                ret = PM3_ETIMEOUT;
                 break;
             }
         }
@@ -1089,25 +1094,7 @@ int GetIso15693AnswerFromTag(uint8_t *response, uint16_t max_len, uint16_t timeo
 
     uint32_t sof_time = *eof_time - (32 * 16);  // time for SOF transfer
 
-    if (!fsk) {
-        sof_time -= (dt->len * 8 * 8 * 16) // time for byte transfers
-                    + (dt->lastBit != SOF_PART2 ? (32 * 16) : 0); // time for EOF transfer
-
-        if (g_dbglevel >= DBG_EXTENDED) {
-            Dbprintf("samples = %d, ret = %d, Decoder: state = %d, lastBit = %d, len = %d, bitCount = %d, posCount = %d, maxlen = %u",
-                     samples,
-                     ret,
-                     dt->state,
-                     dt->lastBit,
-                     dt->len,
-                     dt->bitCount,
-                     dt->posCount,
-                     dt->max_len
-                );
-            Dbprintf("timing: sof_time = %d, eof_time = %d", (sof_time * 4), (*eof_time * 4));
-        }
-    }
-    else {
+    if (fsk) {
         sof_time -= (dtf->len * 8 * 8 * 16) // time for byte transfers
                     + (dtf->lastBit != SOF ? (32 * 16) : 0); // time for EOF transfer
 
@@ -1124,18 +1111,38 @@ int GetIso15693AnswerFromTag(uint8_t *response, uint16_t max_len, uint16_t timeo
                 );
             Dbprintf("timing: sof_time = %d, eof_time = %d", (sof_time * 4), (*eof_time * 4));
         }
+    } else {
+        sof_time -= (dt->len * 8 * 8 * 16) // time for byte transfers
+                    + (dt->lastBit != SOF_PART2 ? (32 * 16) : 0); // time for EOF transfer
+
+        if (g_dbglevel >= DBG_EXTENDED) {
+            Dbprintf("samples = %d, ret = %d, Decoder: state = %d, lastBit = %d, len = %d, bitCount = %d, posCount = %d, maxlen = %u",
+                     samples,
+                     ret,
+                     dt->state,
+                     dt->lastBit,
+                     dt->len,
+                     dt->bitCount,
+                     dt->posCount,
+                     dt->max_len
+                );
+            Dbprintf("timing: sof_time = %d, eof_time = %d", (sof_time * 4), (*eof_time * 4));
+        }
     }
 
-    if (ret < 0) {
+    if (ret != PM3_SUCCESS) {
+        *resp_len = 0;
         return ret;
     }
 
-    if (!fsk) {
+    if (fsk) {
+        LogTrace_ISO15693(dtf->output, dtf->len, (sof_time * 4), (*eof_time * 4), NULL, false);
+        *resp_len = dtf->len;
+    } else {
         LogTrace_ISO15693(dt->output, dt->len, (sof_time * 4), (*eof_time * 4), NULL, false);
-        return dt->len;
+        *resp_len = dt->len;
     }
-    LogTrace_ISO15693(dtf->output, dtf->len, (sof_time * 4), (*eof_time * 4), NULL, false);
-    return dtf->len;
+    return PM3_SUCCESS;
 }
 
 
@@ -1633,7 +1640,7 @@ void SniffIso15693(uint8_t jam_search_len, uint8_t *jam_search_string, bool icla
 
     FpgaDownloadAndGo(FPGA_BITSTREAM_HF_15);
 
-    DbpString("Starting to sniff. Press PM3 Button to stop.");
+    DbpString("Starting to sniff. Press <PM3 button> to stop");
 
     BigBuf_free();
     clear_trace();
@@ -1719,7 +1726,6 @@ void SniffIso15693(uint8_t jam_search_len, uint8_t *jam_search_string, bool icla
 
                 WDT_HIT();
                 if (BUTTON_PRESS()) {
-                    DbpString("Sniff stopped");
                     break;
                 }
             }
@@ -1912,16 +1918,13 @@ static void BuildIdentifyRequest(uint8_t *cmd) {
 //  return: length of received data
 // logging enabled
 int SendDataTag(uint8_t *send, int sendlen, bool init, bool speed_fast, uint8_t *recv,
-                uint16_t max_recv_len, uint32_t start_time, uint16_t timeout, uint32_t *eof_time) {
+                uint16_t max_recv_len, uint32_t start_time, uint16_t timeout, uint32_t *eof_time, uint16_t *resp_len) {
 
     if (init) {
         Iso15693InitReader();
         start_time = GetCountSspClk();
     }
 
-    bool fsk = send[0] & ISO15_REQ_SUBCARRIER_TWO;
-	bool recv_speed = send[0] & ISO15_REQ_DATARATE_HIGH;
-
     if (speed_fast) {
         // high speed (1 out of 4)
         CodeIso15693AsReader(send, sendlen);
@@ -1929,26 +1932,28 @@ int SendDataTag(uint8_t *send, int sendlen, bool init, bool speed_fast, uint8_t
         // low speed (1 out of 256)
         CodeIso15693AsReader256(send, sendlen);
     }
-    int res = 0;
+
     tosend_t *ts = get_tosend();
     TransmitTo15693Tag(ts->buf, ts->max, &start_time);
 
     if (tearoff_hook() == PM3_ETEAROFF) { // tearoff occurred
-
-        res = PM3_ETEAROFF;
-
+        *resp_len = 0;
+        return PM3_ETEAROFF;
     } else {
 
+        int res = PM3_SUCCESS;
         *eof_time = start_time + 32 * ((8 * ts->max) - 4); // subtract the 4 padding bits after EOF
         LogTrace_ISO15693(send, sendlen, (start_time * 4), (*eof_time * 4), NULL, true);
         if (recv != NULL) {
-            res = GetIso15693AnswerFromTag(recv, max_recv_len, timeout, eof_time, fsk, recv_speed);
+            bool fsk = send[0] & ISO15_REQ_SUBCARRIER_TWO;
+            bool recv_speed = send[0] & ISO15_REQ_DATARATE_HIGH;
+            res = GetIso15693AnswerFromTag(recv, max_recv_len, timeout, eof_time, fsk, recv_speed, resp_len);
         }
+        return res;
     }
-    return res;
 }
 
-int SendDataTagEOF(uint8_t *recv, uint16_t max_recv_len, uint32_t start_time, uint16_t timeout, uint32_t *eof_time, bool fsk, bool recv_speed) {
+int SendDataTagEOF(uint8_t *recv, uint16_t max_recv_len, uint32_t start_time, uint16_t timeout, uint32_t *eof_time, bool fsk, bool recv_speed, uint16_t *resp_len) {
 
     CodeIso15693AsReaderEOF();
     tosend_t *ts = get_tosend();
@@ -1956,9 +1961,9 @@ int SendDataTagEOF(uint8_t *recv, uint16_t max_recv_len, uint32_t start_time, ui
     uint32_t end_time = start_time + 32 * (8 * ts->max - 4); // subtract the 4 padding bits after EOF
     LogTrace_ISO15693(NULL, 0, (start_time * 4), (end_time * 4), NULL, true);
 
-    int res = 0;
-    if (recv != NULL) {
-        res = GetIso15693AnswerFromTag(recv, max_recv_len, timeout, eof_time, fsk, recv_speed);
+    int res = PM3_SUCCESS;
+    if (recv) {
+        res = GetIso15693AnswerFromTag(recv, max_recv_len, timeout, eof_time, fsk, recv_speed, resp_len);
     }
     return res;
 }
@@ -2034,9 +2039,7 @@ static void DbdecodeIso15693Answer(int len, uint8_t *d) {
 // Act as ISO15693 reader, perform anti-collision and then attempt to read a sector
 // all demodulation performed in arm rather than host. - greg
 //-----------------------------------------------------------------------------
-// ok
-// parameter is unused !?!
-void ReaderIso15693(uint32_t parameter, iso15_card_select_t *p_card) {
+void ReaderIso15693(iso15_card_select_t *p_card) {
 
     LED_A_ON();
     set_tracing(true);
@@ -2052,10 +2055,11 @@ void ReaderIso15693(uint32_t parameter, iso15_card_select_t *p_card) {
     BuildIdentifyRequest(cmd);
     uint32_t start_time = 0;
     uint32_t eof_time;
-    int recvlen = SendDataTag(cmd, sizeof(cmd), true, true, answer, ISO15693_MAX_RESPONSE_LENGTH, start_time, ISO15693_READER_TIMEOUT, &eof_time);
+    uint16_t recvlen = 0; 
+    int res = SendDataTag(cmd, sizeof(cmd), true, true, answer, ISO15693_MAX_RESPONSE_LENGTH, start_time, ISO15693_READER_TIMEOUT, &eof_time, &recvlen);
 
-    if (recvlen == PM3_ETEAROFF) { // tearoff occurred
-        reply_mix(CMD_ACK, recvlen, 0, 0, NULL, 0);
+    if (res == PM3_ETEAROFF) { // tearoff occurred
+        reply_ng(CMD_HF_ISO15693_READER, res, NULL, 0);
     } else {
 
         //start_time = eof_time + DELAY_ISO15693_VICC_TO_VCD_READER;
@@ -2088,7 +2092,7 @@ void ReaderIso15693(uint32_t parameter, iso15_card_select_t *p_card) {
             // arg1 = len of response (12 bytes)
             // arg2 = rtf
             // asbytes = uid.
-            reply_mix(CMD_ACK, 1, sizeof(uid), 0, uid, sizeof(uid));
+            reply_ng(CMD_HF_ISO15693_READER, PM3_SUCCESS, uid, sizeof(uid));
 
             if (g_dbglevel >= DBG_EXTENDED) {
                 Dbprintf("[+] %d octets read from IDENTIFY request:", recvlen);
@@ -2098,7 +2102,7 @@ void ReaderIso15693(uint32_t parameter, iso15_card_select_t *p_card) {
         } else {
             p_card->uidlen = 0;
             DbpString("Failed to select card");
-            reply_mix(CMD_ACK, 0, 0, 0, NULL, 0);
+            reply_ng(CMD_HF_ISO15693_READER, PM3_EFAILED, NULL, 0);
         }
     }
     switch_off();
@@ -2302,18 +2306,20 @@ void BruteforceIso15693Afi(uint32_t speed) {
 
     int datalen = 5;
     uint32_t eof_time = 0;
-    int recvlen = SendDataTag(data, datalen, true, speed, recv, sizeof(recv), 0, ISO15693_READER_TIMEOUT, &eof_time);
-    uint32_t start_time = eof_time + DELAY_ISO15693_VICC_TO_VCD_READER;
+    uint16_t recvlen = 0;
+    int res = SendDataTag(data, datalen, true, speed, recv, sizeof(recv), 0, ISO15693_READER_TIMEOUT, &eof_time, &recvlen);
+    if (res != PM3_SUCCESS) {
+        DbpString("Failed to select card");
+        reply_ng(CMD_HF_ISO15693_FINDAFI, res, NULL, 0);
+        switch_off();
+        return;
+    }
 
+    uint32_t start_time = eof_time + DELAY_ISO15693_VICC_TO_VCD_READER;
     WDT_HIT();
 
     if (recvlen >= 12) {
         Dbprintf("NoAFI UID = %s", iso15693_sprintUID(NULL, recv + 2));
-    } else {
-        DbpString("Failed to select card");
-        reply_ng(CMD_HF_ISO15693_FINDAFI, PM3_ESOFT, NULL, 0);
-        switch_off();
-        return;
     }
 
     // now with AFI
@@ -2330,7 +2336,8 @@ void BruteforceIso15693Afi(uint32_t speed) {
         data[2] = i & 0xFF;
         AddCrc15(data, 4);
 
-        recvlen = SendDataTag(data, datalen, false, speed, recv, sizeof(recv), start_time, ISO15693_READER_TIMEOUT, &eof_time);
+        recvlen = 0;
+        res = SendDataTag(data, datalen, false, speed, recv, sizeof(recv), start_time, ISO15693_READER_TIMEOUT, &eof_time, &recvlen);
         start_time = eof_time + DELAY_ISO15693_VICC_TO_VCD_READER;
 
         WDT_HIT();
@@ -2339,7 +2346,7 @@ void BruteforceIso15693Afi(uint32_t speed) {
             Dbprintf("AFI = %i  UID = %s", i, iso15693_sprintUID(NULL, recv + 2));
         }
 
-        aborted = BUTTON_PRESS() && data_available();
+        aborted = (BUTTON_PRESS() && data_available());
         if (aborted) {
             break;
         }
@@ -2382,10 +2389,10 @@ void DirectTag15693Command(uint32_t datalen, uint32_t speed, uint32_t recv, uint
     }
 
     uint32_t start_time = 0;
-    int recvlen = SendDataTag(data, datalen, true, speed, (recv ? recvbuf : NULL), sizeof(recvbuf), start_time, timeout, &eof_time);
-
-    if (recvlen == PM3_ETEAROFF) { // tearoff occurred
-        reply_mix(CMD_ACK, recvlen, 0, 0, NULL, 0);
+    uint16_t recvlen = 0;
+    int res = SendDataTag(data, datalen, true, speed, (recv ? recvbuf : NULL), sizeof(recvbuf), start_time, timeout, &eof_time, &recvlen);
+    if (res == PM3_ETEAROFF) { // tearoff occurred
+        reply_ng(CMD_HF_ISO15693_COMMAND, res, NULL, 0);
     } else {
 
         bool fsk = data[0] & ISO15_REQ_SUBCARRIER_TWO;
@@ -2394,16 +2401,18 @@ void DirectTag15693Command(uint32_t datalen, uint32_t speed, uint32_t recv, uint
         // send a single EOF to get the tag response
         if (request_answer) {
             start_time = eof_time + DELAY_ISO15693_VICC_TO_VCD_READER;
-            recvlen = SendDataTagEOF((recv ? recvbuf : NULL), sizeof(recvbuf), start_time, ISO15693_READER_TIMEOUT, &eof_time, fsk, recv_speed);
+            res = SendDataTagEOF((recv ? recvbuf : NULL), sizeof(recvbuf), start_time, ISO15693_READER_TIMEOUT, &eof_time, fsk, recv_speed, &recvlen);
         }
 
         if (recv) {
             recvlen = MIN(recvlen, ISO15693_MAX_RESPONSE_LENGTH);
-            reply_mix(CMD_ACK, recvlen, 0, 0, recvbuf, recvlen);
+            reply_ng(CMD_HF_ISO15693_COMMAND, res, recvbuf, recvlen);
         } else {
-            reply_mix(CMD_ACK, 1, 0, 0, NULL, 0);
+            reply_ng(CMD_HF_ISO15693_COMMAND, PM3_SUCCESS, NULL, 0);
         }
     }
+
+
     // note: this prevents using hf 15 cmd with s option - which isn't implemented yet anyway
     // also prevents hf 15 raw -k  keep_field on ...
     FpgaWriteConfWord(FPGA_MAJOR_MODE_OFF);
@@ -2423,10 +2432,11 @@ void LockPassSlixIso15693(uint32_t pass_id, uint32_t password) {
     //uint8_t cmd_write_pass[] = {ISO15693_REQ_DATARATE_HIGH | ISO15693_REQ_ADDRESS, 0xB4, 0x04, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x04, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00 };
     uint8_t cmd_lock_pass[] = {ISO15693_REQ_DATARATE_HIGH | ISO15693_REQ_ADDRESS, 0xB5, 0x04, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x04, 0x00, 0x00 };
     uint16_t crc;
-    int recvlen = 0;
+    uint16_t recvlen = 0;    
     uint8_t recvbuf[ISO15693_MAX_RESPONSE_LENGTH];
     uint32_t start_time = 0;
     bool done = false;
+    int res;
 
     // setup 'get random number' command
     crc = Iso15693Crc(cmd_get_rnd, 3);
@@ -2458,8 +2468,8 @@ void LockPassSlixIso15693(uint32_t pass_id, uint32_t password) {
             break;
         }
 
-        recvlen = SendDataTag(cmd_get_rnd, sizeof(cmd_get_rnd), true, true, recvbuf, sizeof(recvbuf), start_time);
-        if (recvlen != 5) {
+        res = SendDataTag(cmd_get_rnd, sizeof(cmd_get_rnd), true, true, recvbuf, sizeof(recvbuf), start_time, &recvlen);
+        if (res != PM3_SUCCESS && recvlen != 5) {
             LED_C_ON();
         } else {
             Dbprintf("LockPass: Received random 0x%02X%02X (%d)", recvbuf[1], recvbuf[2], recvlen);
@@ -2475,8 +2485,8 @@ void LockPassSlixIso15693(uint32_t pass_id, uint32_t password) {
             cmd_set_pass[9] = crc >> 8;
 
             Dbprintf("LockPass: Sending old password to end privacy mode", cmd_set_pass[4], cmd_set_pass[5], cmd_set_pass[6], cmd_set_pass[7]);
-            recvlen = SendDataTag(cmd_set_pass, sizeof(cmd_set_pass), false, true, recvbuf, sizeof(recvbuf), start_time);
-            if (recvlen != 3) {
+            res = SendDataTag(cmd_set_pass, sizeof(cmd_set_pass), false, true, recvbuf, sizeof(recvbuf), start_time, &recvlen);
+            if (res != PM3_SUCCESS && recvlen != 3) {
                 Dbprintf("LockPass: Failed to set password (%d)", recvlen);
                 LED_B_ON();
             } else {
@@ -2485,8 +2495,8 @@ void LockPassSlixIso15693(uint32_t pass_id, uint32_t password) {
                 cmd_inventory[4] = crc >> 8;
 
                 Dbprintf("LockPass: Searching for tag...");
-                recvlen = SendDataTag(cmd_inventory, sizeof(cmd_inventory), false, true, recvbuf, sizeof(recvbuf), start_time);
-                if (recvlen != 12) {
+                res = SendDataTag(cmd_inventory, sizeof(cmd_inventory), false, true, recvbuf, sizeof(recvbuf), start_time, &recvlen);
+                if (res != PM3_SUCCESS && recvlen != 12) {
                     Dbprintf("LockPass: Failed to read inventory (%d)", recvlen);
                     LED_B_ON();
                     LED_C_ON();
@@ -2504,8 +2514,8 @@ void LockPassSlixIso15693(uint32_t pass_id, uint32_t password) {
 
                     Dbprintf("LockPass: locking to password 0x%02X%02X%02X%02X for ID %02X", cmd_set_pass[4], cmd_set_pass[5], cmd_set_pass[6], cmd_set_pass[7], pass_id);
 
-                    recvlen = SendDataTag(cmd_lock_pass, sizeof(cmd_lock_pass), false, true, recvbuf, sizeof(recvbuf), start_time);
-                    if (recvlen != 3) {
+                    res = SendDataTag(cmd_lock_pass, sizeof(cmd_lock_pass), false, true, recvbuf, sizeof(recvbuf), start_time, &recvlen);
+                    if (res != PM3_SUCCESS && recvlen != 3) {
                         Dbprintf("LockPass: Failed to lock password (%d)", recvlen);
                     } else {
                         Dbprintf("LockPass: Successful (%d)", recvlen);
@@ -2558,12 +2568,24 @@ void SetTag15693Uid(const uint8_t *uid) {
 
     uint32_t start_time = 0;
     uint32_t eof_time = 0;
+    uint16_t recvlen = 0;
+    int res = PM3_SUCCESS;
     for (int i = 0; i < 4; i++) {
-        SendDataTag(cmd[i], sizeof(cmd[i]), i == 0 ? true : false, true, recvbuf, sizeof(recvbuf), start_time, ISO15693_READER_TIMEOUT_WRITE, &eof_time);
+        res = SendDataTag(
+            cmd[i],
+            sizeof(cmd[i]),
+            (i == 0) ? true : false,
+            true,
+            recvbuf,
+            sizeof(recvbuf),
+            start_time,
+            ISO15693_READER_TIMEOUT_WRITE,
+            &eof_time,
+            &recvlen);
         start_time = eof_time + DELAY_ISO15693_VICC_TO_VCD_READER;
     }
 
-    reply_ng(CMD_HF_ISO15693_CSETUID, PM3_SUCCESS, NULL, 0);
+    reply_ng(CMD_HF_ISO15693_CSETUID, res, NULL, 0);
     switch_off();
 }
 
@@ -2583,8 +2605,9 @@ static bool get_rnd_15693_slixl(uint32_t start_time, uint32_t *eof_time, uint8_t
     AddCrc15(c, 3);
 
     uint8_t recvbuf[ISO15693_MAX_RESPONSE_LENGTH];
-    int recvlen = SendDataTag(c, sizeof(c), false, true, recvbuf, sizeof(recvbuf), start_time, ISO15693_READER_TIMEOUT_WRITE, eof_time);
-    if (recvlen != 5) {
+    uint16_t recvlen = 0;
+    int res = SendDataTag(c, sizeof(c), false, true, recvbuf, sizeof(recvbuf), start_time, ISO15693_READER_TIMEOUT_WRITE, eof_time, &recvlen);
+    if (res != PM3_SUCCESS && recvlen != 5) {
         return false;
     }
 
@@ -2607,11 +2630,11 @@ static uint32_t set_pass_15693_slixl(uint32_t start_time, uint32_t *eof_time, ui
 
     start_time = *eof_time + DELAY_ISO15693_VICC_TO_VCD_READER;
     uint8_t recvbuf[ISO15693_MAX_RESPONSE_LENGTH];
-    int recvlen = SendDataTag(c, sizeof(c), false, true, recvbuf, sizeof(recvbuf), start_time, ISO15693_READER_TIMEOUT_WRITE, eof_time);
-    if (recvlen != 3) {
+    uint16_t recvlen = 0;
+    int res = SendDataTag(c, sizeof(c), false, true, recvbuf, sizeof(recvbuf), start_time, ISO15693_READER_TIMEOUT_WRITE, eof_time, &recvlen);
+    if (res != PM3_SUCCESS && recvlen != 3) {
         return PM3_EWRONGANSWER;
     }
-
     return PM3_SUCCESS;
 }
 
@@ -2629,8 +2652,9 @@ static uint32_t enable_privacy_15693_slixl(uint32_t start_time, uint32_t *eof_ti
 
     start_time = *eof_time + DELAY_ISO15693_VICC_TO_VCD_READER;
     uint8_t recvbuf[ISO15693_MAX_RESPONSE_LENGTH];
-    int recvlen = SendDataTag(c, sizeof(c), false, true, recvbuf, sizeof(recvbuf), start_time, ISO15693_READER_TIMEOUT_WRITE, eof_time);
-    if (recvlen != 3) {
+    uint16_t recvlen = 0
+    int res  = SendDataTag(c, sizeof(c), false, true, recvbuf, sizeof(recvbuf), start_time, ISO15693_READER_TIMEOUT_WRITE, eof_time, &recvlen);
+    if (res != PM3_SUCCESS && recvlen != 3) {
         return PM3_EWRONGANSWER;
     }
     return PM3_SUCCESS;
@@ -2651,8 +2675,9 @@ static uint32_t write_password_15693_slixl(uint32_t start_time, uint32_t *eof_ti
     start_time = *eof_time + DELAY_ISO15693_VICC_TO_VCD_READER;
 
     uint8_t recvbuf[ISO15693_MAX_RESPONSE_LENGTH];
-    int recvlen = SendDataTag(c, sizeof(c), false, true, recvbuf, sizeof(recvbuf), start_time, ISO15693_READER_TIMEOUT_WRITE, eof_time);
-    if (recvlen != 3) {
+    uint16_t recvlen = 0;
+    int res = SendDataTag(c, sizeof(c), false, true, recvbuf, sizeof(recvbuf), start_time, ISO15693_READER_TIMEOUT_WRITE, eof_time, &recvlen);
+    if (res != PM3_SUCCESS && recvlen != 3) {
         return PM3_EWRONGANSWER;
     }
     return PM3_SUCCESS;
@@ -2672,8 +2697,9 @@ static uint32_t destroy_15693_slixl(uint32_t start_time, uint32_t *eof_time, uin
 
     start_time = *eof_time + DELAY_ISO15693_VICC_TO_VCD_READER;
     uint8_t recvbuf[ISO15693_MAX_RESPONSE_LENGTH];
-    int recvlen = SendDataTag(c, sizeof(c), false, true, recvbuf, sizeof(recvbuf), start_time, ISO15693_READER_TIMEOUT_WRITE, eof_time);
-    if (recvlen != 3) {
+    uint16_t recvlen = 0;
+    int res = SendDataTag(c, sizeof(c), false, true, recvbuf, sizeof(recvbuf), start_time, ISO15693_READER_TIMEOUT_WRITE, eof_time, &recvlen);
+    if (res != PM3_SUCCESS && recvlen != 3) {
         return PM3_EWRONGANSWER;
     }
     return PM3_SUCCESS;
diff --git a/armsrc/iso15693.h b/armsrc/iso15693.h
index dbb3ff28b..a984a5f87 100644
--- a/armsrc/iso15693.h
+++ b/armsrc/iso15693.h
@@ -41,21 +41,21 @@ void CodeIso15693AsTag(const uint8_t *cmd, size_t len);
 void TransmitTo15693Reader(const uint8_t *cmd, size_t len, uint32_t *start_time, uint32_t slot_time, bool slow);
 int GetIso15693CommandFromReader(uint8_t *received, size_t max_len, uint32_t *eof_time);
 void TransmitTo15693Tag(const uint8_t *cmd, int len, uint32_t *start_time);
-int GetIso15693AnswerFromTag(uint8_t *response, uint16_t max_len, uint16_t timeout, uint32_t *eof_time, bool fsk, bool recv_speed);
+int GetIso15693AnswerFromTag(uint8_t *response, uint16_t max_len, uint16_t timeout, uint32_t *eof_time, bool fsk, bool recv_speed, uint16_t *resp_len);
 
 //void RecordRawAdcSamplesIso15693(void);
 void AcquireRawAdcSamplesIso15693(void);
-void ReaderIso15693(uint32_t parameter, iso15_card_select_t *p_card); // Simulate an ISO15693 reader - greg
-void SimTagIso15693(uint8_t *uid); // simulate an ISO15693 tag - greg
-void BruteforceIso15693Afi(uint32_t speed); // find an AFI of a tag - atrox
-void DirectTag15693Command(uint32_t datalen, uint32_t speed, uint32_t recv, uint8_t *data); // send arbitrary commands from CLI - atrox
+void ReaderIso15693(iso15_card_select_t *p_card); // ISO15693 reader 
+void SimTagIso15693(uint8_t *uid); // simulate an ISO15693 tag
+void BruteforceIso15693Afi(uint32_t speed); // find an AFI of a tag 
+void DirectTag15693Command(uint32_t datalen, uint32_t speed, uint32_t recv, uint8_t *data); // send arbitrary commands from CLI 
 
 void SniffIso15693(uint8_t jam_search_len, uint8_t *jam_search_string, bool iclass);
 
 int SendDataTag(uint8_t *send, int sendlen, bool init, bool speed_fast, uint8_t *recv,
-                uint16_t max_recv_len, uint32_t start_time, uint16_t timeout, uint32_t *eof_time);
+                uint16_t max_recv_len, uint32_t start_time, uint16_t timeout, uint32_t *eof_time, uint16_t *resp_len);
 
-int SendDataTagEOF(uint8_t *recv, uint16_t max_recv_len, uint32_t start_time, uint16_t timeout, uint32_t *eof_time, bool fsk, bool recv_speed);
+int SendDataTagEOF(uint8_t *recv, uint16_t max_recv_len, uint32_t start_time, uint16_t timeout, uint32_t *eof_time, bool fsk, bool recv_speed, uint16_t *resp_len);
 
 void SetTag15693Uid(const uint8_t *uid);
 
diff --git a/client/src/cmdhf15.c b/client/src/cmdhf15.c
index 9bd0d3f0a..2e79cdaee 100644
--- a/client/src/cmdhf15.c
+++ b/client/src/cmdhf15.c
@@ -429,13 +429,13 @@ static int getUID(bool loop, uint8_t *buf) {
         clearCommandBuffer();
         SendCommandMIX(CMD_HF_ISO15693_COMMAND, sizeof(data), fast, reply, data, sizeof(data));
         PacketResponseNG resp;
-        if (WaitForResponseTimeout(CMD_ACK, &resp, 2000)) {
+        if (WaitForResponseTimeout(CMD_HF_ISO15693_COMMAND, &resp, 2000)) {
 
-            int resplen = resp.oldarg[0];
-            if (resplen >= 12 && CheckCrc15(resp.data.asBytes, 12)) {
+            if (resp.status == PM3_SUCCESS && resp.length >= 12 && CheckCrc15(resp.data.asBytes, 12)) {
 
-                if (buf)
+                if (buf) {
                     memcpy(buf, resp.data.asBytes + 2, 8);
+                }
 
                 DropField();
 
@@ -665,7 +665,7 @@ static int NxpSysInfo(uint8_t *uid) {
     PacketResponseNG resp;
     clearCommandBuffer();
     SendCommandMIX(CMD_HF_ISO15693_COMMAND, reqlen, fast, reply, req, reqlen);
-    if (WaitForResponseTimeout(CMD_ACK, &resp, 2000) == false) {
+    if (WaitForResponseTimeout(CMD_HF_ISO15693_COMMAND, &resp, 2000) == false) {
         PrintAndLogEx(WARNING, "iso15693 timeout");
         DropField();
         return PM3_ETIMEOUT;
@@ -673,12 +673,11 @@ static int NxpSysInfo(uint8_t *uid) {
 
     DropField();
 
-    int status = resp.oldarg[0];
-    if (status == PM3_ETEAROFF) {
-        return status;
+    if (resp.status == PM3_ETEAROFF) {
+        return resp.status;
     }
 
-    if (status < 2) {
+    if (resp.length < 2) {
         PrintAndLogEx(WARNING, "iso15693 card doesn't answer to NXP systeminfo command");
         return PM3_EWRONGANSWER;
     }
@@ -736,13 +735,13 @@ static int NxpSysInfo(uint8_t *uid) {
         clearCommandBuffer();
         SendCommandMIX(CMD_HF_ISO15693_COMMAND, reqlen, fast, reply, req, reqlen);
 
-        if (!WaitForResponseTimeout(CMD_ACK, &resp, 2000)) {
+        if (WaitForResponseTimeout(CMD_HF_ISO15693_COMMAND, &resp, 2000) == false) {
             PrintAndLogEx(WARNING, "iso15693 timeout");
         } else {
             PrintAndLogEx(NORMAL, "");
 
-            status = resp.oldarg[0];
-            if (status < 2) {
+            
+            if (resp.length < 2) {
                 PrintAndLogEx(INFO, "  EAS (Electronic Article Surveillance) is not active");
             } else {
                 recv = resp.data.asBytes;
@@ -770,7 +769,7 @@ static int NxpSysInfo(uint8_t *uid) {
         clearCommandBuffer();
         SendCommandMIX(CMD_HF_ISO15693_COMMAND, reqlen, fast, reply, req, reqlen);
 
-        if (WaitForResponseTimeout(CMD_ACK, &resp, 2000) == false) {
+        if (WaitForResponseTimeout(CMD_HF_ISO15693_COMMAND, &resp, 2000) == false) {
             PrintAndLogEx(WARNING, "iso15693 timeout");
             DropField();
             return PM3_ETIMEOUT;
@@ -778,8 +777,7 @@ static int NxpSysInfo(uint8_t *uid) {
 
         DropField();
 
-        status = resp.oldarg[0];
-        if (status < 2) {
+        if (resp.length < 2) {
             PrintAndLogEx(WARNING, "iso15693 card doesn't answer to READ SIGNATURE command");
             return PM3_EWRONGANSWER;
         }
@@ -869,7 +867,7 @@ static int CmdHF15Info(const char *Cmd) {
     PacketResponseNG resp;
     clearCommandBuffer();
     SendCommandMIX(CMD_HF_ISO15693_COMMAND, reqlen, fast, read_response, req, reqlen);
-    if (WaitForResponseTimeout(CMD_ACK, &resp, 2000) == false) {
+    if (WaitForResponseTimeout(CMD_HF_ISO15693_COMMAND, &resp, 2000) == false) {
         PrintAndLogEx(WARNING, "iso15693 timeout");
         DropField();
         return PM3_ETIMEOUT;
@@ -877,12 +875,11 @@ static int CmdHF15Info(const char *Cmd) {
 
     DropField();
 
-    int status = resp.oldarg[0];
-    if (status == PM3_ETEAROFF) {
-        return status;
+    if (resp.status == PM3_ETEAROFF) {
+        return resp.status;
     }
-    if (status < 2) {
-        PrintAndLogEx(WARNING, "iso15693 card doesn't answer to systeminfo command (%d)", status);
+    if (resp.length < 2) {
+        PrintAndLogEx(WARNING, "iso15693 card doesn't answer to systeminfo command (%d)", resp.length);
         return PM3_EWRONGANSWER;
     }
 
@@ -899,7 +896,7 @@ static int CmdHF15Info(const char *Cmd) {
     PrintAndLogEx(INFO, "-------------------------------------------------------------");
     PrintAndLogEx(SUCCESS, "      TYPE: " _YELLOW_("%s"), getTagInfo_15(data + 2));
     PrintAndLogEx(SUCCESS, "       UID: " _GREEN_("%s"), iso15693_sprintUID(NULL, uid));
-    PrintAndLogEx(SUCCESS, "   SYSINFO: %s", sprint_hex(data, status - 2));
+    PrintAndLogEx(SUCCESS, "   SYSINFO: %s", sprint_hex(data, resp.length - 2));
 
     // DSFID
     if (data[1] & 0x01)
@@ -1155,16 +1152,15 @@ static int CmdHF15WriteAfi(const char *Cmd) {
     clearCommandBuffer();
     SendCommandMIX(CMD_HF_ISO15693_COMMAND, reqlen, fast, read_respone, req, reqlen);
 
-    if (WaitForResponseTimeout(CMD_ACK, &resp, 2000) == false) {
+    if (WaitForResponseTimeout(CMD_HF_ISO15693_COMMAND, &resp, 2000) == false) {
         PrintAndLogEx(ERR, "iso15693 timeout");
         DropField();
         return PM3_ETIMEOUT;
     }
     DropField();
 
-    int status = resp.oldarg[0];
-    if (status == PM3_ETEAROFF) {
-        return status;
+    if (resp.status == PM3_ETEAROFF) {
+        return resp.status;
     }
 
     uint8_t *data = resp.data.asBytes;
@@ -1254,16 +1250,15 @@ static int CmdHF15WriteDsfid(const char *Cmd) {
     clearCommandBuffer();
     SendCommandMIX(CMD_HF_ISO15693_COMMAND, reqlen, fast, read_respone, req, reqlen);
 
-    if (WaitForResponseTimeout(CMD_ACK, &resp, 2000) == false) {
+    if (WaitForResponseTimeout(CMD_HF_ISO15693_COMMAND, &resp, 2000) == false) {
         PrintAndLogEx(ERR, "iso15693 timeout");
         DropField();
         return PM3_ETIMEOUT;
     }
 
     DropField();
-    int status = resp.oldarg[0];
-    if (status == PM3_ETEAROFF) {
-        return status;
+    if (resp.status == PM3_ETEAROFF) {
+        return resp.status;
     }
 
     uint8_t *data = resp.data.asBytes;
@@ -1367,13 +1362,12 @@ static int CmdHF15Dump(const char *Cmd) {
         clearCommandBuffer();
         SendCommandMIX(CMD_HF_ISO15693_COMMAND, sizeof(req), fast, read_respone, req, sizeof(req));
 
-        if (WaitForResponseTimeout(CMD_ACK, &resp, 2000)) {
+        if (WaitForResponseTimeout(CMD_HF_ISO15693_COMMAND, &resp, 2000)) {
 
-            int len = resp.oldarg[0];
-            if (len == PM3_ETEAROFF) {
+            if (resp.status == PM3_ETEAROFF) {
                 continue;
             }
-            if (len < 2) {
+            if (resp.length < 2) {
                 PrintAndLogEx(NORMAL, "");
                 PrintAndLogEx(FAILED, "iso15693 command failed");
                 continue;
@@ -1381,7 +1375,7 @@ static int CmdHF15Dump(const char *Cmd) {
 
             uint8_t *recv = resp.data.asBytes;
 
-            if (CheckCrc15(recv, len) == false) {
+            if (CheckCrc15(recv, resp.length) == false) {
                 PrintAndLogEx(NORMAL, "");
                 PrintAndLogEx(FAILED, "crc (" _RED_("fail") ")");
                 continue;
@@ -1486,17 +1480,16 @@ static int CmdHF15Raw(const char *Cmd) {
     SendCommandMIX(CMD_HF_ISO15693_COMMAND, datalen, fast, read_respone, data, datalen);
 
     if (read_respone) {
-        if (WaitForResponseTimeout(CMD_ACK, &resp, 2000)) {
-            int len = resp.oldarg[0];
-            if (len == PM3_ETEAROFF) {
+        if (WaitForResponseTimeout(CMD_HF_ISO15693_COMMAND, &resp, 2000)) {
+            if (resp.status == PM3_ETEAROFF) {
                 DropField();
-                return len;
+                return resp.status;
             }
-            if (len < 2) {
+            if (resp.length < 2) {
                 PrintAndLogEx(WARNING, "command failed");
             } else {
-                PrintAndLogEx(SUCCESS, "received %i octets", len);
-                PrintAndLogEx(SUCCESS, "%s", sprint_hex(resp.data.asBytes, len));
+                PrintAndLogEx(SUCCESS, "received %i octets", resp.length);
+                PrintAndLogEx(SUCCESS, "%s", sprint_hex(resp.data.asBytes, resp.length));
             }
         } else {
             PrintAndLogEx(WARNING, "timeout while waiting for reply");
@@ -1592,7 +1585,7 @@ static int CmdHF15Readmulti(const char *Cmd) {
     clearCommandBuffer();
     SendCommandMIX(CMD_HF_ISO15693_COMMAND, reqlen, fast, read_respone, req, reqlen);
 
-    if (WaitForResponseTimeout(CMD_ACK, &resp, 2000) == false) {
+    if (WaitForResponseTimeout(CMD_HF_ISO15693_COMMAND, &resp, 2000) == false) {
         PrintAndLogEx(FAILED, "iso15693 card timeout");
         DropField();
         return PM3_ETIMEOUT;
@@ -1600,19 +1593,18 @@ static int CmdHF15Readmulti(const char *Cmd) {
 
     DropField();
 
-    int status = resp.oldarg[0];
-    if (status == PM3_ETEAROFF) {
-        return status;
+    if (resp.status == PM3_ETEAROFF) {
+        return resp.status;
     }
 
-    if (status < 2) {
+    if (resp.length < 2) {
         PrintAndLogEx(FAILED, "iso15693 card readmulti failed");
         return PM3_EWRONGANSWER;
     }
 
     uint8_t *data = resp.data.asBytes;
 
-    if (CheckCrc15(data, status) == false) {
+    if (CheckCrc15(data, resp.length) == false) {
         PrintAndLogEx(FAILED, "crc (" _RED_("fail") ")");
         return PM3_ESOFT;
     }
@@ -1725,7 +1717,7 @@ static int CmdHF15Readblock(const char *Cmd) {
     clearCommandBuffer();
     SendCommandMIX(CMD_HF_ISO15693_COMMAND, reqlen, fast, read_respone, req, reqlen);
 
-    if (WaitForResponseTimeout(CMD_ACK, &resp, 2000) == false) {
+    if (WaitForResponseTimeout(CMD_HF_ISO15693_COMMAND, &resp, 2000) == false) {
         PrintAndLogEx(ERR, "iso15693 timeout");
         DropField();
         return PM3_ETIMEOUT;
@@ -1733,18 +1725,17 @@ static int CmdHF15Readblock(const char *Cmd) {
 
     DropField();
 
-    int status = resp.oldarg[0];
-    if (status == PM3_ETEAROFF) {
-        return status;
+    if (resp.status == PM3_ETEAROFF) {
+        return resp.status;
     }
-    if (status < 2) {
+    if (resp.length < 2) {
         PrintAndLogEx(ERR, "iso15693 command failed");
         return PM3_EWRONGANSWER;
     }
 
     uint8_t *data = resp.data.asBytes;
 
-    if (CheckCrc15(data, status) == false) {
+    if (CheckCrc15(data, resp.length) == false) {
         PrintAndLogEx(FAILED, "crc (" _RED_("fail") ")");
         return PM3_ESOFT;
     }
@@ -1764,7 +1755,7 @@ static int CmdHF15Readblock(const char *Cmd) {
     PrintAndLogEx(NORMAL, "");
     PrintAndLogEx(INFO, "      #%3d  |lck| ascii", block);
     PrintAndLogEx(INFO, "------------+---+------");
-    PrintAndLogEx(INFO, "%s| %s | %s", sprint_hex(data + 2, status - 4), lck, sprint_ascii(data + 2, status - 4));
+    PrintAndLogEx(INFO, "%s| %s | %s", sprint_hex(data + 2, resp.length - 4), lck, sprint_ascii(data + 2, resp.length - 4));
     PrintAndLogEx(NORMAL, "");
     return PM3_SUCCESS;
 }
@@ -1775,19 +1766,18 @@ static int hf_15_write_blk(bool verbose, bool fast, uint8_t *req, uint8_t reqlen
     clearCommandBuffer();
     SendCommandMIX(CMD_HF_ISO15693_COMMAND, reqlen, fast, read_response, req, reqlen);
     PacketResponseNG resp;
-    if (WaitForResponseTimeout(CMD_ACK, &resp, 2000) == false) {
+    if (WaitForResponseTimeout(CMD_HF_ISO15693_COMMAND, &resp, 2000) == false) {
         PrintAndLogEx(FAILED, "iso15693 card timeout, data may be written anyway");
         DropField();
         return PM3_ETIMEOUT;
     }
 
     DropField();
-    int status = resp.oldarg[0];
-    if (status == PM3_ETEAROFF) {
-        return status;
+    if (resp.status == PM3_ETEAROFF) {
+        return resp.status;
     }
 
-    if (status < 2) {
+    if (resp.length < 2) {
         if (verbose) {
             PrintAndLogEx(FAILED, "iso15693 command failed");
         }
@@ -1795,7 +1785,7 @@ static int hf_15_write_blk(bool verbose, bool fast, uint8_t *req, uint8_t reqlen
     }
 
     uint8_t *recv = resp.data.asBytes;
-    if (CheckCrc15(recv, status) == false) {
+    if (CheckCrc15(recv, resp.length) == false) {
         if (verbose) {
             PrintAndLogEx(FAILED, "crc (" _RED_("fail") ")");
         }
