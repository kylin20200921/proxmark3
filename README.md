commit 95d1631bed566978752a36068b03d5a3c837daac
Author: iceman1001 <iceman@iuse.se>
Date:   Wed Mar 23 18:22:03 2022 +0100

    style

diff --git a/armsrc/fpgaloader.c b/armsrc/fpgaloader.c
index 80d793b82..a8abf0a7a 100644
--- a/armsrc/fpgaloader.c
+++ b/armsrc/fpgaloader.c
@@ -163,8 +163,8 @@ void FpgaSetupSsc(uint16_t fpga_mode) {
     // 8, 16 or 32 bits per transfer, no loopback, MSB first, 1 transfer per sync
     // pulse, no output sync
     if (((fpga_mode & FPGA_MAJOR_MODE_MASK) == FPGA_MAJOR_MODE_HF_READER ||
-         (fpga_mode & FPGA_MAJOR_MODE_MASK) == FPGA_MAJOR_MODE_HF_FSK_READER) &&
-        (FpgaGetCurrent() == FPGA_BITSTREAM_HF || FpgaGetCurrent() == FPGA_BITSTREAM_HF_15)) {
+            (fpga_mode & FPGA_MAJOR_MODE_MASK) == FPGA_MAJOR_MODE_HF_FSK_READER) &&
+            (FpgaGetCurrent() == FPGA_BITSTREAM_HF || FpgaGetCurrent() == FPGA_BITSTREAM_HF_15)) {
         AT91C_BASE_SSC->SSC_RFMR = SSC_FRAME_MODE_BITS_IN_WORD(16) | AT91C_SSC_MSBF | SSC_FRAME_MODE_WORDS_PER_TRANSFER(0);
     } else {
         AT91C_BASE_SSC->SSC_RFMR = SSC_FRAME_MODE_BITS_IN_WORD(8) | AT91C_SSC_MSBF | SSC_FRAME_MODE_WORDS_PER_TRANSFER(0);
diff --git a/armsrc/iso15693.c b/armsrc/iso15693.c
index b5e67234a..ac81b494c 100644
--- a/armsrc/iso15693.c
+++ b/armsrc/iso15693.c
@@ -452,7 +452,7 @@ static RAMFUNC int Handle15693SamplesFromTag(uint16_t amplitude, DecodeTag_t *ta
                 if (tag->posCount > 2) {
                     tag->threshold_half += amplitude; // keep track of average high value
                 }
-                if (tag->posCount == (recv_speed?10:40)) {
+                if (tag->posCount == (recv_speed ? 10 : 40)) {
                     tag->threshold_half >>= 2; // (4 times 1/2 average)
                     tag->state = STATE_TAG_SOF_HIGH_END;
                 }
@@ -466,7 +466,7 @@ static RAMFUNC int Handle15693SamplesFromTag(uint16_t amplitude, DecodeTag_t *ta
 
         case STATE_TAG_SOF_HIGH_END: {
             // check for falling edge
-            if (tag->posCount == (recv_speed?13:52) && amplitude < tag->threshold_sof) {
+            if (tag->posCount == (recv_speed ? 13 : 52) && amplitude < tag->threshold_sof) {
                 tag->lastBit = SOF_PART1;  // detected 1st part of SOF (12 samples low and 12 samples high)
                 tag->shiftReg = 0;
                 tag->bitCount = 0;
@@ -478,7 +478,7 @@ static RAMFUNC int Handle15693SamplesFromTag(uint16_t amplitude, DecodeTag_t *ta
                 LED_C_ON();
             } else {
                 tag->posCount++;
-                if (tag->posCount > (recv_speed?13:52)) { // high phase too long
+                if (tag->posCount > (recv_speed ? 13 : 52)) { // high phase too long
                     tag->posCount = 0;
                     tag->previous_amplitude = amplitude;
                     tag->state = STATE_TAG_SOF_LOW;
@@ -494,13 +494,13 @@ static RAMFUNC int Handle15693SamplesFromTag(uint16_t amplitude, DecodeTag_t *ta
                 tag->sum2 = 0;
             }
 
-            if (tag->posCount <= (recv_speed?4:16)) {
+            if (tag->posCount <= (recv_speed ? 4 : 16)) {
                 tag->sum1 += amplitude;
             } else {
                 tag->sum2 += amplitude;
             }
 
-            if (tag->posCount == (recv_speed?8:32)) {
+            if (tag->posCount == (recv_speed ? 8 : 32)) {
                 if (tag->sum1 > tag->threshold_half && tag->sum2 > tag->threshold_half) { // modulation in both halves
                     if (tag->lastBit == LOGIC0) {  // this was already part of EOF
                         tag->state = STATE_TAG_EOF;
@@ -581,13 +581,13 @@ static RAMFUNC int Handle15693SamplesFromTag(uint16_t amplitude, DecodeTag_t *ta
                 tag->sum2 = 0;
             }
 
-            if (tag->posCount <= (recv_speed?4:16)) {
+            if (tag->posCount <= (recv_speed ? 4 : 16)) {
                 tag->sum1 += amplitude;
             } else {
                 tag->sum2 += amplitude;
             }
 
-            if (tag->posCount == (recv_speed?8:32)) {
+            if (tag->posCount == (recv_speed ? 8 : 32)) {
                 if (tag->sum1 > tag->threshold_half && tag->sum2 < tag->threshold_half) { // modulation in first half
                     tag->posCount = 0;
                     tag->state = STATE_TAG_EOF_TAIL;
@@ -608,13 +608,13 @@ static RAMFUNC int Handle15693SamplesFromTag(uint16_t amplitude, DecodeTag_t *ta
                 tag->sum2 = 0;
             }
 
-            if (tag->posCount <= (recv_speed?4:16)) {
+            if (tag->posCount <= (recv_speed ? 4 : 16)) {
                 tag->sum1 += amplitude;
             } else {
                 tag->sum2 += amplitude;
             }
 
-            if (tag->posCount == (recv_speed?8:32)) {
+            if (tag->posCount == (recv_speed ? 8 : 32)) {
                 if (tag->sum1 < tag->threshold_half && tag->sum2 < tag->threshold_half) { // no modulation in both halves
                     LED_C_OFF();
                     return true;
@@ -664,145 +664,131 @@ static void DecodeTagInit(DecodeTag_t *tag, uint8_t *data, uint16_t max_len) {
 #define MAX_COUNT(c, s)   ((s) ? (c >= 13) : (c >= 52))
 
 typedef struct DecodeTagFSK {
-	enum {
+    enum {
         STATE_FSK_ERROR,
-		STATE_FSK_BEFORE_SOF,
-		STATE_FSK_SOF_484,
-		STATE_FSK_SOF_424,
-		STATE_FSK_SOF_END_484,
+        STATE_FSK_BEFORE_SOF,
+        STATE_FSK_SOF_484,
+        STATE_FSK_SOF_424,
+        STATE_FSK_SOF_END_484,
         STATE_FSK_SOF_END_424,
-		STATE_FSK_RECEIVING_DATA_484,
-		STATE_FSK_RECEIVING_DATA_424,
-		STATE_FSK_EOF
-	}        state;
-	enum {
-		LOGIC0_PART1,
-		LOGIC1_PART1,
-		LOGIC0_PART2,
-		LOGIC1_PART2,
+        STATE_FSK_RECEIVING_DATA_484,
+        STATE_FSK_RECEIVING_DATA_424,
+        STATE_FSK_EOF
+    }        state;
+    enum {
+        LOGIC0_PART1,
+        LOGIC1_PART1,
+        LOGIC0_PART2,
+        LOGIC1_PART2,
         SOF
-	}        lastBit;
-	uint8_t  count;
-	uint8_t  bitCount;
-	uint8_t  shiftReg;
-	uint16_t len;
-	uint16_t max_len;
-	uint8_t  *output;
+    }        lastBit;
+    uint8_t  count;
+    uint8_t  bitCount;
+    uint8_t  shiftReg;
+    uint16_t len;
+    uint16_t max_len;
+    uint8_t  *output;
 } DecodeTagFSK_t;
 
 static void DecodeTagFSKReset(DecodeTagFSK_t *DecodeTag) {
-	DecodeTag->state = STATE_FSK_BEFORE_SOF;
-	DecodeTag->bitCount = 0;
-	DecodeTag->len = 0;
-	DecodeTag->shiftReg = 0;
+    DecodeTag->state = STATE_FSK_BEFORE_SOF;
+    DecodeTag->bitCount = 0;
+    DecodeTag->len = 0;
+    DecodeTag->shiftReg = 0;
 }
 
 static void DecodeTagFSKInit(DecodeTagFSK_t *DecodeTag, uint8_t *data, uint16_t max_len) {
-	DecodeTag->output = data;
-	DecodeTag->max_len = max_len;
-	DecodeTagFSKReset(DecodeTag);
+    DecodeTag->output = data;
+    DecodeTag->max_len = max_len;
+    DecodeTagFSKReset(DecodeTag);
 }
 
 // Performances of this function are crutial for stability
 // as it is called in real time for every samples
-static int RAMFUNC Handle15693FSKSamplesFromTag(uint8_t freq, DecodeTagFSK_t *DecodeTag, bool recv_speed)
-{
-	switch(DecodeTag->state) {
-		case STATE_FSK_BEFORE_SOF:
-			if (FREQ_IS_484(freq))
-			{ // possible SOF starting
-				DecodeTag->state = STATE_FSK_SOF_484;
-				DecodeTag->lastBit = LOGIC0_PART1;
-				DecodeTag->count = 1;
-			}
-			break;
-
-		case STATE_FSK_SOF_484:
-            //DbpString("STATE_FSK_SOF_484");
-			if (FREQ_IS_424(freq) && SEOF_COUNT(DecodeTag->count, recv_speed))
-			{ // SOF part1 continue at 424
-				DecodeTag->state = STATE_FSK_SOF_424;
-				DecodeTag->count = 1;
-			}
-            else if (FREQ_IS_484(freq) && !MAX_COUNT(DecodeTag->count, recv_speed)) // still in SOF at 484
-            {
-				DecodeTag->count++;
+static int RAMFUNC Handle15693FSKSamplesFromTag(uint8_t freq, DecodeTagFSK_t *DecodeTag, bool recv_speed) {
+    switch (DecodeTag->state) {
+        case STATE_FSK_BEFORE_SOF:
+            if (FREQ_IS_484(freq)) {
+                // possible SOF starting
+                DecodeTag->state = STATE_FSK_SOF_484;
+                DecodeTag->lastBit = LOGIC0_PART1;
+                DecodeTag->count = 1;
             }
-			else // SOF failed, roll back
-            {
-				DecodeTag->state = STATE_FSK_BEFORE_SOF;
+            break;
+
+        case STATE_FSK_SOF_484:
+            //DbpString("STATE_FSK_SOF_484");
+            if (FREQ_IS_424(freq) && SEOF_COUNT(DecodeTag->count, recv_speed)) {
+                // SOF part1 continue at 424
+                DecodeTag->state = STATE_FSK_SOF_424;
+                DecodeTag->count = 1;
+            } else if (FREQ_IS_484(freq) && !MAX_COUNT(DecodeTag->count, recv_speed)) { // still in SOF at 484
+                DecodeTag->count++;
+            } else { // SOF failed, roll back
+                DecodeTag->state = STATE_FSK_BEFORE_SOF;
             }
-			break;
+            break;
 
-		case STATE_FSK_SOF_424:
+        case STATE_FSK_SOF_424:
             //DbpString("STATE_FSK_SOF_424");
-            if (FREQ_IS_484(freq) && SEOF_COUNT(DecodeTag->count, recv_speed))
-			{ // SOF part 1 finished
-				DecodeTag->state = STATE_FSK_SOF_END_484;
-				DecodeTag->count = 1;
-			}
-			else if (FREQ_IS_424(freq) && !MAX_COUNT(DecodeTag->count, recv_speed)) // still in SOF at 424
-				DecodeTag->count++;
-			else // SOF failed, roll back
-            {
+            if (FREQ_IS_484(freq) && SEOF_COUNT(DecodeTag->count, recv_speed)) {
+                // SOF part 1 finished
+                DecodeTag->state = STATE_FSK_SOF_END_484;
+                DecodeTag->count = 1;
+            } else if (FREQ_IS_424(freq) && !MAX_COUNT(DecodeTag->count, recv_speed)) // still in SOF at 424
+                DecodeTag->count++;
+            else { // SOF failed, roll back
 #ifdef DEBUG
                 if (DEBUG)
                     Dbprintf("SOF_424 failed: freq=%d, count=%d, recv_speed=%d", freq, DecodeTag->count, recv_speed);
 #endif
-				DecodeTag->state = STATE_FSK_BEFORE_SOF;
+                DecodeTag->state = STATE_FSK_BEFORE_SOF;
             }
-			break;
+            break;
 
-		case STATE_FSK_SOF_END_484:
-            if (FREQ_IS_424(freq) && LOGIC_COUNT(DecodeTag->count, recv_speed))
-			{
+        case STATE_FSK_SOF_END_484:
+            if (FREQ_IS_424(freq) && LOGIC_COUNT(DecodeTag->count, recv_speed)) {
                 DecodeTag->state = STATE_FSK_SOF_END_424;
-				DecodeTag->count = 1;
-			}
-            else if (FREQ_IS_484(freq) && !MAX_COUNT(DecodeTag->count, recv_speed)) // still in SOF_END_484
-				DecodeTag->count++;
-            else // SOF failed, roll back
-            {
+                DecodeTag->count = 1;
+            } else if (FREQ_IS_484(freq) && !MAX_COUNT(DecodeTag->count, recv_speed)) // still in SOF_END_484
+                DecodeTag->count++;
+            else { // SOF failed, roll back
 #ifdef DEBUG
                 if (DEBUG)
                     Dbprintf("SOF_END_484 failed: freq=%d, count=%d, recv_speed=%d", freq, DecodeTag->count, recv_speed);
 #endif
-				DecodeTag->state = STATE_FSK_BEFORE_SOF;
+                DecodeTag->state = STATE_FSK_BEFORE_SOF;
             }
-			break;
+            break;
         case STATE_FSK_SOF_END_424:
-            if (FREQ_IS_484(freq) && LOGIC_COUNT(DecodeTag->count, recv_speed))
-			{ // SOF finished at 484
-				DecodeTag->count = 1;
+            if (FREQ_IS_484(freq) && LOGIC_COUNT(DecodeTag->count, recv_speed)) {
+                // SOF finished at 484
+                DecodeTag->count = 1;
                 DecodeTag->lastBit = SOF;
-				DecodeTag->state = STATE_FSK_RECEIVING_DATA_484;
+                DecodeTag->state = STATE_FSK_RECEIVING_DATA_484;
                 LED_C_ON();
-			}
-			else if (FREQ_IS_424(freq) && LOGIC_COUNT(DecodeTag->count-2, recv_speed))
-			{ // SOF finished at 424 (wait count+2 to be sure that next freq is 424)
-				DecodeTag->count = 2;
+            } else if (FREQ_IS_424(freq) && LOGIC_COUNT(DecodeTag->count - 2, recv_speed)) {
+                // SOF finished at 424 (wait count+2 to be sure that next freq is 424)
+                DecodeTag->count = 2;
                 DecodeTag->lastBit = SOF;
                 DecodeTag->state = STATE_FSK_RECEIVING_DATA_424;
                 LED_C_ON();
-			}
-			else if (FREQ_IS_424(freq) && !MAX_COUNT(DecodeTag->count, recv_speed)) // still in SOF_END_424
+            } else if (FREQ_IS_424(freq) && !MAX_COUNT(DecodeTag->count, recv_speed)) // still in SOF_END_424
                 DecodeTag->count++;
-			else // SOF failed, roll back
-            {
+            else { // SOF failed, roll back
 #ifdef DEBUG
                 if (DEBUG)
                     Dbprintf("SOF_END_424 failed: freq=%d, count=%d, recv_speed=%d", freq, DecodeTag->count, recv_speed);
 #endif
-				DecodeTag->state = STATE_FSK_BEFORE_SOF;
+                DecodeTag->state = STATE_FSK_BEFORE_SOF;
             }
             break;
 
 
-		case STATE_FSK_RECEIVING_DATA_424:
-            if (FREQ_IS_484(freq) && LOGIC_COUNT(DecodeTag->count, recv_speed))
-			{
-                if (DecodeTag->lastBit == LOGIC1_PART1)
-                { // logic 1 finished, goto 484
+        case STATE_FSK_RECEIVING_DATA_424:
+            if (FREQ_IS_484(freq) && LOGIC_COUNT(DecodeTag->count, recv_speed)) {
+                if (DecodeTag->lastBit == LOGIC1_PART1) {
+                    // logic 1 finished, goto 484
                     DecodeTag->lastBit = LOGIC1_PART2;
 
                     DecodeTag->shiftReg >>= 1;
@@ -818,17 +804,15 @@ static int RAMFUNC Handle15693FSKSamplesFromTag(uint8_t freq, DecodeTagFSK_t *De
                         DecodeTag->bitCount = 0;
                         DecodeTag->shiftReg = 0;
                     }
-                }
-                else
-                { // end of LOGIC0_PART1
+                } else {
+                    // end of LOGIC0_PART1
                     DecodeTag->lastBit = LOGIC0_PART1;
                 }
                 DecodeTag->count = 1;
                 DecodeTag->state = STATE_FSK_RECEIVING_DATA_484;
-            }
-            else if (FREQ_IS_424(freq) && LOGIC_COUNT(DecodeTag->count-2, recv_speed) &&
-                     DecodeTag->lastBit == LOGIC1_PART1)
-			{ // logic 1 finished, stay in 484
+            } else if (FREQ_IS_424(freq) && LOGIC_COUNT(DecodeTag->count - 2, recv_speed) &&
+                       DecodeTag->lastBit == LOGIC1_PART1) {
+                // logic 1 finished, stay in 484
                 DecodeTag->lastBit = LOGIC1_PART2;
 
                 DecodeTag->shiftReg >>= 1;
@@ -844,39 +828,35 @@ static int RAMFUNC Handle15693FSKSamplesFromTag(uint8_t freq, DecodeTagFSK_t *De
                     DecodeTag->bitCount = 0;
                     DecodeTag->shiftReg = 0;
                 }
-				DecodeTag->count = 2;
- 			}
-            else if (FREQ_IS_424(freq) && !MAX_COUNT(DecodeTag->count, recv_speed)) // still at 424
-				DecodeTag->count++;
-
-			else if (FREQ_IS_484(freq) && DecodeTag->lastBit == LOGIC0_PART2 &&
-					 SEOF_COUNT(DecodeTag->count, recv_speed))
-			{ // EOF has started
+                DecodeTag->count = 2;
+            } else if (FREQ_IS_424(freq) && !MAX_COUNT(DecodeTag->count, recv_speed)) // still at 424
+                DecodeTag->count++;
+
+            else if (FREQ_IS_484(freq) && DecodeTag->lastBit == LOGIC0_PART2 &&
+                     SEOF_COUNT(DecodeTag->count, recv_speed)) {
+                // EOF has started
 #ifdef DEBUG
                 if (DEBUG)
                     Dbprintf("RECEIVING_DATA_424->EOF: freq=%d, count=%d, recv_speed=%d, lastbit=%d, state=%d", freq, DecodeTag->count, recv_speed, DecodeTag->lastBit, DecodeTag->state);
 #endif
-				DecodeTag->count = 1;
-				DecodeTag->state = STATE_FSK_EOF;
-				LED_C_OFF();
-			}
-			else // error
-			{
+                DecodeTag->count = 1;
+                DecodeTag->state = STATE_FSK_EOF;
+                LED_C_OFF();
+            } else { // error
 #ifdef DEBUG
                 if (DEBUG)
                     Dbprintf("RECEIVING_DATA_424 error: freq=%d, count=%d, recv_speed=%d, lastbit=%d, state=%d", freq, DecodeTag->count, recv_speed, DecodeTag->lastBit, DecodeTag->state);
 #endif
-				DecodeTag->state = STATE_FSK_ERROR;
-				LED_C_OFF();
-				return true;
-			}
-			break;
-
-		case STATE_FSK_RECEIVING_DATA_484:
-			if (FREQ_IS_424(freq) && LOGIC_COUNT(DecodeTag->count, recv_speed))
-			{
-                if (DecodeTag->lastBit == LOGIC0_PART1)
-                { // logic 0 finished, goto 424
+                DecodeTag->state = STATE_FSK_ERROR;
+                LED_C_OFF();
+                return true;
+            }
+            break;
+
+        case STATE_FSK_RECEIVING_DATA_484:
+            if (FREQ_IS_424(freq) && LOGIC_COUNT(DecodeTag->count, recv_speed)) {
+                if (DecodeTag->lastBit == LOGIC0_PART1) {
+                    // logic 0 finished, goto 424
                     DecodeTag->lastBit = LOGIC0_PART2;
 
                     DecodeTag->shiftReg >>= 1;
@@ -891,17 +871,15 @@ static int RAMFUNC Handle15693FSKSamplesFromTag(uint8_t freq, DecodeTagFSK_t *De
                         DecodeTag->bitCount = 0;
                         DecodeTag->shiftReg = 0;
                     }
-                }
-                else
-                { // end of LOGIC1_PART1
+                } else {
+                    // end of LOGIC1_PART1
                     DecodeTag->lastBit = LOGIC1_PART1;
                 }
                 DecodeTag->count = 1;
                 DecodeTag->state = STATE_FSK_RECEIVING_DATA_424;
-            }
-			else if (FREQ_IS_484(freq) && LOGIC_COUNT(DecodeTag->count-2, recv_speed) &&
-                     DecodeTag->lastBit == LOGIC0_PART1)
-			{ // logic 0 finished, stay in 424
+            } else if (FREQ_IS_484(freq) && LOGIC_COUNT(DecodeTag->count - 2, recv_speed) &&
+                       DecodeTag->lastBit == LOGIC0_PART1) {
+                // logic 0 finished, stay in 424
                 DecodeTag->lastBit = LOGIC0_PART2;
 
                 DecodeTag->shiftReg >>= 1;
@@ -916,49 +894,44 @@ static int RAMFUNC Handle15693FSKSamplesFromTag(uint8_t freq, DecodeTagFSK_t *De
                     DecodeTag->bitCount = 0;
                     DecodeTag->shiftReg = 0;
                 }
-				DecodeTag->count = 2;
- 			}
-			else if (FREQ_IS_484(freq) && !MAX_COUNT(DecodeTag->count, recv_speed)) // still at 484
-				DecodeTag->count++;
-            else // error
-			{
+                DecodeTag->count = 2;
+            } else if (FREQ_IS_484(freq) && !MAX_COUNT(DecodeTag->count, recv_speed)) // still at 484
+                DecodeTag->count++;
+            else { // error
 #ifdef DEBUG
                 if (DEBUG)
                     Dbprintf("RECEIVING_DATA_484 error: freq=%d, count=%d, recv_speed=%d, lastbit=%d, state=%d", freq, DecodeTag->count, recv_speed, DecodeTag->lastBit, DecodeTag->state);
 #endif
-				LED_C_OFF();
-				DecodeTag->state = STATE_FSK_ERROR;
-				return true;
-			}
-			break;
-
-		case STATE_FSK_EOF:
-			if (FREQ_IS_484(freq) && !MAX_COUNT(DecodeTag->count, recv_speed)) // still at 484
-			{
-				DecodeTag->count++;
-				if (SEOF_COUNT(DecodeTag->count, recv_speed))
-					return true; // end of the transmission
-			}
-			else // error
-			{
+                LED_C_OFF();
+                DecodeTag->state = STATE_FSK_ERROR;
+                return true;
+            }
+            break;
+
+        case STATE_FSK_EOF:
+            if (FREQ_IS_484(freq) && !MAX_COUNT(DecodeTag->count, recv_speed)) { // still at 484
+                DecodeTag->count++;
+                if (SEOF_COUNT(DecodeTag->count, recv_speed))
+                    return true; // end of the transmission
+            } else { // error
 #ifdef DEBUG
                 if (DEBUG)
                     Dbprintf("EOF error: freq=%d, count=%d, recv_speed=%d", freq, DecodeTag->count, recv_speed);
 #endif
-				DecodeTag->state = STATE_FSK_ERROR;
-				return true;
-			}
-			break;
-		case STATE_FSK_ERROR:
-			LED_C_OFF();
+                DecodeTag->state = STATE_FSK_ERROR;
+                return true;
+            }
+            break;
+        case STATE_FSK_ERROR:
+            LED_C_OFF();
 #ifdef DEBUG
             if (DEBUG)
-                    Dbprintf("FSK error: freq=%d, count=%d, recv_speed=%d", freq, DecodeTag->count, recv_speed);
+                Dbprintf("FSK error: freq=%d, count=%d, recv_speed=%d", freq, DecodeTag->count, recv_speed);
 #endif
-			return true; // error
-			break;
-	}
-	return false;
+            return true; // error
+            break;
+    }
+    return false;
 }
 
 /*
@@ -1108,7 +1081,7 @@ int GetIso15693AnswerFromTag(uint8_t *response, uint16_t max_len, uint16_t timeo
                      dtf->bitCount,
                      dtf->count,
                      dtf->max_len
-                );
+                    );
             Dbprintf("timing: sof_time = %d, eof_time = %d", (sof_time * 4), (*eof_time * 4));
         }
     } else {
@@ -1125,7 +1098,7 @@ int GetIso15693AnswerFromTag(uint8_t *response, uint16_t max_len, uint16_t timeo
                      dt->bitCount,
                      dt->posCount,
                      dt->max_len
-                );
+                    );
             Dbprintf("timing: sof_time = %d, eof_time = %d", (sof_time * 4), (*eof_time * 4));
         }
     }
@@ -1652,7 +1625,7 @@ void SniffIso15693(uint8_t jam_search_len, uint8_t *jam_search_string, bool icla
 
     DecodeTagFSK_t dtagfsk = {0};
     uint8_t response2[ISO15693_MAX_RESPONSE_LENGTH] = {0};
-	DecodeTagFSKInit(&dtagfsk, response2, sizeof(response2));
+    DecodeTagFSKInit(&dtagfsk, response2, sizeof(response2));
 
     DecodeReader_t dreader = {0};
     uint8_t cmd[ISO15693_MAX_COMMAND_LENGTH] = {0};
@@ -1744,8 +1717,7 @@ void SniffIso15693(uint8_t jam_search_len, uint8_t *jam_search_string, bool icla
                                         - 16 * 16; // time for EOF transfer
                     LogTrace_ISO15693(dreader.output, dreader.byteCount, (sof_time * 4), (eof_time * 4), NULL, true);
 
-                    if (!iclass) // Those flags don't exist in iClass
-                    {
+                    if (!iclass) { // Those flags don't exist in iClass
                         expect_fsk_answer = dreader.output[0] & ISO15_REQ_SUBCARRIER_TWO;
                         expect_fast_answer = dreader.output[0] & ISO15_REQ_DATARATE_HIGH;
                     }
@@ -1765,8 +1737,7 @@ void SniffIso15693(uint8_t jam_search_len, uint8_t *jam_search_string, bool icla
                                         - 32 * 16  // time for SOF transfer
                                         - 16 * 16; // time for EOF transfer
                     LogTrace_ISO15693(dreader.output, dreader.byteCount, (sof_time * 4), (eof_time * 4), NULL, true);
-                    if (!iclass) // Those flags don't exist in iClass
-                    {
+                    if (!iclass) { // Those flags don't exist in iClass
                         expect_fsk_answer = dreader.output[0] & ISO15_REQ_SUBCARRIER_TWO;
                         expect_fast_answer = dreader.output[0] & ISO15_REQ_DATARATE_HIGH;
                     }
@@ -1784,8 +1755,7 @@ void SniffIso15693(uint8_t jam_search_len, uint8_t *jam_search_string, bool icla
 
         if (!reader_is_active && expect_tag_answer) {                       // no need to try decoding tag data if the reader is currently sending or no answer expected yet
 
-            if (!expect_fsk_answer)
-            {
+            if (!expect_fsk_answer) {
                 if (Handle15693SamplesFromTag((sniffdata >> 4) << 2, &dtag, expect_fast_answer)) {
 
                     uint32_t eof_time = dma_start_time + (samples * 16) - DELAY_TAG_TO_ARM_SNIFF; // end of EOF
@@ -1807,30 +1777,26 @@ void SniffIso15693(uint8_t jam_search_len, uint8_t *jam_search_string, bool icla
                 } else {
                     tag_is_active = (dtag.state >= STATE_TAG_RECEIVING_DATA);
                 }
-            }
-            else
-            {
+            } else {
                 if (FREQ_IS_0((sniffdata >> 2) & 0x3)) // tolerate 1 00
                     sniffdata = sniffdata_prev;
 
                 if (Handle15693FSKSamplesFromTag((sniffdata >> 2) & 0x3, &dtagfsk, expect_fast_answer)) {
                     expect_fsk_answer = false;
-                }
-                else {
+                } else {
                     tag_is_active = (dtagfsk.state >= STATE_FSK_RECEIVING_DATA_484);
                 }
-                if (!expect_fsk_answer)
-                { // FSK answer no more expected: switch back to ASK
-                    if (dtagfsk.len > 0)
-                    {
+                if (!expect_fsk_answer) {
+                    // FSK answer no more expected: switch back to ASK
+                    if (dtagfsk.len > 0) {
                         uint32_t eof_time = dma_start_time + (samples * 16) - DELAY_TAG_TO_ARM_SNIFF; // end of EOF
                         if (dtagfsk.lastBit == SOF) {
                             eof_time -= (8 * 16); // needed 8 additional samples to confirm single SOF (iCLASS)
                         }
                         uint32_t sof_time = eof_time
-                            - dtagfsk.len * 8 * 8 * 16 // time for byte transfers
-                            - (32 * 16)  // time for SOF transfer
-                            - (dtagfsk.lastBit != SOF ? (32 * 16) : 0); // time for EOF transfer
+                                            - dtagfsk.len * 8 * 8 * 16 // time for byte transfers
+                                            - (32 * 16)  // time for SOF transfer
+                                            - (dtagfsk.lastBit != SOF ? (32 * 16) : 0); // time for EOF transfer
 
                         LogTrace_ISO15693(dtagfsk.output, dtagfsk.len, (sof_time * 4), (eof_time * 4), NULL, false);
                     }
@@ -2055,7 +2021,7 @@ void ReaderIso15693(iso15_card_select_t *p_card) {
     BuildIdentifyRequest(cmd);
     uint32_t start_time = 0;
     uint32_t eof_time;
-    uint16_t recvlen = 0; 
+    uint16_t recvlen = 0;
     int res = SendDataTag(cmd, sizeof(cmd), true, true, answer, ISO15693_MAX_RESPONSE_LENGTH, start_time, ISO15693_READER_TIMEOUT, &eof_time, &recvlen);
 
     if (res == PM3_ETEAROFF) { // tearoff occurred
@@ -2432,7 +2398,7 @@ void LockPassSlixIso15693(uint32_t pass_id, uint32_t password) {
     //uint8_t cmd_write_pass[] = {ISO15693_REQ_DATARATE_HIGH | ISO15693_REQ_ADDRESS, 0xB4, 0x04, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x04, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00 };
     uint8_t cmd_lock_pass[] = {ISO15693_REQ_DATARATE_HIGH | ISO15693_REQ_ADDRESS, 0xB5, 0x04, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x04, 0x00, 0x00 };
     uint16_t crc;
-    uint16_t recvlen = 0;    
+    uint16_t recvlen = 0;
     uint8_t recvbuf[ISO15693_MAX_RESPONSE_LENGTH];
     uint32_t start_time = 0;
     bool done = false;
@@ -2572,16 +2538,16 @@ void SetTag15693Uid(const uint8_t *uid) {
     int res = PM3_SUCCESS;
     for (int i = 0; i < 4; i++) {
         res = SendDataTag(
-            cmd[i],
-            sizeof(cmd[i]),
-            (i == 0) ? true : false,
-            true,
-            recvbuf,
-            sizeof(recvbuf),
-            start_time,
-            ISO15693_READER_TIMEOUT_WRITE,
-            &eof_time,
-            &recvlen);
+                  cmd[i],
+                  sizeof(cmd[i]),
+                  (i == 0) ? true : false,
+                  true,
+                  recvbuf,
+                  sizeof(recvbuf),
+                  start_time,
+                  ISO15693_READER_TIMEOUT_WRITE,
+                  &eof_time,
+                  &recvlen);
         start_time = eof_time + DELAY_ISO15693_VICC_TO_VCD_READER;
     }
 
diff --git a/armsrc/iso15693.h b/armsrc/iso15693.h
index a984a5f87..277074189 100644
--- a/armsrc/iso15693.h
+++ b/armsrc/iso15693.h
@@ -45,10 +45,10 @@ int GetIso15693AnswerFromTag(uint8_t *response, uint16_t max_len, uint16_t timeo
 
 //void RecordRawAdcSamplesIso15693(void);
 void AcquireRawAdcSamplesIso15693(void);
-void ReaderIso15693(iso15_card_select_t *p_card); // ISO15693 reader 
+void ReaderIso15693(iso15_card_select_t *p_card); // ISO15693 reader
 void SimTagIso15693(uint8_t *uid); // simulate an ISO15693 tag
-void BruteforceIso15693Afi(uint32_t speed); // find an AFI of a tag 
-void DirectTag15693Command(uint32_t datalen, uint32_t speed, uint32_t recv, uint8_t *data); // send arbitrary commands from CLI 
+void BruteforceIso15693Afi(uint32_t speed); // find an AFI of a tag
+void DirectTag15693Command(uint32_t datalen, uint32_t speed, uint32_t recv, uint8_t *data); // send arbitrary commands from CLI
 
 void SniffIso15693(uint8_t jam_search_len, uint8_t *jam_search_string, bool iclass);
 
diff --git a/client/luascripts/hf_14a_i2crevive.lua b/client/luascripts/hf_14a_i2crevive.lua
index a33a4ca5b..c26bf4cad 100644
--- a/client/luascripts/hf_14a_i2crevive.lua
+++ b/client/luascripts/hf_14a_i2crevive.lua
@@ -59,7 +59,7 @@ function main(args)
 
     local i
     local cmds = {}
-	--check for params 
+	--check for params
     for o, a in getopt.getopt(args, 'h') do
         if o == 'h' then return help() end
     end
diff --git a/client/src/cmddata.c b/client/src/cmddata.c
index 0961c6853..81ab70c87 100644
--- a/client/src/cmddata.c
+++ b/client/src/cmddata.c
@@ -2888,16 +2888,16 @@ static int CmdDiff(const char *Cmd) {
 
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "data diff",
-                    "Diff takes a multitude of input data and makes a binary compare.\n"
-                    "It accepts filenames (filesystem or RDV4 flashmem SPIFFS), emulator memory, magic gen1",
-                    "data diff -w 4 -a hf-mfu-01020304.bin -b hf-mfu-04030201.bin\n"
-                    "data diff -a fileA -b fileB\n"
-                    "data diff -a fileA --eb\n"
+                  "Diff takes a multitude of input data and makes a binary compare.\n"
+                  "It accepts filenames (filesystem or RDV4 flashmem SPIFFS), emulator memory, magic gen1",
+                  "data diff -w 4 -a hf-mfu-01020304.bin -b hf-mfu-04030201.bin\n"
+                  "data diff -a fileA -b fileB\n"
+                  "data diff -a fileA --eb\n"
 //                    "data diff -a fileA --cb\n"
-                    "data diff --fa fileA -b fileB\n"
-                    "data diff --fa fileA --fb fileB\n"
-                    "data diff --ea --cb\n"
-                );
+                  "data diff --fa fileA -b fileB\n"
+                  "data diff --fa fileA --fb fileB\n"
+                  "data diff --ea --cb\n"
+                 );
 
     void *argtable[] = {
         arg_param_begin,
@@ -2937,7 +2937,7 @@ static int CmdDiff(const char *Cmd) {
     CLIParserFree(ctx);
 
     // sanity check
-    if (IfPm3Rdv4Fw() == false && (splenA > 0 || splenB > 0) ) {
+    if (IfPm3Rdv4Fw() == false && (splenA > 0 || splenB > 0)) {
         PrintAndLogEx(WARNING, "No RDV4 Flashmemory available");
         return PM3_EINVARG;
     }
@@ -2952,7 +2952,7 @@ static int CmdDiff(const char *Cmd) {
     }
 
     //
-    if (width > 16 || width < 1 ) {
+    if (width > 16 || width < 1) {
         PrintAndLogEx(INFO, "Width out of range, using default 16 bytes width");
         width = 16;
     }
@@ -3111,19 +3111,19 @@ static int CmdDiff(const char *Cmd) {
 
     // print data diff loop
     int i;
-    for (i = 0; i < n;  i += width ) {
+    for (i = 0; i < n;  i += width) {
 
         memset(line, 0, sizeof(line));
 
-        int diff = memcmp(inA + i, inB + i, width);        
+        int diff = memcmp(inA + i, inB + i, width);
 
         // if ok,  just print
         if (diff == 0) {
-            hex_to_buffer((uint8_t*)line, inA + i, width, width, 0, 1, true);
-            ascii_to_buffer((uint8_t*)(line + strlen(line)), inA + i, width, width, 0);
+            hex_to_buffer((uint8_t *)line, inA + i, width, width, 0, 1, true);
+            ascii_to_buffer((uint8_t *)(line + strlen(line)), inA + i, width, width, 0);
             strcat(line + strlen(line), " | ");
-            hex_to_buffer((uint8_t*)(line + strlen(line)), inB + i, width, width, 0, 1, true);
-            ascii_to_buffer((uint8_t*)(line + strlen(line)), inB + i, width, width, 0);
+            hex_to_buffer((uint8_t *)(line + strlen(line)), inB + i, width, width, 0, 1, true);
+            ascii_to_buffer((uint8_t *)(line + strlen(line)), inB + i, width, width, 0);
         } else {
 
             char dlnA[240] = {0};
@@ -3138,7 +3138,7 @@ static int CmdDiff(const char *Cmd) {
 
             // if diff,  time to find it
             for (int j = i; j < (i + width); j++) {
-                    
+
                 char ca = inA[j];
                 char cb = inB[j];
 
@@ -3166,8 +3166,8 @@ static int CmdDiff(const char *Cmd) {
     // mod
 
 
-    // print different length 
-    bool tallestA = (datalenA > datalenB); 
+    // print different length
+    bool tallestA = (datalenA > datalenB);
     if (tallestA) {
         n = datalenA;
     } else {
@@ -3175,13 +3175,13 @@ static int CmdDiff(const char *Cmd) {
     }
 
     // print data diff loop
-    for (; i < n;  i += width ) {
+    for (; i < n;  i += width) {
 
         memset(line, 0, sizeof(line));
 
         if (tallestA) {
-            hex_to_buffer((uint8_t*)line, inA + i, width, width, 0, 1, true);
-            ascii_to_buffer((uint8_t*)(line + strlen(line)), inA + i, width, width, 0);
+            hex_to_buffer((uint8_t *)line, inA + i, width, width, 0, 1, true);
+            ascii_to_buffer((uint8_t *)(line + strlen(line)), inA + i, width, width, 0);
             strcat(line + strlen(line), " | ");
             for (int j = 0; j < width; j++) {
                 strcat(line + strlen(line), "-- ");
@@ -3198,8 +3198,8 @@ static int CmdDiff(const char *Cmd) {
                 strcat(line + strlen(line), ".");
             }
             strcat(line + strlen(line), " | ");
-            hex_to_buffer((uint8_t*)(line + strlen(line)), inB + i, width, width, 0, 1, true);
-            ascii_to_buffer((uint8_t*)(line + strlen(line)), inB + i, width, width, 0);
+            hex_to_buffer((uint8_t *)(line + strlen(line)), inB + i, width, width, 0, 1, true);
+            ascii_to_buffer((uint8_t *)(line + strlen(line)), inB + i, width, width, 0);
         }
 
         PrintAndLogEx(INFO, "%03X | %s", i, line);
diff --git a/client/src/cmdflashmemspiffs.c b/client/src/cmdflashmemspiffs.c
index af84d277b..a1923d588 100644
--- a/client/src/cmdflashmemspiffs.c
+++ b/client/src/cmdflashmemspiffs.c
@@ -90,7 +90,7 @@ out:
 }
 
 int flashmem_spiffs_download(char *fn, uint8_t fnlen, void **pdest, size_t *destlen) {
-       // get size from spiffs itself !
+    // get size from spiffs itself !
     clearCommandBuffer();
     SendCommandNG(CMD_SPIFFS_STAT, (uint8_t *)fn, fnlen);
     PacketResponseNG resp;
@@ -542,7 +542,7 @@ static int CmdFlashMemSpiFFSView(const char *Cmd) {
 
     int breaks = arg_get_int_def(ctx, 2, 32);
     CLIParserFree(ctx);
- 
+
     uint8_t *dump = NULL;
     size_t dumplen = 0;
     int res = flashmem_spiffs_download(fn, fnlen, (void **)&dump, &dumplen);
diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index cfbe2f549..5d0f580cc 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -261,7 +261,7 @@ static void mf_print_blocks(uint16_t n, uint8_t *d) {
 static int mf_print_keys(uint16_t n, uint8_t *d) {
 
     uint8_t sectors = 0;
-    switch(n) {
+    switch (n) {
         case MIFARE_MINI_MAXBLOCK:
             sectors = MIFARE_MINI_MAXSECTOR;
             break;
@@ -270,7 +270,7 @@ static int mf_print_keys(uint16_t n, uint8_t *d) {
             break;
         case MIFARE_4K_MAXBLOCK:
             sectors = MIFARE_4K_MAXSECTOR;
-            break;        
+            break;
         case MIFARE_1K_MAXBLOCK:
         default:
             sectors = MIFARE_1K_MAXSECTOR;
@@ -292,7 +292,7 @@ static int mf_print_keys(uint16_t n, uint8_t *d) {
     }
     printKeyTable(sectors, e_sector);
     free(e_sector);
-    return PM3_SUCCESS;    
+    return PM3_SUCCESS;
 }
 
 static void mf_print_values(uint16_t n, uint8_t *d) {
@@ -303,7 +303,7 @@ static void mf_print_values(uint16_t n, uint8_t *d) {
     uint8_t cnt = 0;
     uint32_t value = 0;
     for (uint16_t i = 0; i < n; i++) {
-    
+
         if (mfc_value(d + (i * MFBLOCK_SIZE), &value))  {
             PrintAndLogEx(INFO, "%03d | " _YELLOW_("%" PRIu32) " " _YELLOW_("0x%" PRIX32), i, value, value);
             ++cnt;
@@ -311,7 +311,7 @@ static void mf_print_values(uint16_t n, uint8_t *d) {
     }
 
     if (cnt) {
-        PrintAndLogEx(INFO, "Found %u value blocks in file", cnt);        
+        PrintAndLogEx(INFO, "Found %u value blocks in file", cnt);
         PrintAndLogEx(NORMAL, "");
     }
 }
@@ -3634,7 +3634,7 @@ void printKeyTableEx(uint8_t sectorscnt, sector_t *e_sector, uint8_t start_secto
             snprintf(strB, sizeof(strB), "%012" PRIX64, e_sector[i].Key[1]);
 
         if (e_sector[i].foundKey[0] > 1) {
-            PrintAndLogEx(SUCCESS, " "_YELLOW_("%03d")" | %03d | " _GREEN_("%s")" | " _YELLOW_("%c")" | " _GREEN_("%s")" | " _YELLOW_("%c") 
+            PrintAndLogEx(SUCCESS, " "_YELLOW_("%03d")" | %03d | " _GREEN_("%s")" | " _YELLOW_("%c")" | " _GREEN_("%s")" | " _YELLOW_("%c")
                           , i
                           , mfSectorTrailerOfSector(i)
                           , strA, e_sector[i].foundKey[0]
@@ -3647,7 +3647,7 @@ void printKeyTableEx(uint8_t sectorscnt, sector_t *e_sector, uint8_t start_secto
             if (start_sector == 0)
                 s = i;
 
-            PrintAndLogEx(SUCCESS, " "_YELLOW_("%03d")" | %03d | " _GREEN_("%s")" | " _YELLOW_("%d")" | " _GREEN_("%s")" | " _YELLOW_("%d") 
+            PrintAndLogEx(SUCCESS, " "_YELLOW_("%03d")" | %03d | " _GREEN_("%s")" | " _YELLOW_("%d")" | " _GREEN_("%s")" | " _YELLOW_("%d")
                           , s
                           , mfSectorTrailerOfSector(s)
                           , strA, e_sector[i].foundKey[0]
diff --git a/client/src/pm3line_vocabulory.h b/client/src/pm3line_vocabulory.h
index 2c514f7cb..0564a76f4 100644
--- a/client/src/pm3line_vocabulory.h
+++ b/client/src/pm3line_vocabulory.h
@@ -101,6 +101,7 @@ const static vocabulory_t vocabulory[] = {
     { 1, "data bin2hex" }, 
     { 0, "data bitsamples" }, 
     { 1, "data clear" }, 
+    { 1, "data diff" }, 
     { 0, "data hexsamples" }, 
     { 1, "data hex2bin" }, 
     { 1, "data load" }, 
diff --git a/client/src/util.h b/client/src/util.h
index fa9b0eb94..2401a95aa 100644
--- a/client/src/util.h
+++ b/client/src/util.h
@@ -60,7 +60,7 @@ int FillBuffer(uint8_t *data, size_t maxDataLength, size_t *dataLength, ...);
 
 bool CheckStringIsHEXValue(const char *value);
 void ascii_to_buffer(uint8_t *buf, const uint8_t *hex_data, const size_t hex_len,
-                   const size_t hex_max_len, const size_t min_str_len);
+                     const size_t hex_max_len, const size_t min_str_len);
 void hex_to_buffer(uint8_t *buf, const uint8_t *hex_data, const size_t hex_len,
                    const size_t hex_max_len, const size_t min_str_len, const size_t spaces_between,
                    bool uppercase);
diff --git a/doc/commands.json b/doc/commands.json
index 1faa02809..410a63835 100644
--- a/doc/commands.json
+++ b/doc/commands.json
@@ -322,6 +322,29 @@
             ],
             "usage": "data detectclock [-hafnp]"
         },
+        "data diff": {
+            "command": "data diff",
+            "description": "diff takes a multitude of input data and makes a binary compare. it accepts filenames (filesystem or rdv4 flashmem spiffs), emulator memory, magic gen1",
+            "notes": [
+                "data diff -w 4 -a hf-mfu-01020304.bin -b hf-mfu-04030201.bin",
+                "data diff -a filea -b fileb",
+                "data diff -a filea --eb",
+                "data diff --fa filea -b fileb",
+                "data diff --fa filea --fb fileb",
+                "data diff --ea --cb"
+            ],
+            "offline": true,
+            "options": [
+                "-h, --help this help",
+                "-a <fn> input file name a",
+                "-b <fn> input file name b",
+                "--eb emulator memory <hf mf esave>",
+                "--fa <fn> input spiffs file a",
+                "--fb <fn> input spiffs file b",
+                "-w <4|8|16> width of data output"
+            ],
+            "usage": "data diff [-h] [-a <fn>] [-b <fn>] [--eb] [--fa <fn>] [--fb <fn>] [-w <4|8|16>]"
+        },
         "data dirthreshold": {
             "command": "data dirthreshold",
             "description": "max rising higher up-thres/ min falling lower down-thres, keep rest as prev.",
@@ -381,7 +404,7 @@
         },
         "data help": {
             "command": "data help",
-            "description": "help this help ----------- ------------------------- modulation------------------------- biphaserawdecode biphase decode bin stream in demodbuffer detectclock detect ask, fsk, nrz, psk clock rate of wave in graphbuffer fsktonrz convert fsk2 to nrz wave for alternate fsk demodulating (for weak fsk) manrawdecode manchester decode binary stream in demodbuffer modulation identify lf signal for clock and modulation rawdemod demodulate the data in the graphbuffer and output binary ----------- ------------------------- graph------------------------- askedgedetect adjust graph for manual ask demod using the length of sample differences to detect the edge of a wave autocorr autocorrelation over window dirthreshold max rising higher up-thres/ min falling lower down-thres, keep rest as prev. decimate decimate samples undecimate un-decimate samples hide hide graph window hpf remove dc offset from trace iir apply iir buttersworth filter on plot data grid overlay grid on graph window ltrim trim samples from left of trace mtrim trim out samples from the specified start to the specified stop norm normalize max/min to +/-128 plot show graph window rtrim trim samples from right of trace setgraphmarkers set blue and orange marker in graph window shiftgraphzero shift 0 for graphed wave + or - shift value timescale set a timescale to get a differential reading between the yellow and purple markers as time duration zerocrossings count time between zero-crossings convertbitstream convert graphbuffer's 0/1 values to 127 / -127 getbitstream convert graphbuffer's >=1 values to 1 and <1 to 0 ----------- ------------------------- general------------------------- asn1 asn1 decoder bin2hex converts binary to hexadecimal clear clears bigbuf on deviceside and graph window hex2bin converts hexadecimal to binary load load contents of file into graph window print print the data in the demodbuffer save save signal trace data (from graph window) setdebugmode set debugging level on client side",
+            "description": "help this help ----------- ------------------------- modulation------------------------- biphaserawdecode biphase decode bin stream in demodbuffer detectclock detect ask, fsk, nrz, psk clock rate of wave in graphbuffer fsktonrz convert fsk2 to nrz wave for alternate fsk demodulating (for weak fsk) manrawdecode manchester decode binary stream in demodbuffer modulation identify lf signal for clock and modulation rawdemod demodulate the data in the graphbuffer and output binary ----------- ------------------------- graph------------------------- askedgedetect adjust graph for manual ask demod using the length of sample differences to detect the edge of a wave autocorr autocorrelation over window dirthreshold max rising higher up-thres/ min falling lower down-thres, keep rest as prev. decimate decimate samples undecimate un-decimate samples hide hide graph window hpf remove dc offset from trace iir apply iir buttersworth filter on plot data grid overlay grid on graph window ltrim trim samples from left of trace mtrim trim out samples from the specified start to the specified stop norm normalize max/min to +/-128 plot show graph window rtrim trim samples from right of trace setgraphmarkers set blue and orange marker in graph window shiftgraphzero shift 0 for graphed wave + or - shift value timescale set a timescale to get a differential reading between the yellow and purple markers as time duration zerocrossings count time between zero-crossings convertbitstream convert graphbuffer's 0/1 values to 127 / -127 getbitstream convert graphbuffer's >=1 values to 1 and <1 to 0 ----------- ------------------------- general------------------------- asn1 asn1 decoder bin2hex converts binary to hexadecimal clear clears bigbuf on deviceside and graph window diff diff of input files hex2bin converts hexadecimal to binary load load contents of file into graph window print print the data in the demodbuffer save save signal trace data (from graph window) setdebugmode set debugging level on client side",
             "notes": [],
             "offline": true,
             "options": [],
@@ -10908,8 +10931,8 @@
         }
     },
     "metadata": {
-        "commands_extracted": 688,
+        "commands_extracted": 689,
         "extracted_by": "PM3Help2JSON v1.00",
-        "extracted_on": "2022-02-27T15:27:46"
+        "extracted_on": "2022-03-23T16:50:35"
     }
 }
\ No newline at end of file
diff --git a/doc/commands.md b/doc/commands.md
index ff41afea3..b6c2633b0 100644
--- a/doc/commands.md
+++ b/doc/commands.md
@@ -120,6 +120,7 @@ Check column "offline" for their availability.
 |`data bin2hex           `|Y       |`Converts binary to hexadecimal`
 |`data bitsamples        `|N       |`Get raw samples as bitstring`
 |`data clear             `|Y       |`Clears bigbuf on deviceside and graph window`
+|`data diff              `|Y       |`diff of input files`
 |`data hexsamples        `|N       |`Dump big buffer as hex bytes`
 |`data hex2bin           `|Y       |`Converts hexadecimal to binary`
 |`data load              `|Y       |`Load contents of file into graph window`
diff --git a/fpga-xc2s30/fpga_hf_15.v b/fpga-xc2s30/fpga_hf_15.v
index 60f62e5d8..b107ec180 100644
--- a/fpga-xc2s30/fpga_hf_15.v
+++ b/fpga-xc2s30/fpga_hf_15.v
@@ -207,9 +207,9 @@ hi_get_trace gt(
 // Major modes:
 //   000 --  HF 15 reader; subcarrier frequency and modulation depth selectable
 //   001 --  HF simulated tag
-//   010 --  
+//   010 --
 //   011 --  HF sniff
-//   100 --  
+//   100 --
 //   101 --  HF get trace
 //   110 --  unused
 //   111 --  FPGA_MAJOR_MODE_OFF
diff --git a/fpga-xc2s30/hi_reader_15.v b/fpga-xc2s30/hi_reader_15.v
index b60eafa0c..0db4fe2a3 100644
--- a/fpga-xc2s30/hi_reader_15.v
+++ b/fpga-xc2s30/hi_reader_15.v
@@ -87,7 +87,7 @@ reg [7:0] diff32 = 8'd0;
 reg [11:0] match16 = 12'd0;
 reg [11:0] match32 = 12'd0;
 reg [11:0] match28 = 12'd0;
-   
+
 always @(negedge adc_clk)
 begin
     if (corr_i_cnt[0] == 1'b0) // every 2 clock
@@ -116,7 +116,7 @@ begin
 
             avg128[127:8] = avg128[119:0];
             avg128[7:0] = avg;
-           
+
 
             if (corr_i_cnt[4:1] == 4'b0000) // every 32 clock (8*4)
             begin
