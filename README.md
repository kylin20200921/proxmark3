commit 247790b8334fdbbf3e7efb7a6192850750c32931
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Mar 31 23:33:20 2022 +0200

    text

diff --git a/armsrc/iso14443a.c b/armsrc/iso14443a.c
index 19a79f706..9ac4146ca 100644
--- a/armsrc/iso14443a.c
+++ b/armsrc/iso14443a.c
@@ -166,7 +166,7 @@ void printHf14aConfig(void) {
     Dbprintf("  [r] RATS override....... %s%s%s",
              (hf14aconfig.forcerats == 0) ? _GREEN_("std") "    ( follow standard )" : "",
              (hf14aconfig.forcerats == 1) ? _RED_("force") "  ( always do RATS )" : "",
-             (hf14aconfig.forcerats == 2) ? _RED_("skip") "   () always skip RATS )" : ""
+             (hf14aconfig.forcerats == 2) ? _RED_("skip") "   ( always skip RATS )" : ""
             );
 }
 
@@ -678,7 +678,7 @@ void RAMFUNC SniffIso14443a(uint8_t param) {
     uint8_t *data = dma->buf;
 
     // Setup and start DMA.
-    if (!FpgaSetupSscDma((uint8_t *) dma->buf, DMA_BUFFER_SIZE)) {
+    if (FpgaSetupSscDma((uint8_t *) dma->buf, DMA_BUFFER_SIZE) == false) {
         if (g_dbglevel > 1) Dbprintf("FpgaSetupSscDma failed. Exiting");
         return;
     }
@@ -730,7 +730,7 @@ void RAMFUNC SniffIso14443a(uint8_t param) {
         // Need two samples to feed Miller and Manchester-Decoder
         if (rx_samples & 0x01) {
 
-            if (!TagIsActive) {        // no need to try decoding reader data if the tag is sending
+            if (TagIsActive == false) {        // no need to try decoding reader data if the tag is sending
                 uint8_t readerdata = (previous_data & 0xF0) | (*data >> 4);
                 if (MillerDecoding(readerdata, (rx_samples - 1) * 4)) {
                     LED_C_ON();
@@ -757,7 +757,7 @@ void RAMFUNC SniffIso14443a(uint8_t param) {
             }
 
             // no need to try decoding tag data if the reader is sending - and we cannot afford the time
-            if (!ReaderIsActive) {
+            if (ReaderIsActive == false) {
                 uint8_t tagdata = (previous_data << 4) | (*data & 0x0F);
                 if (ManchesterDecoding(tagdata, 0, (rx_samples - 1) * 4)) {
                     LED_B_ON();
