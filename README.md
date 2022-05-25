commit cbf06104bd8f8c3155b7b478ba0cdb9086c2f9a4
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Dec 25 13:46:00 2021 +0100

    different rats for DESFire simulation (suggestion from @mosci)

diff --git a/armsrc/iso14443a.c b/armsrc/iso14443a.c
index 1e83baff9..7f3040685 100644
--- a/armsrc/iso14443a.c
+++ b/armsrc/iso14443a.c
@@ -1017,7 +1017,7 @@ bool SimulateIso14443aInit(int tagType, int flags, uint8_t *data, tag_response_i
     static uint8_t rSAKc3[3]  = { 0x00 };
     // dummy ATS (pseudo-ATR), answer to RATS
 //    static uint8_t rRATS[] = { 0x04, 0x58, 0x80, 0x02, 0x00, 0x00 };
-    static uint8_t rRATS[] = { 0x05, 0x75, 0x80, 0x60, 0x02, 0x00, 0x00 };
+    static uint8_t rRATS[] = { 0x05, 0x75, 0x80, 0x60, 0x02, 0x00, 0x00, 0x00 };
 
     // GET_VERSION response for EV1/NTAG
     static uint8_t rVERSION[10] = { 0x00 };
@@ -1044,6 +1044,7 @@ bool SimulateIso14443aInit(int tagType, int flags, uint8_t *data, tag_response_i
             rATQA[0] = 0x04;
             rATQA[1] = 0x03;
             sak = 0x20;
+            memcpy(rRATS, "\x06\x75\x77\x81\x02\x00\x00\x00", 8);
         }
         break;
         case 4: { // ISO/IEC 14443-4 - javacard (JCOP)
@@ -1227,9 +1228,9 @@ bool SimulateIso14443aInit(int tagType, int flags, uint8_t *data, tag_response_i
 
     // "precompile" responses. There are 11 predefined responses with a total of 80 bytes data to transmit.
     // Coded responses need one byte per bit to transfer (data, parity, start, stop, correction)
-    // 80 * 8 data bits, 80 * 1 parity bits, 11 start bits, 11 stop bits, 11 correction bits
-    // 80 * 8 + 80 + 11 + 11 + 11 == 753
-#define ALLOCATED_TAG_MODULATION_BUFFER_SIZE 753
+    // 81 * 8 data bits, 81 * 1 parity bits, 11 start bits, 11 stop bits, 11 correction bits
+    // 81 * 8 + 81 + 11 + 11 + 11 == 762
+#define ALLOCATED_TAG_MODULATION_BUFFER_SIZE 762
 
     uint8_t *free_buffer = BigBuf_malloc(ALLOCATED_TAG_MODULATION_BUFFER_SIZE);
     // modulation buffer pointer and current buffer free space size
