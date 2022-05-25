commit 3a920f4e0c86ef96624008e8365eb5d49dea9354
Author: iceman1001 <iceman@iuse.se>
Date:   Fri May 14 22:43:08 2021 +0200

    unsigned shift

diff --git a/armsrc/epa.c b/armsrc/epa.c
index d1f411a7e..74e16d808 100644
--- a/armsrc/epa.c
+++ b/armsrc/epa.c
@@ -472,6 +472,7 @@ int EPA_PACE_MSE_Set_AT(pace_version_info_t pace_version_info, uint8_t password)
 // Perform the PACE protocol by replaying given APDUs
 //-----------------------------------------------------------------------------
 void EPA_PACE_Replay(PacketCommandNG *c) {
+
     uint32_t timings[ARRAYLEN(apdu_lengths_replay)] = {0};
 
     // if an APDU has been passed, save it
diff --git a/client/src/cmddata.c b/client/src/cmddata.c
index b28bc8abc..e61c1808c 100644
--- a/client/src/cmddata.c
+++ b/client/src/cmddata.c
@@ -12,9 +12,9 @@
 #include "cmddata.h"
 #include <stdio.h>
 #include <string.h>
-#include <limits.h>   // for CmdNorm INT_MIN && INT_MAX
-#include <math.h>     // pow
-#include <ctype.h>    // tolower
+#include <limits.h>              // for CmdNorm INT_MIN && INT_MAX
+#include <math.h>                // pow
+#include <ctype.h>               // tolower
 #include "commonutil.h"          // ARRAYLEN
 #include "cmdparser.h"           // for command_t
 #include "ui.h"                  // for show graph controls
@@ -28,7 +28,7 @@
 #include "mifare/ndef.h"
 #include "cliparser.h"
 #include "cmdlft55xx.h"          // print...
-#include "crypto/asn1utils.h"     // ASN1 decode / print
+#include "crypto/asn1utils.h"    // ASN1 decode / print
 
 uint8_t DemodBuffer[MAX_DEMOD_BUF_LEN];
 size_t DemodBufferLen = 0;
diff --git a/client/src/wiegand_formatutils.c b/client/src/wiegand_formatutils.c
index 43ad6147d..878734149 100644
--- a/client/src/wiegand_formatutils.c
+++ b/client/src/wiegand_formatutils.c
@@ -176,19 +176,19 @@ bool add_HID_header(wiegand_message_t *data) {
         return false;
 
     if (data->Length >= 64) {
-        data->Top |= 1 << (data->Length - 64); // leading 1: start bit
         data->Top |= 0x09e00000; // Extended-length header
+        data->Top |= 1U << (data->Length - 64); // leading 1: start bit
     } else if (data->Length > 37) {
-        data->Mid |= 1 << (data->Length - 32); // leading 1: start bit
         data->Top |= 0x09e00000; // Extended-length header
+        data->Mid |= 1U << (data->Length - 32); // leading 1: start bit
     } else if (data->Length == 37) {
         // No header bits added to 37-bit cards
     } else if (data->Length >= 32) {
         data->Mid |= 0x20; // Bit 37; standard header
-        data->Mid |= 1 << (data->Length - 32); // leading 1: start bit
+        data->Mid |= 1U << (data->Length - 32); // leading 1: start bit
     } else {
         data->Mid |= 0x20; // Bit 37; standard header
-        data->Bot |= 1 << data->Length; // leading 1: start bit
+        data->Bot |= 1U << data->Length; // leading 1: start bit
     }
     return true;
 }
