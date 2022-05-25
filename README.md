commit 6924a42bf02257cb9b60ca295b04b86bb296aa46
Author: iceman1001 <iceman@iuse.se>
Date:   Wed Nov 3 22:01:11 2021 +0100

    note about EV2, that LRP uses same commands

diff --git a/include/protocols.h b/include/protocols.h
index 96c00dc5f..fc0369f3e 100644
--- a/include/protocols.h
+++ b/include/protocols.h
@@ -422,8 +422,9 @@ ISO 7816-4 Basic interindustry commands. For command APDU's.
 #define MFDES_AUTHENTICATE_ISO          0x1A  // AUTHENTICATE_STANDARD
 #define MFDES_AUTHENTICATE_AES          0xAA
 
-#define MFDES_AUTHENTICATE_EV2F         0x71
-#define MFDES_AUTHENTICATE_EV2NF        0x77
+//  Leakage Resilient Primitive (LRP)
+#define MFDES_AUTHENTICATE_EV2F         0x71  // LRP, AuthenticateLRPFirst    
+#define MFDES_AUTHENTICATE_EV2NF        0x77  // LRP, AuthenticateLRPNonFirst
 
 #define MFDES_CREDIT                    0x0C
 #define MFDES_LIMITED_CREDIT            0x1C
