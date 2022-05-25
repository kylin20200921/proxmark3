commit d733a66c7bcc392e226dea9dd96f4ae585ccb0b1
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Oct 10 16:15:29 2021 +0200

    smart info, smart reader,  now have ATR fingerprinting

diff --git a/client/atr_scrap_eftlab.py b/client/atr_scrap_eftlab.py
index fc4579e6c..f99963f4d 100755
--- a/client/atr_scrap_eftlab.py
+++ b/client/atr_scrap_eftlab.py
@@ -75,11 +75,12 @@ typedef struct atr_s {
 const char *getAtrInfo(const char *atr_str);
 
 // atr_t array are expected to be NULL terminated
-const static atr_t AtrTable[] = {""")
+const static atr_t AtrTable[] = {
+    { "3BDF18FFC080B1FE751F033078464646462026204963656D616E1D", "Cardhelper by 0xFFFF and Iceman" },""")
 
     print_atr(df)
 
-    print("""    {NULL, "no ATR info available"}
+    print("""    {NULL, "N/A"}
 };
 
 #endif""")
diff --git a/client/src/atrs.h b/client/src/atrs.h
index 3cbcfeb3d..8b4568c20 100644
--- a/client/src/atrs.h
+++ b/client/src/atrs.h
@@ -13,6 +13,7 @@ const char *getAtrInfo(const char *atr_str);
 
 // atr_t array are expected to be NULL terminated
 const static atr_t AtrTable[] = {
+    { "3BDF18FFC080B1FE751F033078464646462026204963656D616E1D", "Cardhelper by 0xFFFF and Iceman" },
     { "3B021450", "Schlumberger Multiflex 3k" },
     { "3B025301", "Gemplus GemClub Memo SuperShop clubcard" },
     { "3B0400000000", "Laundromat payment card Spanish ID ('DNIe: Documento Nacional de Identidad electronico). http://www.dnie.es/" },
@@ -1618,7 +1619,7 @@ const static atr_t AtrTable[] = {
     { "3FFF9500FF918171FE47005449474552363031205265764D383013", "Spanish pay TV card for GOLTV" },
     { "3FFF9500FF918171FF4700444E4153505330312044736836303916", "PayTV card for DishNetwork Sat receiver http://www.dishnetwork.com/ Cards were obsoleted in nationwide system update in 2009." },
     { "3FFF9500FF918171FF470054494745523030332052657632353064", "Tivu' Sat (Italy) CAM card www.tivu.tv" },
-    {NULL, "no ATR info available"}
+    {NULL, "N/A"}
 };
 
 #endif
diff --git a/client/src/cmdsmartcard.c b/client/src/cmdsmartcard.c
index 5db317f7c..f0764bbf5 100644
--- a/client/src/cmdsmartcard.c
+++ b/client/src/cmdsmartcard.c
@@ -150,23 +150,23 @@ static void PrintATR(uint8_t *atr, size_t atrlen) {
     bool protocol_T15_present = false;
 
     if (T0 & 0x10) {
-        PrintAndLogEx(INFO, "\t- TA1 (Maximum clock frequency, proposed bit duration) [ 0x%02x ]", atr[2 + T1len]);
+        PrintAndLogEx(INFO, "    - TA1 (Maximum clock frequency, proposed bit duration) [ 0x%02x ]", atr[2 + T1len]);
         T1len++;
     }
 
     if (T0 & 0x20) {
-        PrintAndLogEx(INFO, "\t- TB1 (Deprecated: VPP requirements) [ 0x%02x ]", atr[2 + T1len]);
+        PrintAndLogEx(INFO, "    - TB1 (Deprecated: VPP requirements) [ 0x%02x ]", atr[2 + T1len]);
         T1len++;
     }
 
     if (T0 & 0x40) {
-        PrintAndLogEx(INFO, "\t- TC1 (Extra delay between bytes required by card) [ 0x%02x ]", atr[2 + T1len]);
+        PrintAndLogEx(INFO, "    - TC1 (Extra delay between bytes required by card) [ 0x%02x ]", atr[2 + T1len]);
         T1len++;
     }
 
     if (T0 & 0x80) {
         uint8_t TD1 = atr[2 + T1len];
-        PrintAndLogEx(INFO, "\t- TD1 (First offered transmission protocol, presence of TA2..TD2) [ 0x%02x ] Protocol T%d", TD1, TD1 & 0x0f);
+        PrintAndLogEx(INFO, "    - TD1 (First offered transmission protocol, presence of TA2..TD2) [ 0x%02x ] Protocol T%d", TD1, TD1 & 0x0f);
         protocol_T0_present = false;
         if ((TD1 & 0x0f) == 0) {
             protocol_T0_present = true;
@@ -178,20 +178,20 @@ static void PrintATR(uint8_t *atr, size_t atrlen) {
         T1len++;
 
         if (TD1 & 0x10) {
-            PrintAndLogEx(INFO, "\t- TA2 (Specific protocol and parameters to be used after the ATR) [ 0x%02x ]", atr[2 + T1len + TD1len]);
+            PrintAndLogEx(INFO, "    - TA2 (Specific protocol and parameters to be used after the ATR) [ 0x%02x ]", atr[2 + T1len + TD1len]);
             TD1len++;
         }
         if (TD1 & 0x20) {
-            PrintAndLogEx(INFO, "\t- TB2 (Deprecated: VPP precise voltage requirement) [ 0x%02x ]", atr[2 + T1len + TD1len]);
+            PrintAndLogEx(INFO, "    - TB2 (Deprecated: VPP precise voltage requirement) [ 0x%02x ]", atr[2 + T1len + TD1len]);
             TD1len++;
         }
         if (TD1 & 0x40) {
-            PrintAndLogEx(INFO, "\t- TC2 (Maximum waiting time for protocol T=0) [ 0x%02x ]", atr[2 + T1len + TD1len]);
+            PrintAndLogEx(INFO, "    - TC2 (Maximum waiting time for protocol T=0) [ 0x%02x ]", atr[2 + T1len + TD1len]);
             TD1len++;
         }
         if (TD1 & 0x80) {
             uint8_t TDi = atr[2 + T1len + TD1len];
-            PrintAndLogEx(INFO, "\t- TD2 (A supported protocol or more global parameters, presence of TA3..TD3) [ 0x%02x ] Protocol T%d", TDi, TDi & 0x0f);
+            PrintAndLogEx(INFO, "    - TD2 (A supported protocol or more global parameters, presence of TA3..TD3) [ 0x%02x ] Protocol T%d", TDi, TDi & 0x0f);
             if ((TDi & 0x0f) == 0) {
                 protocol_T0_present = true;
             }
@@ -205,20 +205,20 @@ static void PrintATR(uint8_t *atr, size_t atrlen) {
             while (nextCycle) {
                 nextCycle = false;
                 if (TDi & 0x10) {
-                    PrintAndLogEx(INFO, "\t- TA%d: 0x%02x", vi, atr[2 + T1len + TD1len + TDilen]);
+                    PrintAndLogEx(INFO, "    - TA%d: 0x%02x", vi, atr[2 + T1len + TD1len + TDilen]);
                     TDilen++;
                 }
                 if (TDi & 0x20) {
-                    PrintAndLogEx(INFO, "\t- TB%d: 0x%02x", vi, atr[2 + T1len + TD1len + TDilen]);
+                    PrintAndLogEx(INFO, "    - TB%d: 0x%02x", vi, atr[2 + T1len + TD1len + TDilen]);
                     TDilen++;
                 }
                 if (TDi & 0x40) {
-                    PrintAndLogEx(INFO, "\t- TC%d: 0x%02x", vi, atr[2 + T1len + TD1len + TDilen]);
+                    PrintAndLogEx(INFO, "    - TC%d: 0x%02x", vi, atr[2 + T1len + TD1len + TDilen]);
                     TDilen++;
                 }
                 if (TDi & 0x80) {
                     TDi = atr[2 + T1len + TD1len + TDilen];
-                    PrintAndLogEx(INFO, "\t- TD%d [ 0x%02x ] Protocol T%d", vi, TDi, TDi & 0x0f);
+                    PrintAndLogEx(INFO, "    - TD%d [ 0x%02x ] Protocol T=%d", vi, TDi, TDi & 0x0f);
                     TDilen++;
 
                     nextCycle = true;
@@ -248,10 +248,10 @@ static void PrintATR(uint8_t *atr, size_t atrlen) {
         PrintAndLogEx(WARNING, "Invalid ATR length. len: %zu, T1len: %d, TD1len: %d, TDilen: %d, K: %d", atrlen, T1len, TD1len, TDilen, K);
 
     if (K > 0)
-        PrintAndLogEx(INFO, "Historical bytes | len %02d | format %02x", K, atr[2 + T1len + TD1len + TDilen]);
+        PrintAndLogEx(DEBUG, "Historical bytes | len %02d | format %02x", K, atr[2 + T1len + TD1len + TDilen]);
 
     if (K > 1) {
-        PrintAndLogEx(INFO, "\tHistorical bytes");
+        PrintAndLogEx(INFO, "    Historical bytes ( %u )", K);
         print_buffer(&atr[2 + T1len + TD1len + TDilen], K, 1);
     }
 }
@@ -691,11 +691,17 @@ static int CmdSmartInfo(const char *Cmd) {
 
     // print header
     PrintAndLogEx(INFO, "--- " _CYAN_("Smartcard Information") " ---------");
-    PrintAndLogEx(INFO, "ISO7618-3 ATR : %s", sprint_hex(card.atr, card.atr_len));
-
-    PrintAndLogEx(INFO, "http://smartcard-atr.apdu.fr/parse?ATR=%s", sprint_hex_inrow(card.atr, card.atr_len));
+    PrintAndLogEx(INFO, "ISO7618-3 ATR... %s", sprint_hex(card.atr, card.atr_len));
+    // convert bytes to str.
+    char *hexstr = calloc((card.atr_len << 1) + 1, sizeof(uint8_t));
+    if (hexstr == NULL) {
+        PrintAndLogEx(WARNING, "failed to allocate memory");
+        return PM3_EMALLOC;
+    }
 
-    //PrintAndLogEx(INFO, "ATR lookup : %s", getAtrInfo("3FFF112503108041B00669FF4A50700000415A010011"));
+    hex_to_buffer((uint8_t *)hexstr, card.atr, card.atr_len, (card.atr_len << 1), 0, 0, true);
+    PrintAndLogEx(INFO, "Fingerprint..... %s", getAtrInfo(hexstr));
+    free(hexstr);
 
     // print ATR
     PrintAndLogEx(INFO, "ATR");
@@ -758,19 +764,18 @@ static int CmdSmartReader(const char *Cmd) {
         return PM3_ESOFT;
     }
     smart_card_atr_t *card = (smart_card_atr_t *)resp.data.asBytes;
-    PrintAndLogEx(INFO, "ISO7816-3 ATR : %s", sprint_hex(card->atr, card->atr_len));
-
-    // test with normal lookup, with existing ATR in table
-    PrintAndLogEx(INFO, "ATR lookup : %s", getAtrInfo("3FFF112503108041B00669FF4A50700000415A010011")); // Astro (Pay TV)
+    PrintAndLogEx(INFO, "ISO7816-3 ATR... %s", sprint_hex(card->atr, card->atr_len));
 
-  //PrintAndLogEx(INFO, "ATR lookup : %s", getAtrInfo("3FFD13250250800F..B0..69FF4A50D08000495403"));
-
-    // test with random bytes in .. space
-    PrintAndLogEx(INFO, "ATR lookup : %s", getAtrInfo("3FFD13250250800FEEB00269FF4A50D08000495403")); // Sky (Italy) VideoGuard CAM card
+    // convert bytes to str.
+    char *hexstr = calloc((card->atr_len << 1) + 1, sizeof(uint8_t));
+    if (hexstr == NULL) {
+        PrintAndLogEx(WARNING, "failed to allocate memory");
+        return PM3_EMALLOC;
+    }
 
-    // test w shorted str,  not in ATR table
-    PrintAndLogEx(INFO, "ATR lookup : %s", getAtrInfo("3FFD13250250800FEEB00269FF4A50D080004954")); // Sky (Italy) VideoGuard CAM card
-    
+    hex_to_buffer((uint8_t *)hexstr, card->atr, card->atr_len, (card->atr_len << 1), 0, 0, true);
+    PrintAndLogEx(INFO, "Fingerprint..... %s", getAtrInfo(hexstr));
+    free(hexstr);
     return PM3_SUCCESS;
 }
 
