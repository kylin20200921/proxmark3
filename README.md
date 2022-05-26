commit df49565c046574a389b5fc2c62f0173f3ddda66a
Author: iceman1001 <iceman@iuse.se>
Date:   Wed Mar 23 17:49:09 2022 +0100

    style

diff --git a/armsrc/BigBuf.c b/armsrc/BigBuf.c
index 9c27bd4d8..d685f0388 100644
--- a/armsrc/BigBuf.c
+++ b/armsrc/BigBuf.c
@@ -233,7 +233,7 @@ uint32_t BigBuf_get_traceLen(void) {
   by 'hf list -t raw', alternatively 'hf list -t <proto>' for protocol-specific
   annotation of commands/responses.
 **/
-bool RAMFUNC LogTrace(const uint8_t *btBytes, uint16_t iLen, uint32_t timestamp_start, uint32_t timestamp_end, uint8_t *parity, bool readerToTag) {
+bool RAMFUNC LogTrace(const uint8_t *btBytes, uint16_t iLen, uint32_t timestamp_start, uint32_t timestamp_end, uint8_t *parity, bool reader2tag) {
     if (tracing == false) {
         return false;
     }
@@ -268,7 +268,7 @@ bool RAMFUNC LogTrace(const uint8_t *btBytes, uint16_t iLen, uint32_t timestamp_
     hdr->timestamp = timestamp_start;
     hdr->duration = duration & 0xFFFF;
     hdr->data_len = iLen;
-    hdr->isResponse = !readerToTag;
+    hdr->isResponse = !reader2tag;
     trace_len += TRACELOG_HDR_LEN;
 
     // data bytes
@@ -298,11 +298,11 @@ bool LogTrace_ISO15693(const uint8_t *bytes, uint16_t len, uint32_t ts_start, ui
 }
 
 // specific LogTrace function for bitstreams: the partial byte size is stored in first parity byte. E.g. bitstream "1100 00100010" -> partial byte: 4 bits
-bool RAMFUNC LogTraceBits(const uint8_t *btBytes, uint16_t bitLen, uint32_t timestamp_start, uint32_t timestamp_end, bool readerToTag) {
+bool RAMFUNC LogTraceBits(const uint8_t *btBytes, uint16_t bitLen, uint32_t timestamp_start, uint32_t timestamp_end, bool reader2tag) {
     uint8_t parity[(nbytes(bitLen) - 1) / 8 + 1];
     memset(parity, 0x00, sizeof(parity));
     parity[0] = bitLen % 8;
-    return LogTrace(btBytes, nbytes(bitLen), timestamp_start, timestamp_end, parity, readerToTag);
+    return LogTrace(btBytes, nbytes(bitLen), timestamp_start, timestamp_end, parity, reader2tag);
 }
 
 // Emulator memory
diff --git a/armsrc/BigBuf.h b/armsrc/BigBuf.h
index dd1f727d7..b1003a4c1 100644
--- a/armsrc/BigBuf.h
+++ b/armsrc/BigBuf.h
@@ -51,8 +51,8 @@ void set_tracing(bool enable);
 void set_tracelen(uint32_t value);
 bool get_tracing(void);
 
-bool RAMFUNC LogTrace(const uint8_t *btBytes, uint16_t iLen, uint32_t timestamp_start, uint32_t timestamp_end, uint8_t *parity, bool readerToTag);
-bool RAMFUNC LogTraceBits(const uint8_t *btBytes, uint16_t bitLen, uint32_t timestamp_start, uint32_t timestamp_end, bool readerToTag);
+bool RAMFUNC LogTrace(const uint8_t *btBytes, uint16_t iLen, uint32_t timestamp_start, uint32_t timestamp_end, uint8_t *parity, bool reader2tag);
+bool RAMFUNC LogTraceBits(const uint8_t *btBytes, uint16_t bitLen, uint32_t timestamp_start, uint32_t timestamp_end, bool reader2tag);
 bool LogTrace_ISO15693(const uint8_t *bytes, uint16_t len, uint32_t ts_start, uint32_t ts_end, uint8_t *parity, bool reader2tag);
 
 uint8_t emlSet(uint8_t *data, uint32_t offset, uint32_t length);
