commit ec59acf64cc1608a66d72ea00a50baff1b10e857
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Feb 12 17:46:34 2022 +0100

    style

diff --git a/armsrc/Standalone/lf_em4100rsww.c b/armsrc/Standalone/lf_em4100rsww.c
index 5ec39881a..727d188a3 100644
--- a/armsrc/Standalone/lf_em4100rsww.c
+++ b/armsrc/Standalone/lf_em4100rsww.c
@@ -15,7 +15,7 @@
 //
 //-----------------------------------------------------------------------------
 // Modes of operation:
-// 
+//
 // --- Read ---
 // Proxmark reads an EM4100 tag. LED A is turned on. When the tag is detected, it is saved to flash (RDV4 only) and proxmark enters the emulation mode.
 // It's the default mode for non-RDV4 devices, and if no previous read is present in the flash it's the default mode for RDV4 devices.
@@ -28,17 +28,17 @@
 // Pressing the button enters writing mode and clones the emulated tag.
 // Double pressing the button enters the validation mode.
 // Holding the button enters the reading mode.
-// 
+//
 // --- Write ---
 // Proxmarks writes the last read tag. LEDs A and B are turned on.
 // When writing is complete LEDs A and B blink three times and proxmark enters the emulation mode.
-// 
+//
 // --- Validate ---
 // Proxmark reads an EM4100 tag. LED C is turned on.
 // If tag matches the last saved tag, LED C blinks three times. If it doesn't all LEDs blink three times. Proxmark enters the emulation mode afterwards.
 // The result of the read is DISCARDED.
 // Pressing the button enters the emulation mode.
-// 
+//
 // --- Wipe ---
 // Proxmark continously wipes all approached T55xx tags. LED D is turned on, LEDs A-C are blinking.
 // Pressing the button enters the default mode (reading or emulation).
@@ -129,235 +129,232 @@ static void SaveIDtoFlash(uint64_t id) {
         bt[4 - i] = (uint8_t)(id >> 8 * i & 0xff);
     }
     if (exists_in_spiffs(filename))
-		rdv40_spiffs_append(filename, &bt[0], 5, RDV40_SPIFFS_SAFETY_NORMAL);
-	else 
+        rdv40_spiffs_append(filename, &bt[0], 5, RDV40_SPIFFS_SAFETY_NORMAL);
+    else
         rdv40_spiffs_write(filename, &bt[0], 5, RDV40_SPIFFS_SAFETY_NORMAL);
-	
-	if (exists_in_spiffs(filenameLast))
-		rdv40_spiffs_remove(filenameLast, RDV40_SPIFFS_SAFETY_NORMAL);
-	
-	rdv40_spiffs_write(filenameLast, &bt[0], 5, RDV40_SPIFFS_SAFETY_NORMAL);
+
+    if (exists_in_spiffs(filenameLast))
+        rdv40_spiffs_remove(filenameLast, RDV40_SPIFFS_SAFETY_NORMAL);
+
+    rdv40_spiffs_write(filenameLast, &bt[0], 5, RDV40_SPIFFS_SAFETY_NORMAL);
 }
 
 static bool ReadFlash(void) {
-	if (exists_in_spiffs(filenameLast) == false)
-		return false;
-	
-	uint8_t bt[5];
-	if (rdv40_spiffs_read(filenameLast, (uint8_t *) &bt, 5, RDV40_SPIFFS_SAFETY_NORMAL) < 0)
-		return false;
-	
-	low = bt[0];
-	low <<= 32;
-	low |= (bt[1] << 24) | (bt[2] << 16) | (bt[3] << 8) | bt[4];
-	low2 = low;
-	high = 0;
-	high2 = 0;
-	return true;
+    if (exists_in_spiffs(filenameLast) == false)
+        return false;
+
+    uint8_t bt[5];
+    if (rdv40_spiffs_read(filenameLast, (uint8_t *) &bt, 5, RDV40_SPIFFS_SAFETY_NORMAL) < 0)
+        return false;
+
+    low = bt[0];
+    low <<= 32;
+    low |= (bt[1] << 24) | (bt[2] << 16) | (bt[3] << 8) | bt[4];
+    low2 = low;
+    high = 0;
+    high2 = 0;
+    return true;
 }
 #endif
 
 static void Wipe(void) {
-	DbpString("Wipe mode");
-	LEDsoff();
-	
-	for (;;) {
-		LED_A_ON();
-		LED_B_ON();
-		LED_C_ON();
-		LED_D_ON();
-		copy_em410x_to_t55xx(LF_RWSB_T55XX_TYPE, LF_CLOCK, (uint32_t) 0, (uint32_t) 0, false);
-		SpinDelay(60);
-		LEDsoff();
-		LED_D_ON();
-		
-		int b = BUTTON_HELD(100);
-		if (b != BUTTON_NO_CLICK || data_available())
-			return;
-		
-		SpinDelay(100);
-
-		b = BUTTON_HELD(100);
-		if (b != BUTTON_NO_CLICK || data_available())
-			return;
-	}
+    DbpString("Wipe mode");
+    LEDsoff();
+
+    for (;;) {
+        LED_A_ON();
+        LED_B_ON();
+        LED_C_ON();
+        LED_D_ON();
+        copy_em410x_to_t55xx(LF_RWSB_T55XX_TYPE, LF_CLOCK, (uint32_t) 0, (uint32_t) 0, false);
+        SpinDelay(60);
+        LEDsoff();
+        LED_D_ON();
+
+        int b = BUTTON_HELD(100);
+        if (b != BUTTON_NO_CLICK || data_available())
+            return;
+
+        SpinDelay(100);
+
+        b = BUTTON_HELD(100);
+        if (b != BUTTON_NO_CLICK || data_available())
+            return;
+    }
 }
 
 static void Read(void) {
-	mode = 0;
-	
-	while (low2 == 0 || mode == 0)
-	{
-		DbpString("Read");
-		LEDsoff();
-		LED_A_ON();
-		
-		low2 = 0;
-		high2 = 0;
-		lf_em410x_watch(1, &high2, &low2, false);
-		
-		if (low2 != 0) {
-			LED_B_ON();
-			low = low2;
-			high = high2;
-			mode = 1;
-
-			#ifdef WITH_FLASH
-			SaveIDtoFlash(low2);
-			#endif
-			
-			SpinDelay(50);
-			LED_C_ON();
-			SpinDelay(50);
-			LED_D_ON();
-			SpinDelay(50);
-			LEDsoff();
-			return;
-		}
-		
-		if (data_available())
-			return;
-
-		int b = BUTTON_CLICKED(1000);
-
-		if ((b == BUTTON_SINGLE_CLICK || b == BUTTON_HOLD) && low != 0) {
-			mode = 1;
-			return;
-		}
-
-		if (b == BUTTON_DOUBLE_CLICK) {
-			Wipe();
-
-			if (low != 0) {
-				mode = 1;
-				return;
-			}
-		}
-	}
+    mode = 0;
+
+    while (low2 == 0 || mode == 0) {
+        DbpString("Read");
+        LEDsoff();
+        LED_A_ON();
+
+        low2 = 0;
+        high2 = 0;
+        lf_em410x_watch(1, &high2, &low2, false);
+
+        if (low2 != 0) {
+            LED_B_ON();
+            low = low2;
+            high = high2;
+            mode = 1;
+
+#ifdef WITH_FLASH
+            SaveIDtoFlash(low2);
+#endif
+
+            SpinDelay(50);
+            LED_C_ON();
+            SpinDelay(50);
+            LED_D_ON();
+            SpinDelay(50);
+            LEDsoff();
+            return;
+        }
+
+        if (data_available())
+            return;
+
+        int b = BUTTON_CLICKED(1000);
+
+        if ((b == BUTTON_SINGLE_CLICK || b == BUTTON_HOLD) && low != 0) {
+            mode = 1;
+            return;
+        }
+
+        if (b == BUTTON_DOUBLE_CLICK) {
+            Wipe();
+
+            if (low != 0) {
+                mode = 1;
+                return;
+            }
+        }
+    }
 }
 
 static void Validate(void) {
-	DbpString("Validate");
-	LEDsoff();
-	LED_C_ON();
-
-	for (;;) {
-		low2 = 0;
-		high2 = 0;
-
-		lf_em410x_watch(1, &high2, &low2, false);
-
-		if (low == low2 && high == high2) {
-			LED_C_OFF();
-			SpinDelay(150);
-			for (int i = 0; i < 3; i++) {
-				LED_C_ON();
-				SpinDelay(150);
-				LED_C_OFF();
-				SpinDelay(150);
-			}
-
-			return;
-		}
-		else if (low2 != 0 || high2 != 0) {
-			LEDsoff();
-			for (int i = 0; i < 3; i++) {
-				LED_A_ON();
-				LED_B_ON();
-				LED_C_ON();
-				LED_D_ON();
-				SpinDelay(250);
-				LEDsoff();
-				SpinDelay(150);
-			}
-
-			return;
-		}
-		else
-			SpinDelay(200);
-
-		int b = BUTTON_HELD(200);
-		if (b != BUTTON_NO_CLICK || data_available())
-			return;
-	}
+    DbpString("Validate");
+    LEDsoff();
+    LED_C_ON();
+
+    for (;;) {
+        low2 = 0;
+        high2 = 0;
+
+        lf_em410x_watch(1, &high2, &low2, false);
+
+        if (low == low2 && high == high2) {
+            LED_C_OFF();
+            SpinDelay(150);
+            for (int i = 0; i < 3; i++) {
+                LED_C_ON();
+                SpinDelay(150);
+                LED_C_OFF();
+                SpinDelay(150);
+            }
+
+            return;
+        } else if (low2 != 0 || high2 != 0) {
+            LEDsoff();
+            for (int i = 0; i < 3; i++) {
+                LED_A_ON();
+                LED_B_ON();
+                LED_C_ON();
+                LED_D_ON();
+                SpinDelay(250);
+                LEDsoff();
+                SpinDelay(150);
+            }
+
+            return;
+        } else
+            SpinDelay(200);
+
+        int b = BUTTON_HELD(200);
+        if (b != BUTTON_NO_CLICK || data_available())
+            return;
+    }
 }
 
 static void Write(void) {
-	DbpString("Write");
-	LED_A_ON();
-	LED_B_ON();
-	copy_em410x_to_t55xx(LF_RWSB_T55XX_TYPE, LF_CLOCK, (uint32_t)(low >> 32), (uint32_t)(low & 0xffffffff), false);
-	SpinDelay(75);
-	LEDsoff();
-
-	for (int i = 0; i < 3; i++) {
-		LED_A_ON();
-		LED_B_ON();
-		SpinDelay(75);
-		LED_A_OFF();
-		LED_B_OFF();
-		SpinDelay(75);
-	}
+    DbpString("Write");
+    LED_A_ON();
+    LED_B_ON();
+    copy_em410x_to_t55xx(LF_RWSB_T55XX_TYPE, LF_CLOCK, (uint32_t)(low >> 32), (uint32_t)(low & 0xffffffff), false);
+    SpinDelay(75);
+    LEDsoff();
+
+    for (int i = 0; i < 3; i++) {
+        LED_A_ON();
+        LED_B_ON();
+        SpinDelay(75);
+        LED_A_OFF();
+        LED_B_OFF();
+        SpinDelay(75);
+    }
 }
 
 static void Emulate(void) {
-	DbpString("Emulate");
-	LEDsoff();
-
-	for (;;) {
-		int bx = BUTTON_HELD(50);
-		if (bx == BUTTON_NO_CLICK)
-			break;
-		SpinDelay(50);
-	}
-
-	LED_B_ON();
-	construct_EM410x_emul(rev_quads(low));
+    DbpString("Emulate");
+    LEDsoff();
+
+    for (;;) {
+        int bx = BUTTON_HELD(50);
+        if (bx == BUTTON_NO_CLICK)
+            break;
+        SpinDelay(50);
+    }
+
+    LED_B_ON();
+    construct_EM410x_emul(rev_quads(low));
     SimulateTagLowFrequencyEx(buflen, 0, false, -1);
 
-	int b = BUTTON_CLICKED(800);
+    int b = BUTTON_CLICKED(800);
 
-	if (b == BUTTON_NO_CLICK)
-		return;
+    if (b == BUTTON_NO_CLICK)
+        return;
 
-	for (;;) {
-		int bx = BUTTON_HELD(50);
-		if (bx == BUTTON_NO_CLICK)
-			break;
-		SpinDelay(50);
-	}
+    for (;;) {
+        int bx = BUTTON_HELD(50);
+        if (bx == BUTTON_NO_CLICK)
+            break;
+        SpinDelay(50);
+    }
 
-	if (b == BUTTON_SINGLE_CLICK)
-		Write();
-	else if (b == BUTTON_HOLD)
-		mode = 0;
-	else if (b == BUTTON_DOUBLE_CLICK)
-		Validate();
+    if (b == BUTTON_SINGLE_CLICK)
+        Write();
+    else if (b == BUTTON_HOLD)
+        mode = 0;
+    else if (b == BUTTON_DOUBLE_CLICK)
+        Validate();
 }
 
 void RunMod() {
     StandAloneMode();
-	LEDsoff();
-	LED_D_ON();
+    LEDsoff();
+    LED_D_ON();
     FpgaDownloadAndGo(FPGA_BITSTREAM_LF);
-	WDT_HIT();
-	
-#ifdef WITH_FLASH	
-	if (ReadFlash())
-		mode = 1;
-	else Read();
+    WDT_HIT();
+
+#ifdef WITH_FLASH
+    if (ReadFlash())
+        mode = 1;
+    else Read();
 #else
-	Read();
+    Read();
 #endif
 
     for (;;) {
-		WDT_HIT();
-		LEDsoff();
-		
+        WDT_HIT();
+        LEDsoff();
+
         if (data_available()) return;
 
-		if (mode == 0)
-			Read();
-		else Emulate();
+        if (mode == 0)
+            Read();
+        else Emulate();
     }
 }
diff --git a/client/src/cmdhfcipurse.c b/client/src/cmdhfcipurse.c
index 3a8e49244..27c29c191 100644
--- a/client/src/cmdhfcipurse.c
+++ b/client/src/cmdhfcipurse.c
@@ -233,7 +233,7 @@ static int CLIParseCommandParametersEx(CLIParserContext *ctx, size_t keyid, size
         if (CLIParamHexToBuf(arg_get_str(ctx, aidid), hdata, hdatalen, &hdatalen)) {
             return PM3_ESOFT;
         }
-            
+
         if (hdatalen && (hdatalen < 1 || hdatalen > 16)) {
             PrintAndLogEx(ERR, _RED_("ERROR:") " application id length must be 1-16 bytes only");
             return PM3_EINVARG;
@@ -1466,7 +1466,7 @@ static int CmdHFCipurseUpdateKey(const char *Cmd) {
         arg_int0(NULL, "newkeyn", "<dec>", "target key ID"),
         arg_str0(NULL, "newkey",  "<hex 16 byte>", "new key"),
         arg_str0(NULL, "newkeya", "<hex 1 byte>", "new key additional info. 0x00 by default"),
-        
+
         arg_int0(NULL, "enckeyn", "<dec>", "encrypt key ID (must be equal to the key on the card)"),
         arg_str0(NULL, "enckey",  "<hex 16 byte>", "encrypt key (must be equal to the key on the card)"),
 
@@ -1541,7 +1541,7 @@ static int CmdHFCipurseUpdateKey(const char *Cmd) {
     uint8_t encKey[CIPURSE_AES_KEY_LENGTH] = CIPURSE_DEFAULT_KEY;
     if (hdatalen)
         memcpy(encKey, hdata, CIPURSE_AES_KEY_LENGTH);
-   
+
     bool noauth = arg_get_lit(ctx, 15);
     bool needCommit = arg_get_lit(ctx, 16);
 
diff --git a/client/src/cmdhfmfdes.c b/client/src/cmdhfmfdes.c
index 5b7438c8c..fa445d501 100644
--- a/client/src/cmdhfmfdes.c
+++ b/client/src/cmdhfmfdes.c
@@ -4998,7 +4998,7 @@ static int CmdHF14ADesWriteData(const char *Cmd) {
         arg_lit0(NULL, "debit",   "use for value file debit operation instead of credit"),
         arg_lit0(NULL, "commit",  "commit needs for backup file only. For the other file types and in the `auto` mode - command set it automatically"),
         arg_int0(NULL, "updaterec", "<dec>", "Record number for update record command. Updates record instead of write. Lastest record - 0"),
-        arg_str0(NULL, "isoid"   ,  "<hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)"),
+        arg_str0(NULL, "isoid",  "<hex>", "Application ISO ID (ISO DF ID) (2 hex bytes, big endian)"),
         arg_str0(NULL, "fileisoid", "<hex>", "File ISO ID (ISO DF ID) (2 hex bytes, big endian). Works only for ISO write commands"),
         arg_str0(NULL, "readerid",  "<hex>", "reader id for CommitReaderID command. If present - the command issued before write command"),
         arg_str0(NULL, "trkey",     "<hex>", "key for decode previous reader id"),
diff --git a/client/src/cmdhfmfp.c b/client/src/cmdhfmfp.c
index b90f1b9bc..fa8bfbd9e 100644
--- a/client/src/cmdhfmfp.c
+++ b/client/src/cmdhfmfp.c
@@ -893,7 +893,7 @@ static int CmdHFMFPWrbl(const char *Cmd) {
 
     void *argtable[] = {
         arg_param_begin,
-        arg_lit0("v",  "verbose", "Verbose mode"),        
+        arg_lit0("v",  "verbose", "Verbose mode"),
         arg_lit0("b",  "keyb",    "Use key B (def: keyA)"),
         arg_int1(NULL, "blk",     "<0..255>", "Block number"),
         arg_str1("d",  "data",    "<hex>", "Data, 16 hex bytes"),
diff --git a/client/src/cmdhfmfu.c b/client/src/cmdhfmfu.c
index f85f67ac9..5620f7a64 100644
--- a/client/src/cmdhfmfu.c
+++ b/client/src/cmdhfmfu.c
@@ -135,22 +135,22 @@ static int ul_print_nxp_silicon_info(uint8_t *card_uid) {
     uint16_t waferCoordX = ((uid[6] & 3) << 8) | uid[1];
     uint16_t waferCoordY = ((uid[6] & 12) << 6) | uid[2];
     uint32_t waferCounter = (
-                (uid[4] << 5) |
-                ((uid[6] & 0xF0) << 17) |
-                (uid[5] << 13) |
-                (uid[3] >> 3)
-            );
+                                (uid[4] << 5) |
+                                ((uid[6] & 0xF0) << 17) |
+                                (uid[5] << 13) |
+                                (uid[3] >> 3)
+                            );
     uint8_t testSite = uid[3] & 7;
 
     PrintAndLogEx(NORMAL, "");
     PrintAndLogEx(INFO, "--- " _CYAN_("Tag Silicon Information"));
     PrintAndLogEx(INFO, "       Wafer Counter: %" PRId32 " ( 0x%02" PRIX32 " )", waferCounter, waferCounter);
     PrintAndLogEx(INFO, "   Wafer Coordinates: x %" PRId16 ", y %" PRId16 " (0x%02" PRIX16 ", 0x%02" PRIX16 ")"
-            , waferCoordX
-            , waferCoordY
-            , waferCoordX
-            , waferCoordY
-        );
+                  , waferCoordX
+                  , waferCoordY
+                  , waferCoordX
+                  , waferCoordY
+                 );
     PrintAndLogEx(INFO, "           Test Site: %u", testSite);
     return PM3_SUCCESS;
 }
@@ -2063,7 +2063,7 @@ void printMFUdumpEx(mfu_dump_t *card, uint16_t pages, uint8_t startpage) {
     PrintAndLogEx(INFO, "Version..... " _YELLOW_("%s"), sprint_hex(card->version, sizeof(card->version)));
     PrintAndLogEx(INFO, "TBD 0....... %s", sprint_hex(card->tbo, sizeof(card->tbo)));
     PrintAndLogEx(INFO, "TBD 1....... %s", sprint_hex(card->tbo1, sizeof(card->tbo1)));
-    PrintAndLogEx(INFO, "Signature... %s", sprint_hex(card->signature, 16 ));
+    PrintAndLogEx(INFO, "Signature... %s", sprint_hex(card->signature, 16));
     PrintAndLogEx(INFO, "             %s", sprint_hex(card->signature + 16, sizeof(card->signature) - 16));
     for (uint8_t i = 0; i < 3; i ++) {
         PrintAndLogEx(INFO, "Counter %d... %s", i, sprint_hex(card->counter_tearing[i], 3));
@@ -2535,7 +2535,7 @@ static int CmdHF14AMfURestore(const char *Cmd) {
         free(fptr);
     }
 
-   // reserve memory
+    // reserve memory
     uint8_t *dump = NULL;
     size_t bytes_read = 0;
     int res = 0;
@@ -2589,7 +2589,7 @@ static int CmdHF14AMfURestore(const char *Cmd) {
 
     if (pages - 1 != mem->pages) {
         PrintAndLogEx(ERR, "Error, invalid dump, wrong page count");
-        PrintAndLogEx(INFO, " %u  vs mempg %u", pages -1 ,  mem->pages);
+        PrintAndLogEx(INFO, " %u  vs mempg %u", pages - 1,  mem->pages);
         free(dump);
         return PM3_ESOFT;
     }
@@ -4152,7 +4152,7 @@ static int CmdHF14AMfuEView(const char *Cmd) {
         return PM3_ETIMEOUT;
     }
 
-    printMFUdumpEx( (mfu_dump_t *)dump, blocks, 0);
+    printMFUdumpEx((mfu_dump_t *)dump, blocks, 0);
     free(dump);
     return PM3_SUCCESS;
 }
@@ -4225,14 +4225,14 @@ static int CmdHF14AMfuView(const char *Cmd) {
         return res;
     }
 
-    uint16_t block_cnt =  ((bytes_read - MFU_DUMP_PREFIX_LENGTH) / MFU_BLOCK_SIZE);
+    uint16_t block_cnt = ((bytes_read - MFU_DUMP_PREFIX_LENGTH) / MFU_BLOCK_SIZE);
 
     if (verbose) {
         PrintAndLogEx(INFO, "File: " _YELLOW_("%s"), filename);
         PrintAndLogEx(INFO, "File size %zu bytes, file blocks %d (0x%x)", bytes_read, block_cnt, block_cnt);
     }
 
-    printMFUdumpEx( (mfu_dump_t *)dump, block_cnt, 0);
+    printMFUdumpEx((mfu_dump_t *)dump, block_cnt, 0);
     free(dump);
     return PM3_SUCCESS;
 }
diff --git a/client/src/fileutils.c b/client/src/fileutils.c
index ddf8580a9..f2249d1ee 100644
--- a/client/src/fileutils.c
+++ b/client/src/fileutils.c
@@ -1112,7 +1112,7 @@ int loadFileJSONex(const char *preferredName, void *data, size_t maxdatalen, siz
 
     if (!strcmp(ctype, "mfu")) {
 
-        mfu_dump_t* mem = (mfu_dump_t *)udata;
+        mfu_dump_t *mem = (mfu_dump_t *)udata;
 
         JsonLoadBufAsHex(root, "$.Card.Version", mem->version, sizeof(mem->version), datalen);
         JsonLoadBufAsHex(root, "$.Card.TBO_0", mem->tbo, sizeof(mem->tbo), datalen);
@@ -1124,7 +1124,7 @@ int loadFileJSONex(const char *preferredName, void *data, size_t maxdatalen, siz
         JsonLoadBufAsHex(root, "$.Card.Tearing1", &mem->counter_tearing[1][3], 1, datalen);
         JsonLoadBufAsHex(root, "$.Card.Counter2", &mem->counter_tearing[2][0], 3, datalen);
         JsonLoadBufAsHex(root, "$.Card.Tearing2", &mem->counter_tearing[2][3], 1, datalen);
-        *datalen = MFU_DUMP_PREFIX_LENGTH;        
+        *datalen = MFU_DUMP_PREFIX_LENGTH;
 
         size_t sptr = 0;
         for (int i = 0; i < 256; i++) {
@@ -1146,7 +1146,7 @@ int loadFileJSONex(const char *preferredName, void *data, size_t maxdatalen, siz
         }
         // remove one, since pages indicates a index rather than number of available pages
         --mem->pages;
-        
+
         *datalen += sptr;
     }
 
diff --git a/client/src/pm3line_vocabulory.h b/client/src/pm3line_vocabulory.h
index b58422b70..a54adb525 100644
--- a/client/src/pm3line_vocabulory.h
+++ b/client/src/pm3line_vocabulory.h
@@ -185,6 +185,8 @@ const static vocabulory_t vocabulory[] = {
     { 0, "hf cipurse formatall" }, 
     { 0, "hf cipurse create" }, 
     { 0, "hf cipurse delete" }, 
+    { 0, "hf cipurse updkey" }, 
+    { 0, "hf cipurse updakey" }, 
     { 0, "hf cipurse default" }, 
     { 1, "hf cipurse test" }, 
     { 1, "hf epa help" }, 
@@ -348,6 +350,7 @@ const static vocabulory_t vocabulory[] = {
     { 0, "hf mfu ndefread" }, 
     { 0, "hf mfu rdbl" }, 
     { 0, "hf mfu restore" }, 
+    { 1, "hf mfu view" }, 
     { 0, "hf mfu wrbl" }, 
     { 0, "hf mfu eload" }, 
     { 0, "hf mfu eview" }, 
diff --git a/doc/commands.json b/doc/commands.json
index 5332ea348..85723c4c9 100644
--- a/doc/commands.json
+++ b/doc/commands.json
@@ -708,7 +708,7 @@
                 "-h, --help this help",
                 "-k, -k, --keep keep field on for next command",
                 "-a, -a, --apdu show apdu reqests and responses",
-                "-w, -w, --wired send data via contact (iso7816) interface. contactless interface set by default."
+                "-w, -w, --wired send data via contact (iso7816) interface. (def: contactless interface)"
             ],
             "usage": "emv challenge [-hkaw]"
         },
@@ -728,10 +728,10 @@
                 "-c, -c, --cda executes cda transaction. needs to get sdad in results.",
                 "-d, -d, --decision <aac|tc|arqc> terminal decision. aac - declined, tc - approved, arqc - online authorisation requested",
                 "-p, -p, --params load parameters from `emv_defparams.json` file for cdoldata making from cdol and parameters",
-                "-m, -m, --make make cdoldata from cdol (tag 8c and 8d) and parameters (by default uses default parameters)",
+                "-m, -m, --make make cdoldata from cdol (tag 8c and 8d) and parameters (def: use default parameters)",
                 "-a, -a, --apdu show apdu reqests and responses",
                 "-t, -t, --tlv tlv decode results of selected applets",
-                "-w, -w, --wired send data via contact (iso7816) interface. contactless interface set by default.",
+                "-w, -w, --wired send data via contact (iso7816) interface. (def: contactless interface)",
                 "<hex> cdoldata/cdol"
             ],
             "usage": "emv genac [-hkcpmatw] [-d <aac|tc|arqc>] <hex> [<hex>]..."
@@ -749,17 +749,17 @@
                 "-h, --help this help",
                 "-k, -k, --keep keep field on for next command",
                 "-p, -p, --params load parameters from `emv_defparams.json` file for pdoldata making from pdol and parameters",
-                "-m, -m, --make make pdoldata from pdol (tag 9f38) and parameters (by default uses default parameters)",
+                "-m, -m, --make make pdoldata from pdol (tag 9f38) and parameters (def: uses default parameters)",
                 "-a, -a, --apdu show apdu reqests and responses",
                 "-t, -t, --tlv tlv decode results of selected applets",
-                "-w, -w, --wired send data via contact (iso7816) interface. contactless interface set by default.",
+                "-w, -w, --wired send data via contact (iso7816) interface. (def: contactless interface)",
                 "<hex> pdoldata/pdol"
             ],
             "usage": "emv gpo [-hkpmatw] [<hex>]..."
         },
         "emv help": {
             "command": "emv help",
-            "description": "help this help test crypto logic test. list list iso7816 history --------------------------------------------------------------------------------------- emv exec available offline: no executes emv contactless transaction",
+            "description": "help this help test crypto logic test list list iso7816 history --------------------------------------------------------------------------------------- emv exec available offline: no executes emv contactless transaction",
             "notes": [
                 "emv exec -sat -> select card, execute msd transaction, show apdu and tlv",
                 "emv exec -satc -> select card, execute cda transaction, show apdu and tlv"
@@ -767,17 +767,17 @@
             "offline": true,
             "options": [
                 "-h, --help this help",
-                "-s, -s, --select activate field and select card.",
-                "-a, -a, --apdu show apdu reqests and responses.",
-                "-t, -t, --tlv tlv decode results.",
-                "-j, -j, --jload load transaction parameters from `emv_defparams.json` file.",
-                "-f, -f, --forceaid force search aid. search aid instead of execute ppse.",
+                "-s, -s, --select activate field and select card",
+                "-a, -a, --apdu show apdu reqests and responses",
+                "-t, -t, --tlv tlv decode results",
+                "-j, -j, --jload load transaction parameters from `emv_defparams.json` file",
+                "-f, -f, --forceaid force search aid. search aid instead of execute ppse",
                 "by default: transaction type - msd",
-                "-v, -v, --qvsdc transaction type - qvsdc or m/chip.",
-                "-c, -c, --qvsdccda transaction type - qvsdc or m/chip plus cda (sdad generation).",
-                "-x, -x, --vsdc transaction type - vsdc. for test only. not a standard behavior.",
-                "-g, -g, --acgpo visa. generate ac from gpo.",
-                "-w, -w, --wired send data via contact (iso7816) interface. contactless interface set by default."
+                "-v, -v, --qvsdc transaction type - qvsdc or m/chip",
+                "-c, -c, --qvsdccda transaction type - qvsdc or m/chip plus cda (sdad generation)",
+                "-x, -x, --vsdc transaction type - vsdc. for test only. not a standard behavior",
+                "-g, -g, --acgpo visa. generate ac from gpo",
+                "-w, -w, --wired send data via contact (iso7816) interface. (def: contactless interface)"
             ],
             "usage": "emv exec [-hsatjfvcxgw] by default:"
         },
@@ -794,10 +794,10 @@
                 "-h, --help this help",
                 "-k, -k, --keep keep field on for next command",
                 "-p, -p, --params load parameters from `emv_defparams.json` file for ddoldata making from ddol and parameters",
-                "-m, -m, --make make ddoldata from ddol (tag 9f49) and parameters (by default uses default parameters)",
+                "-m, -m, --make make ddoldata from ddol (tag 9f49) and parameters (def: use default parameters)",
                 "-a, -a, --apdu show apdu reqests and responses",
                 "-t, -t, --tlv tlv decode results of selected applets",
-                "-w, -w, --wired send data via contact (iso7816) interface. contactless interface set by default.",
+                "-w, -w, --wired send data via contact (iso7816) interface. (def: contactless interface)",
                 "<hex> ddoldata/ddol"
             ],
             "usage": "emv intauth [-hkpmatw] <hex> [<hex>]..."
@@ -836,10 +836,10 @@
                 "-s, -s, --select activate field and select card",
                 "-k, -k, --keep keep field on for next command",
                 "-1, --pse pse (1pay.sys.ddf01) mode",
-                "-2, --ppse ppse (2pay.sys.ddf01) mode (default mode)",
+                "-2, --ppse ppse (2pay.sys.ddf01) mode (def)",
                 "-a, -a, --apdu show apdu reqests and responses",
                 "-t, -t, --tlv tlv decode results of selected applets",
-                "-w, -w, --wired send data via contact (iso7816) interface. contactless interface set by default."
+                "-w, -w, --wired send data via contact (iso7816) interface. (def: contactless interface)"
             ],
             "usage": "emv pse [-hsk12atw]"
         },
@@ -856,7 +856,7 @@
                 "-k, -k, --keep keep field on for next command",
                 "-a, -a, --apdu show apdu reqests and responses",
                 "-t, -t, --tlv tlv decode results of selected applets",
-                "-w, -w, --wired send data via contact (iso7816) interface. contactless interface set by default.",
+                "-w, -w, --wired send data via contact (iso7816) interface. (def: contactless interface)",
                 "<hex> <sfi 1 byte><sfirecord 1 byte"
             ],
             "usage": "emv readrec [-hkatw] <hex> [<hex>]..."
@@ -873,7 +873,7 @@
                 "-h, --help this help",
                 "-t, -t, --selftest self test",
                 "-a, -a, --apdu show apdu reqests and responses",
-                "-w, -w, --wired send data via contact (iso7816) interface. contactless interface set by default"
+                "-w, -w, --wired send data via contact (iso7816) interface. (def: contactless interface)"
             ],
             "usage": "emv roca [-htaw]"
         },
@@ -887,17 +887,17 @@
             "offline": false,
             "options": [
                 "-h, --help this help",
-                "-a, -a, --apdu show apdu reqests and responses.",
-                "-t, -t, --tlv tlv decode results.",
+                "-a, -a, --apdu show apdu reqests and responses",
+                "-t, -t, --tlv tlv decode results",
                 "-e, -e, --extract extract tlv elements and fill application data",
-                "-j, -j, --jload load transaction parameters from `emv_defparams.json` file.",
+                "-j, -j, --jload load transaction parameters from `emv_defparams.json` file",
                 "by default: transaction type - msd",
-                "-v, -v, --qvsdc transaction type - qvsdc or m/chip.",
-                "-c, -c, --qvsdccda transaction type - qvsdc or m/chip plus cda (sdad generation).",
-                "-x, -x, --vsdc transaction type - vsdc. for test only. not a standard behavior.",
-                "-g, -g, --acgpo visa. generate ac from gpo.",
+                "-v, -v, --qvsdc transaction type - qvsdc or m/chip",
+                "-c, -c, --qvsdccda transaction type - qvsdc or m/chip plus cda (sdad generation)",
+                "-x, -x, --vsdc transaction type - vsdc. for test only. not a standard behavior",
+                "-g, -g, --acgpo visa. generate ac from gpo",
                 "-m, -m, --merge merge output file with card's data. (warning: the file may be corrupted!)",
-                "-w, -w, --wired send data via contact (iso7816) interface. contactless interface set by default.",
+                "-w, -w, --wired send data via contact (iso7816) interface. (def: contactless interface)",
                 "<fn> json output filename"
             ],
             "usage": "emv scan [-hatejvcxgmw] by default: <fn>"
@@ -916,7 +916,7 @@
                 "-k, -k, --keep keep field on for next command",
                 "-a, -a, --apdu show apdu reqests and responses",
                 "-t, -t, --tlv tlv decode results of selected applets",
-                "-w, -w, --wired send data via contact (iso7816) interface. contactless interface set by default."
+                "-w, -w, --wired send data via contact (iso7816) interface. (def: contactless interface)"
             ],
             "usage": "emv search [-hskatw]"
         },
@@ -934,7 +934,7 @@
                 "-k, -k, --keep keep field for next command",
                 "-a, -a, --apdu show apdu requests and responses",
                 "-t, -t, --tlv tlv decode results",
-                "-w, -w, --wired send data via contact (iso7816) interface. contactless interface set by default.",
+                "-w, -w, --wired send data via contact (iso7816) interface. (def: contactless interface)",
                 "<hex> applet aid"
             ],
             "usage": "emv select [-hskatw] <hex>"
@@ -1703,18 +1703,18 @@
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
+                "-v, --verbose verbose mode",
                 "-n <dec> key id",
                 "-k, --key <hex> auth key",
                 "--mfd show info about master file",
-                "--aid <hex 1..16 bytes> select application id (aid)",
+                "--aid <hex> select application id (aid) ( 1..16 bytes )",
                 "--fid <hex> file id",
-                "--chfid <hex 2 bytes> child file id (ef under application/master file)",
+                "--chfid <hex> child file id (ef under application/master file) ( 2 bytes )",
                 "--noauth read file attributes without authentication",
-                "--sreq <plain|mac(default)|encode> communication reader-picc security level",
-                "--sresp <plain|mac(default)|encode> communication picc-reader security level"
+                "--sreq <plain|mac|encode> communication reader-picc security level (def: mac)",
+                "--sresp <plain|mac|encode> communication picc-reader security level (def: mac)"
             ],
-            "usage": "hf cipurse aread [-hav] [-n <dec>] [-k <hex>] [--mfd] [--aid <hex 1..16 bytes>] [--fid <hex>] [--chfid <hex 2 bytes>] [--noauth] [--sreq <plain|mac(default)|encode>] [--sresp <plain|mac(default)|encode>]"
+            "usage": "hf cipurse aread [-hav] [-n <dec>] [-k <hex>] [--mfd] [--aid <hex>] [--fid <hex>] [--chfid <hex>] [--noauth] [--sreq <plain|mac|encode>] [--sresp <plain|mac|encode>]"
         },
         "hf cipurse auth": {
             "command": "hf cipurse auth",
@@ -1727,14 +1727,14 @@
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "--aid <hex 1..16 bytes> application id (aid)",
-                "--fid <hex 2 bytes> top file/application id (fid)",
+                "-v, --verbose verbose mode",
+                "--aid <hex> application id (aid) ( 1..16 bytes )",
+                "--fid <hex> top file/application id (fid) ( 2 bytes )",
                 "--mfd select masterfile by empty id",
                 "-n <dec> key id",
                 "-k, --key <hex> auth key"
             ],
-            "usage": "hf cipurse auth [-hav] [--aid <hex 1..16 bytes>] [--fid <hex 2 bytes>] [--mfd] [-n <dec>] [-k <hex>]"
+            "usage": "hf cipurse auth [-hav] [--aid <hex>] [--fid <hex>] [--mfd] [-n <dec>] [-k <hex>]"
         },
         "hf cipurse awrite": {
             "command": "hf cipurse awrite",
@@ -1749,20 +1749,20 @@
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
+                "-v, --verbose verbose mode",
                 "-n <dec> key id",
                 "-k, --key <hex> auth key",
                 "--mfd show info about master file",
-                "--aid <hex 1..16 bytes> select application id (aid)",
+                "--aid <hex> select application id (aid) ( 1..16 bytes )",
                 "--fid <hex> file id",
-                "--chfid <hex 2 bytes> child file id (ef under application/master file)",
+                "--chfid <hex> child file id (ef under application/master file) ( 2 bytes )",
                 "--noauth read file attributes without authentication",
-                "--sreq <plain|mac(default)|encode> communication reader-picc security level",
-                "--sresp <plain|mac(default)|encode> communication picc-reader security level",
+                "--sreq <plain|mac|encode> communication reader-picc security level (def: mac)",
+                "--sresp <plain|mac|encode> communication picc-reader security level (def: mac)",
                 "-d, --data <hex> file attributes",
-                "--commit need commit after write"
+                "--commit commit after write"
             ],
-            "usage": "hf cipurse awrite [-hav] [-n <dec>] [-k <hex>] [--mfd] [--aid <hex 1..16 bytes>] [--fid <hex>] [--chfid <hex 2 bytes>] [--noauth] [--sreq <plain|mac(default)|encode>] [--sresp <plain|mac(default)|encode>] [-d <hex>] [--commit]"
+            "usage": "hf cipurse awrite [-hav] [-n <dec>] [-k <hex>] [--mfd] [--aid <hex>] [--fid <hex>] [--chfid <hex>] [--noauth] [--sreq <plain|mac|encode>] [--sresp <plain|mac|encode>] [-d <hex>] [--commit]"
         },
         "hf cipurse create": {
             "command": "hf cipurse create",
@@ -1776,19 +1776,19 @@
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
+                "-v, --verbose verbose mode",
                 "-n <dec> key id",
                 "-k, --key <hex> auth key",
-                "--aid <hex 1..16 bytes> application id (aid)",
-                "--fid <hex 2 bytes> file id (fid)",
+                "--aid <hex> application id (aid) ( 1..16 bytes )",
+                "--fid <hex> file id (fid) ( 2 bytes )",
                 "--mfd select masterfile by empty id",
                 "-d, --data <hex> data with dgi for create",
-                "--sreq <plain|mac(default)|encode> communication reader-picc security level",
-                "--sresp <plain|mac(default)|encode> communication picc-reader security level",
+                "--sreq <plain|mac|encode> communication reader-picc security level (def: mac)",
+                "--sresp <plain|mac|encode> communication picc-reader security level (def: mac)",
                 "--no-auth execute without authentication",
-                "--commit need commit after create"
+                "--commit commit after create"
             ],
-            "usage": "hf cipurse create [-hav] [-n <dec>] [-k <hex>] [--aid <hex 1..16 bytes>] [--fid <hex 2 bytes>] [--mfd] [-d <hex>] [--sreq <plain|mac(default)|encode>] [--sresp <plain|mac(default)|encode>] [--no-auth] [--commit]"
+            "usage": "hf cipurse create [-hav] [-n <dec>] [-k <hex>] [--aid <hex>] [--fid <hex>] [--mfd] [-d <hex>] [--sreq <plain|mac|encode>] [--sresp <plain|mac|encode>] [--no-auth] [--commit]"
         },
         "hf cipurse default": {
             "command": "hf cipurse default",
@@ -1804,10 +1804,10 @@
                 "--clear resets to defaults",
                 "-n <dec> key id",
                 "-k, --key <hex> authentication key",
-                "--aid <hex 1..16 bytes> application id (aid)",
-                "--fid <hex 2 bytes> file id"
+                "--aid <hex> application id (aid) ( 1..16 bytes )",
+                "--fid <hex> file id ( 2 bytes )"
             ],
-            "usage": "hf cipurse default [-h] [--clear] [-n <dec>] [-k <hex>] [--aid <hex 1..16 bytes>] [--fid <hex 2 bytes>]"
+            "usage": "hf cipurse default [-h] [--clear] [-n <dec>] [-k <hex>] [--aid <hex>] [--fid <hex>]"
         },
         "hf cipurse delete": {
             "command": "hf cipurse delete",
@@ -1822,18 +1822,18 @@
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
+                "-v, --verbose verbose mode",
                 "-n <dec> key id",
                 "-k, --key <hex> auth key",
                 "--fid <hex> file/application id under mf for delete",
-                "--aid <hex 1..16 bytes> application id (aid) for delete",
-                "--chfid <hex 2 bytes> child file id (ef under application/master file)",
-                "--sreq <plain|mac(default)|encode> communication reader-picc security level",
-                "--sresp <plain|mac(default)|encode> communication picc-reader security level",
+                "--aid <hex> application id (aid) for delete ( 1..16 bytes )",
+                "--chfid <hex> child file id (ef under application/master file) ( 2 bytes )",
+                "--sreq <plain|mac|encode> communication reader-picc security level (def: mac)",
+                "--sresp <plain|mac|encode> communication picc-reader security level (def: mac)",
                 "--no-auth execute without authentication",
-                "--commit commit"
+                "--commit commit after delete"
             ],
-            "usage": "hf cipurse delete [-hav] [-n <dec>] [-k <hex>] [--fid <hex>] [--aid <hex 1..16 bytes>] [--chfid <hex 2 bytes>] [--sreq <plain|mac(default)|encode>] [--sresp <plain|mac(default)|encode>] [--no-auth] [--commit]"
+            "usage": "hf cipurse delete [-hav] [-n <dec>] [-k <hex>] [--fid <hex>] [--aid <hex>] [--chfid <hex>] [--sreq <plain|mac|encode>] [--sresp <plain|mac|encode>] [--no-auth] [--commit]"
         },
         "hf cipurse formatall": {
             "command": "hf cipurse formatall",
@@ -1847,14 +1847,14 @@
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
+                "-v, --verbose verbose mode",
                 "-n <dec> key id",
                 "-k, --key <hex> auth key",
-                "--sreq <plain|mac(default)|encode> communication reader-picc security level",
-                "--sresp <plain|mac(default)|encode> communication picc-reader security level",
+                "--sreq <plain|mac|encode> communication reader-picc security level (def: mac)",
+                "--sresp <plain|mac|encode> communication picc-reader security level (def: mac)",
                 "--no-auth execute without authentication"
             ],
-            "usage": "hf cipurse formatall [-hav] [-n <dec>] [-k <hex>] [--sreq <plain|mac(default)|encode>] [--sresp <plain|mac(default)|encode>] [--no-auth]"
+            "usage": "hf cipurse formatall [-hav] [-n <dec>] [-k <hex>] [--sreq <plain|mac|encode>] [--sresp <plain|mac|encode>] [--no-auth]"
         },
         "hf cipurse help": {
             "command": "hf cipurse help",
@@ -1880,17 +1880,17 @@
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
+                "-v, --verbose verbose mode",
                 "-n <dec> key id",
                 "-k, --key <hex> auth key",
-                "--aid <hex 1..16 bytes> application id (aid)",
+                "--aid <hex> application id (aid) ( 1..16 bytes )",
                 "--fid <hex> file id",
                 "-o, --offset <dec> offset for reading data from file",
                 "--noauth read file without authentication",
-                "--sreq <plain|mac(default)|encode> communication reader-picc security level",
-                "--sresp <plain|mac(default)|encode> communication picc-reader security level"
+                "--sreq <plain|mac|encode> communication reader-picc security level (def: mac)",
+                "--sresp <plain|mac|encode> communication picc-reader security level (def: mac)"
             ],
-            "usage": "hf cipurse read [-hav] [-n <dec>] [-k <hex>] [--aid <hex 1..16 bytes>] [--fid <hex>] [-o <dec>] [--noauth] [--sreq <plain|mac(default)|encode>] [--sresp <plain|mac(default)|encode>]"
+            "usage": "hf cipurse read [-hav] [-n <dec>] [-k <hex>] [--aid <hex>] [--fid <hex>] [-o <dec>] [--noauth] [--sreq <plain|mac|encode>] [--sresp <plain|mac|encode>]"
         },
         "hf cipurse select": {
             "command": "hf cipurse select",
@@ -1905,14 +1905,14 @@
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
+                "-v, --verbose verbose mode",
                 "-t, --tlv tlv decode returned data",
-                "--aid <hex 1..16 bytes> application id (aid)",
-                "--fid <hex 2 bytes> top level file (or application) id (fid)",
+                "--aid <hex> application id (aid) 1..16 bytes",
+                "--fid <hex> top level file (or application) id (fid) 2 bytes",
                 "--mfd select masterfile by empty id",
-                "--chfid <hex 2 bytes> child file id (ef under application/master file)"
+                "--chfid <hex> child file id (ef under application/master file) 2 bytes"
             ],
-            "usage": "hf cipurse select [-havt] [--aid <hex 1..16 bytes>] [--fid <hex 2 bytes>] [--mfd] [--chfid <hex 2 bytes>]"
+            "usage": "hf cipurse select [-havt] [--aid <hex>] [--fid <hex>] [--mfd] [--chfid <hex>]"
         },
         "hf cipurse test": {
             "command": "hf cipurse test",
@@ -1929,6 +1929,61 @@
             ],
             "usage": "hf epa cnonces [-h] --size <dec> --num <dec> -d <dec>"
         },
+        "hf cipurse updakey": {
+            "command": "hf cipurse updakey",
+            "description": "update key attributes. factory default - 0x02. b0 - update right - 1 self b1 - change key and rights - 0 frozen b2 - use as key encryption key - 1 blocked b8 - key validity - 0 valid",
+            "notes": [
+                "hf cipurse updakey --trgkeyn 2 --attr 80 -> block key 2 for lifetime (warning!)",
+                "hf cipurse updakey --trgkeyn 1 --attr 02 --commit -> for key 1"
+            ],
+            "offline": false,
+            "options": [
+                "-h, --help this help",
+                "-a, --apdu show apdu requests and responses",
+                "-v, --verbose show technical data",
+                "-n <dec> key id for authentication",
+                "-k, --key <hex> auth key",
+                "--aid <hex 1..16 bytes> application id (aid)",
+                "--fid <hex 2 bytes> file id (fid)",
+                "--mfd select masterfile by empty id",
+                "--trgkeyn <dec> target key id",
+                "--attr <hex 1 byte> key attributes 1 byte",
+                "--sreq <plain|mac(default)|encode> communication reader-picc security level",
+                "--sresp <plain|mac(default)|encode> communication picc-reader security level",
+                "--no-auth execute without authentication",
+                "--commit commit"
+            ],
+            "usage": "hf cipurse updakey [-hav] [-n <dec>] [-k <hex>] [--aid <hex 1..16 bytes>] [--fid <hex 2 bytes>] [--mfd] [--trgkeyn <dec>] [--attr <hex 1 byte>] [--sreq <plain|mac(default)|encode>] [--sresp <plain|mac(default)|encode>] [--no-auth] [--commit]"
+        },
+        "hf cipurse updkey": {
+            "command": "hf cipurse updkey",
+            "description": "update key.",
+            "notes": [
+                "hf cipurse updkey --aid 4144204631 --newkeyn 2 --newkeya 00 --newkey 73737373737373737373737373737373 -> update default application key 2 with default value 73..73",
+                "hf cipurse updkey --newkeyn 1 --newkeya 00 --newkey 0102030405060708090a0b0c0d0e0f10 --commit -> for key 1"
+            ],
+            "offline": false,
+            "options": [
+                "-h, --help this help",
+                "-a, --apdu show apdu requests and responses",
+                "-v, --verbose show technical data",
+                "-n <dec> key id for authentication",
+                "-k, --key <hex> auth key",
+                "--aid <hex 1..16 bytes> application id (aid)",
+                "--fid <hex 2 bytes> file id (fid)",
+                "--mfd select masterfile by empty id",
+                "--newkeyn <dec> target key id",
+                "--newkey <hex 16 byte> new key",
+                "--newkeya <hex 1 byte> new key additional info. 0x00 by default",
+                "--enckeyn <dec> encrypt key id (must be equal to the key on the card)",
+                "--enckey <hex 16 byte> encrypt key (must be equal to the key on the card)",
+                "--sreq <plain|mac(default)|encode> communication reader-picc security level",
+                "--sresp <plain|mac(default)|encode> communication picc-reader security level",
+                "--no-auth execute without authentication",
+                "--commit commit"
+            ],
+            "usage": "hf cipurse updakey [-hav] [-n <dec>] [-k <hex>] [--aid <hex 1..16 bytes>] [--fid <hex 2 bytes>] [--mfd] [--newkeyn <dec>] [--newkey <hex 16 byte>] [--newkeya <hex 1 byte>] [--enckeyn <dec>] [--enckey <hex 16 byte>] [--sreq <plain|mac(default)|encode>] [--sresp <plain|mac(default)|encode>] [--no-auth] [--commit]"
+        },
         "hf cipurse write": {
             "command": "hf cipurse write",
             "description": "write file in the application by file id with key id and key. if no key is supplied, default key of 737373...7373 will be used",
@@ -1942,19 +1997,19 @@
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
+                "-v, --verbose verbose mode",
                 "-n <dec> key id",
                 "-k, --key <hex> auth key",
-                "--aid <hex 1..16 bytes> application id (aid)",
+                "--aid <hex> application id (aid) ( 1..16 bytes )",
                 "--fid <hex> file id",
                 "-o, --offset <dec> offset for reading data from file",
                 "--noauth read file without authentication",
-                "--sreq <plain|mac(default)|encode> communication reader-picc security level",
-                "--sresp <plain|mac(default)|encode> communication picc-reader security level",
-                "-d, --data <hex> hex data to write to new file",
-                "--commit need commit after write"
+                "--sreq <plain|mac|encode> communication reader-picc security level (def: mac)",
+                "--sresp <plain|mac|encode> communication picc-reader security level (def: mac)",
+                "-d, --data <hex> data to write to new file",
+                "--commit commit after write"
             ],
-            "usage": "hf cipurse write [-hav] [-n <dec>] [-k <hex>] [--aid <hex 1..16 bytes>] [--fid <hex>] [-o <dec>] [--noauth] [--sreq <plain|mac(default)|encode>] [--sresp <plain|mac(default)|encode>] [-d <hex>] [--commit]"
+            "usage": "hf cipurse write [-hav] [-n <dec>] [-k <hex>] [--aid <hex>] [--fid <hex>] [-o <dec>] [--noauth] [--sreq <plain|mac|encode>] [--sresp <plain|mac|encode>] [-d <hex>] [--commit]"
         },
         "hf emrtd help": {
             "command": "hf emrtd help",
@@ -2317,7 +2372,7 @@
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu reqests and responses",
-                "-v, --verbose show technical data. vv - show full certificates data",
+                "-v, --verbose verbose mode. vv - show full certificates data",
                 "-c, --cbor show cbor decoded data",
                 "-l, --list add credentialid from json to allowlist",
                 "-f, --file <fn> parameter json file name"
@@ -2338,19 +2393,19 @@
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu reqests and responses",
-                "-v, --verbose show technical data",
+                "-v, --verbose verbose mode",
                 "default mode: dont-enforce-user-presence-and-sign",
                 "-u, --user mode: enforce-user-presence-and-sign",
                 "-c, --check mode: check-only",
-                "-f, --file <fn> json input file name for parameters",
+                "-f, --file <fn> json file name for parameters",
                 "-k, --key <hex> public key to verify signature",
                 "--kh <hex> key handle (var 0..255b)",
-                "--cp <ascii> challenge parameter (1..16 chars)",
-                "--ap <ascii> application parameter (1..16 chars)",
+                "--cp <str> challenge parameter (1..16 chars)",
+                "--ap <str> application parameter (1..16 chars)",
                 "--cpx <hex> challenge parameter (32 bytes hex)",
                 "--apx <hex> application parameter (32 bytes hex)"
             ],
-            "usage": "hf fido auth [-havuc] default mode: [-f <fn>] [-k <hex>] [--kh <hex>] [--cp <ascii>] [--ap <ascii>] [--cpx <hex>] [--apx <hex>]"
+            "usage": "hf fido auth [-havuc] default mode: [-f <fn>] [-k <hex>] [--kh <hex>] [--cp <str>] [--ap <str>] [--cpx <hex>] [--apx <hex>]"
         },
         "hf fido help": {
             "command": "hf fido help",
@@ -2396,7 +2451,7 @@
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu reqests and responses",
-                "-v, --verbose show technical data. vv - show full certificates data",
+                "-v, --verbose verbose mode. vv - show full certificates data",
                 "-t, --tlv show der certificate contents in tlv representation",
                 "-c, --cbor show cbor decoded data",
                 "-f, --file <fn> parameter json file name"
@@ -2416,15 +2471,15 @@
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data. vv - show full certificates data",
+                "-v, --verbose verbose mode. vv - show full certificates data",
                 "-t, --tlv show der certificate contents in tlv representation",
                 "-f, --file <fn> json input file name for parameters",
-                "--cp <ascii> challenge parameter (1..16 chars)",
-                "--ap <ascii> application parameter (1..16 chars)",
+                "--cp <str> challenge parameter (1..16 chars)",
+                "--ap <str> application parameter (1..16 chars)",
                 "--cpx <hex> challenge parameter (32 bytes hex)",
                 "--apx <hex> application parameter (32 bytes hex)"
             ],
-            "usage": "hf fido reg [-havt] [-f <fn>] [--cp <ascii>] [--ap <ascii>] [--cpx <hex>] [--apx <hex>]"
+            "usage": "hf fido reg [-havt] [-f <fn>] [--cp <str>] [--ap <str>] [--cpx <hex>] [--apx <hex>]"
         },
         "hf gallagher clone": {
             "command": "hf gallagher clone",
@@ -3475,7 +3530,7 @@
                 "-h, --help this help",
                 "-k, --key <hex> key specified as 12 hex symbols",
                 "--blk <dec> input block number",
-                "-a target key a, if found also check key b for duplicate",
+                "-a target key a",
                 "-b target key b",
                 "-*, --all target both key a & b (default)",
                 "--mini mifare classic mini / s20",
@@ -4239,7 +4294,7 @@
             "command": "hf mfdes auth",
             "description": "select application on the card. it selects app if it is a valid one or returns an error.",
             "notes": [
-                "hf mfdes auth -n 0 -t des -k 0000000000000000 -f none -> select picc level and authenticate with key num=0, key type=des, key=00..00 and key derivation = none",
+                "hf mfdes auth -n 0 -t des -k 0000000000000000 --kdf none -> select picc level and authenticate with key num=0, key type=des, key=00..00 and key derivation = none",
                 "hf mfdes auth -n 0 -t aes -k 00000000000000000000000000000000 -> select picc level and authenticate with key num=0, key type=aes, key=00..00 and key derivation = none",
                 "hf mfdes auth -n 0 -t des -k 0000000000000000 --save -> select picc level and authenticate and in case of successful authentication - save channel parameters to defaults",
                 "hf mfdes auth --aid 123456 -> select application 123456 and authenticate via parameters from `default` command"
@@ -4248,109 +4303,110 @@
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
+                "-v, --verbose verbose mode",
                 "-n, --keyno <dec> key number",
-                "-t, --algo <des|2tdea|3tdea|aes> crypt algo: des, 2tdea, 3tdea, aes",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
                 "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none|an10922|gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <hex> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain|mac|encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native|niso|iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40|ev1|ev2|lrp> secure channel: d40/ev1/ev2/lrp",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
                 "--aid <hex> application id of application for some parameters (3 hex bytes, big endian)",
-                "--appisoid <hex> application iso id (iso df id) (2 hex bytes, big endian).",
+                "--isoid <hex> application iso id (iso df id) (2 hex bytes, big endian)",
                 "--save saves channels parameters to defaults if authentication succeeds"
             ],
-            "usage": "hf mfdes auth [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [-f <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [-s <d40|ev1|ev2|lrp>] [--aid <hex>] [--appisoid <hex>] [--save]"
+            "usage": "hf mfdes auth [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [--isoid <hex>] [--save]"
         },
         "hf mfdes bruteaid": {
             "command": "hf mfdes bruteaid",
             "description": "recover aids by bruteforce. warning: this command takes a loooong time",
             "notes": [
                 "hf mfdes bruteaid -> search all apps",
-                "hf mfdes bruteaid -s f0000f -i 16 -> search mad range manually"
+                "hf mfdes bruteaid --start f0000f -i 16 -> search mad range manually"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
-                "-s, --start <hex> starting app id as hex bytes (3 bytes, big endian)",
-                "-e, --end <hex> last app id as hex bytes (3 bytes, big endian)",
+                "--start <hex> starting app id as hex bytes (3 bytes, big endian)",
+                "--end <hex> last app id as hex bytes (3 bytes, big endian)",
                 "-i, --step <dec> increment step when bruteforcing",
                 "-m, --mad only bruteforce the mad range"
             ],
-            "usage": "hf mfdes bruteaid [-hm] [-s <hex>] [-e <hex>] [-i <dec>]"
+            "usage": "hf mfdes bruteaid [-hm] [--start <hex>] [--end <hex>] [-i <dec>]"
         },
         "hf mfdes changekey": {
             "command": "hf mfdes changekey",
             "description": "change picc/application key. needs to provide keynum/key for a valid authentication (may get from default parameters).",
             "notes": [
-                "change crypto algorithm for picc key is possible, but for app keys crypto algorithm is set by createapp command and can't be changed wo application delete",
+                "change crypto algorithm for picc key is possible,",
+                "but for app keys crypto algorithm is set by createapp command and can't be changed wo application delete",
                 "",
                 "hf mfdes changekey --aid 123456 -> execute with default factory setup. change des key 0 in the app 123456 from 00..00 to 00..00",
-                "hf mfdes changekey --appisoid df01 -t aes -s lrp --newkeyno 01 -> change key 01 via lrp channelhf mfdes changekey -t des --newalgo aes --newkey 11223344556677889900112233445566 --newver a5 -> change card master key to aes one",
+                "hf mfdes changekey --isoid df01 -t aes -s lrp --newkeyno 01 -> change key 01 via lrp channelhf mfdes changekey -t des --newalgo aes --newkey 11223344556677889900112233445566 --newver a5 -> change card master key to aes one",
                 "hf mfdes changekey --aid 123456 -t aes --key 00000000000000000000000000000000 --newkey 11223344556677889900112233445566 -> change app master key",
                 "hf mfdes changekey --aid 123456 -t des -n 0 --newkeyno 1 --oldkey 5555555555555555 --newkey 1122334455667788 -> change key 1 with auth from key 0",
-                "hf mfdes changekey --aid 123456 -t 3tdea --newkey 11223344556677889900112233445566778899001122334 -> change 3tdea key 0 from default 00..00 to provided"
+                "hf mfdes changekey --aid 123456 -t 3tdea --newkey 112233445566778899001122334455667788990011223344 -> change 3tdea key 0 from default 00..00 to provided"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id of application (3 hex bytes, big endian)",
-                "--appisoid <isoid hex> application iso id (iso df id) (2 hex bytes, big endian).",
-                "--oldalgo <des/2tdea/3tdea/aes> old key crypto algorithm: des, 2tdea, 3tdea, aes",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id of application (3 hex bytes, big endian)",
+                "--isoid <hex> application iso id (iso df id) (2 hex bytes, big endian).",
+                "--oldalgo <des|2tdea|3tdea|aes> old key crypto algorithm",
                 "--oldkey <old key> old key (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "--newkeyno <keyno> key number for change",
-                "--newalgo <des/2tdea/3tdea/aes> new key crypto algorithm: des, 2tdea, 3tdea, aes",
-                "--newkey <new key> new key (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "--newver <version hex> new key's version (1 hex byte)"
+                "--newkeyno <dec> key number for change",
+                "--newalgo <des|2tdea|3tdea|aes> new key crypto algorithm",
+                "--newkey <hex> new key (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--newver <hex> version of new key (1 hex byte)"
             ],
-            "usage": "hf mfdes changekey [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>] [--appisoid <isoid hex>] [--oldalgo <des/2tdea/3tdea/aes>] [--oldkey <old key>] [--newkeyno <keyno>] [--newalgo <des/2tdea/3tdea/aes>] [--newkey <new key>] [--newver <version hex>]"
+            "usage": "hf mfdes changekey [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [--isoid <hex>] [--oldalgo <des|2tdea|3tdea|aes>] [--oldkey <old key>] [--newkeyno <dec>] [--newalgo <des|2tdea|3tdea|aes>] [--newkey <hex>] [--newver <hex>]"
         },
         "hf mfdes chfilesettings": {
             "command": "hf mfdes chfilesettings",
             "description": "get file settings from file from application. master key needs to be provided or flag --no-auth set (depend on cards settings).",
             "notes": [
                 "hf mfdes chfilesettings --aid 123456 --fid 01 --amode plain --rrights free --wrights free --rwrights free --chrights key0 -> change file settings app=123456, file=01 with defaults from `default` command",
-                "hf mfdes chfilesettings -n 0 -t des -k 0000000000000000 -f none --aid 123456 --fid 01 --rawdata 00eeee -> execute with default factory setup",
+                "hf mfdes chfilesettings -n 0 -t des -k 0000000000000000 --kdf none --aid 123456 --fid 01 --rawdata 00eeee -> execute with default factory setup",
                 "hf mfdes chfilesettings --aid 123456 --fid 01 --rawdata 810000021f112f22 -> change file settings with additional rights for keys 1 and 2",
-                "hf mfdes chfilesettings --appisoid df01 --fid 00 --amode plain --rawrights eee0 -s lrp -t aes -> change file settings via lrp channel"
+                "hf mfdes chfilesettings --isoid df01 --fid 00 --amode plain --rawrights eee0 -s lrp -t aes -> change file settings via lrp channel"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id (3 hex bytes, big endian)",
-                "--appisoid <isoid hex> application iso id (iso df id) (2 hex bytes, big endian).",
-                "--fid <file id hex> file id (1 hex byte)",
-                "--rawdata <file settings hex> file settings (hex > 5 bytes). have priority over the other settings.",
-                "--amode <plain/mac/encrypt> file access mode: plain/mac/encrypt",
-                "--rawrights <access rights hex> access rights for file (hex 2 byte) r/w/rw/chg, 0x0 - 0xd key, 0xe free, 0xf denied",
-                "--rrights <key0/../key13/free/deny> read file access mode: the specified key, free, deny",
-                "--wrights <key0/../key13/free/deny> write file access mode: the specified key, free, deny",
-                "--rwrights <key0/../key13/free/deny> read/write file access mode: the specified key, free, deny",
-                "--chrights <key0/../key13/free/deny> change file settings access mode: the specified key, free, deny",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id (3 hex bytes, big endian)",
+                "--isoid <hex> application iso id (iso df id) (2 hex bytes, big endian)",
+                "--fid <hex> file id (1 hex byte)",
+                "--rawdata <hex> file settings (hex > 5 bytes). have priority over the other settings",
+                "--amode <plain|mac|encrypt> file access mode",
+                "--rawrights <hex> access rights for file (2 hex bytes) r/w/rw/chg, 0x0 - 0xd key, 0xe free, 0xf denied",
+                "--rrights <key0..13|free|deny> read file access mode: the specified key, free, deny",
+                "--wrights <key0..13|free|deny> write file access mode: the specified key, free, deny",
+                "--rwrights <key0..13|free|deny> read/write file access mode: the specified key, free, deny",
+                "--chrights <key0..13|free|deny> change file settings access mode: the specified key, free, deny",
                 "--no-auth execute without authentication"
             ],
-            "usage": "hf mfdes chfilesettings [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>] [--appisoid <isoid hex>] [--fid <file id hex>] [--rawdata <file settings hex>] [--amode <plain/mac/encrypt>] [--rawrights <access rights hex>] [--rrights <key0/../key13/free/deny>] [--wrights <key0/../key13/free/deny>] [--rwrights <key0/../key13/free/deny>] [--chrights <key0/../key13/free/deny>] [--no-auth]"
+            "usage": "hf mfdes chfilesettings [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [--isoid <hex>] [--fid <hex>] [--rawdata <hex>] [--amode <plain|mac|encrypt>] [--rawrights <hex>] [--rrights <key0..13|free|deny>] [--wrights <key0..13|free|deny>] [--rwrights <key0..13|free|deny>] [--chrights <key0..13|free|deny>] [--no-auth]"
         },
         "hf mfdes chk": {
             "command": "hf mfdes chk",
@@ -4365,19 +4421,19 @@
             "offline": false,
             "options": [
                 "-h, --help this help",
-                "--aid <aid> use specific aid (3 hex bytes, big endian)",
-                "-k, --key <key> key for checking (hex 16 bytes)",
-                "-d, --dict <file> file with keys dictionary",
+                "--aid <hex> use specific aid (3 hex bytes, big endian)",
+                "-k, --key <hex> key for checking (hex 16 bytes)",
+                "-d, --dict <fn> dictionary file with keys",
                 "--pattern1b check all 1-byte combinations of key (0000...0000, 0101...0101, 0202...0202, ...)",
                 "--pattern2b check all 2-byte combinations of key (0000...0000, 0001...0001, 0002...0002, ...)",
                 "--startp2b <pattern> start key (2-byte hex) for 2-byte search (use with `--pattern2b`)",
-                "-j, --json <file> json file to save keys",
-                "-v, --verbose verbose mode.",
-                "-f, --kdf <kdf> key derivation function (kdf) (0=none, 1=an10922, 2=gallagher)",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
+                "-j, --json <fn> json file name to save keys",
+                "-v, --verbose verbose mode",
+                "--kdf <0|1|2> key derivation function (kdf) (0=none, 1=an10922, 2=gallagher)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
                 "-a, --apdu show apdu requests and responses"
             ],
-            "usage": "hf mfdes chk [-hva] [--aid <aid>] [-k <key>] [-d <file>] [--pattern1b] [--pattern2b] [--startp2b <pattern>] [-j <file>] [-f <kdf>] [-i <kdfi>]"
+            "usage": "hf mfdes chk [-hva] [--aid <hex>] [-k <hex>] [-d <fn>] [--pattern1b] [--pattern2b] [--startp2b <pattern>] [-j <fn>] [--kdf <0|1|2>] [-i <hex>]"
         },
         "hf mfdes chkeysettings": {
             "command": "hf mfdes chkeysettings",
@@ -4390,46 +4446,46 @@
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id (3 hex bytes, big endian)",
-                "-d, --data <key settings hex> key settings (hex 1 byte)"
-            ],
-            "usage": "hf mfdes chkeysettings [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>] [-d <key settings hex>]"
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id (3 hex bytes, big endian)",
+                "-d, --data <hex> key settings (1 hex byte)"
+            ],
+            "usage": "hf mfdes chkeysettings [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [-d <hex>]"
         },
         "hf mfdes clearrecfile": {
             "command": "hf mfdes clearrecfile",
             "description": "clear record file. master key needs to be provided or flag --no-auth set (depend on cards settings).",
             "notes": [
                 "hf mfdes clearrecfile --aid 123456 --fid 01 -> clear record file for: app=123456, file=01 with defaults from `default` command",
-                "hf mfdes clearrecfile --appisoid df01 --fid 01 -s lrp -t aes -n 3 -> clear record file for lrp channel with key number 3"
+                "hf mfdes clearrecfile --isoid df01 --fid 01 -s lrp -t aes -n 3 -> clear record file for lrp channel with key number 3"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id (3 hex bytes, big endian)",
-                "--appisoid <isoid hex> application iso id (iso df id) (2 hex bytes, big endian).",
-                "--fid <file id hex> file id for clearing (1 hex byte)",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id (3 hex bytes, big endian)",
+                "--isoid <hex> application iso id (iso df id) (2 hex bytes, big endian)",
+                "--fid <hex> file id for clearing (1 hex byte)",
                 "--no-auth execute without authentication"
             ],
-            "usage": "hf mfdes clearrecfile [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>] [--appisoid <isoid hex>] [--fid <file id hex>] [--no-auth]"
+            "usage": "hf mfdes clearrecfile [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [--isoid <hex>] [--fid <hex>] [--no-auth]"
         },
         "hf mfdes createapp": {
             "command": "hf mfdes createapp",
@@ -4472,26 +4528,26 @@
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
+                "-v, --verbose verbose mode",
                 "-n, --keyno <dec> key number",
-                "-t, --algo <des|2tdea|3tdea|aes> crypt algo: des, 2tdea, 3tdea, aes",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
                 "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none|an10922|gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain|mac|encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native|niso|iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40|ev1|ev2|lrp> secure channel: d40/ev1/ev2/lrp",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
                 "--rawdata <hex> raw data that sends to command",
                 "--aid <hex> application id for create. mandatory. (3 hex bytes, big endian)",
-                "--fid <hex> iso file id. forbidden values: 0000 3f00, 3fff, ffff. (2 hex bytes, big endian). if specified - enable iso file id over all the files in the app.",
-                "--dfname <string> iso df name 1..16 chars string",
-                "--ks1 <hex> key settings 1 (hex 1 byte). application master key settings. default 0x0f",
-                "--ks2 <hex> key settings 2 (hex 1 byte). default 0x0e",
-                "--dstalgo <des|2tdea|3tdea|aes> application key crypt algo: des, 2tdea, 3tdea, aes. default des",
-                "--numkeys <dec> number of keys 0x00..0x0e. default 0x0e",
+                "--fid <hex> iso file id. forbidden values: 0000 3f00, 3fff, ffff. (2 hex bytes, big endian)",
+                "--dfname <string> iso df name (1..16 chars)",
+                "--ks1 <hex> key settings 1 (1 hex byte). application master key settings (def: 0x0f)",
+                "--ks2 <hex> key settings 2 (1 hex byte). (def: 0x0e)",
+                "--dstalgo <des|2tdea|3tdea|aes> application key crypt algo (def: des)",
+                "--numkeys <dec> number of keys 0x00..0x0e (def: 0x0e)",
                 "--no-auth execute without authentication"
             ],
-            "usage": "hf mfdes createapp [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [-f <none|an10922|gallagher>] [-i <kdfi>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [-s <d40|ev1|ev2|lrp>] [--rawdata <hex>] [--aid <hex>] [--fid <hex>] [--dfname <string>] [--ks1 <hex>] [--ks2 <hex>] [--dstalgo <des|2tdea|3tdea|aes>] [--numkeys <dec>] [--no-auth]"
+            "usage": "hf mfdes createapp [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--rawdata <hex>] [--aid <hex>] [--fid <hex>] [--dfname <string>] [--ks1 <hex>] [--ks2 <hex>] [--dstalgo <des|2tdea|3tdea|aes>] [--numkeys <dec>] [--no-auth]"
         },
         "hf mfdes createfile": {
             "command": "hf mfdes createfile",
@@ -4503,37 +4559,37 @@
                 "hf mfdes createfile --aid 123456 --fid 01 --isofid 0001 --size 000010 -> create file with iso id. authentication with defaults from `default` command",
                 "hf mfdes createfile --aid 123456 --fid 01 --rawtype 01 --rawdata 000100eeee000100 -> create file via sending rawdata to the card. can be used to create any type of file. authentication with defaults from `default` command",
                 "hf mfdes createfile --aid 123456 --fid 01 --amode plain --rrights free --wrights free --rwrights free --chrights key0 -> create file app=123456, file=01 and mentioned rights with defaults from `default` command",
-                "hf mfdes createfile -n 0 -t des -k 0000000000000000 -f none --aid 123456 --fid 01 --rawtype 00 --rawdata 00eeee000100 -> execute with default factory setup"
+                "hf mfdes createfile -n 0 -t des -k 0000000000000000 --kdf none --aid 123456 --fid 01 --rawtype 00 --rawdata 00eeee000100 -> execute with default factory setup"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id (3 hex bytes, big endian)",
-                "--fid <file id hex> file id (1 hex byte)",
-                "--isofid <iso file id hex> iso file id (2 hex bytes)",
-                "--rawtype <file type hex 1b> raw file type (hex 1 byte)",
-                "--rawdata <file settings hex> raw file settings (hex > 5 bytes)",
-                "--amode <plain/mac/encrypt> file access mode: plain/mac/encrypt",
-                "--rawrights <access rights hex> access rights for file (hex 2 byte) r/w/rw/chg, 0x0 - 0xd key, 0xe free, 0xf denied",
-                "--rrights <key0/../key13/free/deny> read file access mode: the specified key, free, deny",
-                "--wrights <key0/../key13/free/deny> write file access mode: the specified key, free, deny",
-                "--rwrights <key0/../key13/free/deny> read/write file access mode: the specified key, free, deny",
-                "--chrights <key0/../key13/free/deny> change file settings access mode: the specified key, free, deny",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id (3 hex bytes, big endian)",
+                "--fid <hex> file id (1 hex byte)",
+                "--isofid <hex> iso file id (2 hex bytes)",
+                "--rawtype <hex> raw file type (1 hex byte)",
+                "--rawdata <hex> raw file settings (hex > 5 bytes)",
+                "--amode <plain|mac|encrypt> file access mode",
+                "--rawrights <hex> access rights for file (2 hex bytes) r/w/rw/chg, 0x0 - 0xd key, 0xe free, 0xf denied",
+                "--rrights <key0..key13|free|deny> read file access mode: the specified key, free, deny",
+                "--wrights <key0..key13|free|deny> write file access mode: the specified key, free, deny",
+                "--rwrights <key0..key13|free|deny> read/write file access mode: the specified key, free, deny",
+                "--chrights <key0..key13|free|deny> change file settings access mode: the specified key, free, deny",
                 "--no-auth execute without authentication",
                 "--size <hex> file size (3 hex bytes, big endian)",
                 "--backup create backupfile instead of standard file"
             ],
-            "usage": "hf mfdes createfile [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>] [--fid <file id hex>] [--isofid <iso file id hex>] [--rawtype <file type hex 1b>] [--rawdata <file settings hex>] [--amode <plain/mac/encrypt>] [--rawrights <access rights hex>] [--rrights <key0/../key13/free/deny>] [--wrights <key0/../key13/free/deny>] [--rwrights <key0/../key13/free/deny>] [--chrights <key0/../key13/free/deny>] [--no-auth] [--size <hex>] [--backup]"
+            "usage": "hf mfdes createfile [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [--fid <hex>] [--isofid <hex>] [--rawtype <hex>] [--rawdata <hex>] [--amode <plain|mac|encrypt>] [--rawrights <hex>] [--rrights <key0..key13|free|deny>] [--wrights <key0..key13|free|deny>] [--rwrights <key0..key13|free|deny>] [--chrights <key0..key13|free|deny>] [--no-auth] [--size <hex>] [--backup]"
         },
         "hf mfdes createmacfile": {
             "command": "hf mfdes createmacfile",
@@ -4545,37 +4601,37 @@
                 "",
                 "hf mfdes createmacfile --aid 123456 --fid 01 --rawrights 0ff0 --mackey 00112233445566778899aabbccddeeff --mackeyver 01 -> create transaction mac file with parameters. rights from default. authentication with defaults from `default` command",
                 "hf mfdes createmacfile --aid 123456 --fid 01 --amode plain --rrights free --wrights deny --rwrights free --chrights key0 --mackey 00112233445566778899aabbccddeeff -> create file app=123456, file=01, with key, and mentioned rights with defaults from `default` command",
-                "hf mfdes createmacfile -n 0 -t des -k 0000000000000000 -f none --aid 123456 --fid 01 -> execute with default factory setup. key and keyver == 0x00..00",
-                "hf mfdes createmacfile --appisoid df01 --fid 0f -s lrp -t aes --rawrights 0ff0 --mackey 00112233445566778899aabbccddeeff --mackeyver 01 -> create transaction mac file via lrp channel",
-                "hf mfdes createmacfile --appisoid df01 --fid 0f -s lrp -t aes --rawrights 0f10 --mackey 00112233445566778899aabbccddeeff --mackeyver 01 -> create transaction mac file via lrp channel with commitreaderid command enable"
+                "hf mfdes createmacfile -n 0 -t des -k 0000000000000000 --kdf none --aid 123456 --fid 01 -> execute with default factory setup. key and keyver == 0x00..00",
+                "hf mfdes createmacfile --isoid df01 --fid 0f -s lrp -t aes --rawrights 0ff0 --mackey 00112233445566778899aabbccddeeff --mackeyver 01 -> create transaction mac file via lrp channel",
+                "hf mfdes createmacfile --isoid df01 --fid 0f -s lrp -t aes --rawrights 0f10 --mackey 00112233445566778899aabbccddeeff --mackeyver 01 -> create transaction mac file via lrp channel with commitreaderid command enable"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id (3 hex bytes, big endian)",
-                "--appisoid <isoid hex> application iso id (iso df id) (2 hex bytes, big endian).",
-                "--fid <file id hex> file id (1 hex byte)",
-                "--amode <plain/mac/encrypt> file access mode: plain/mac/encrypt",
-                "--rawrights <access rights hex> access rights for file (hex 2 byte) r/w/rw/chg, 0x0 - 0xd key, 0xe free, 0xf denied",
-                "--rrights <key0/../key13/free/deny> read file access mode: the specified key, free, deny",
-                "--wrights <key0/../key13/free/deny> write file access mode: the specified key, free, deny",
-                "--rwrights <key0/../key13/free/deny> read/write file access mode: the specified key, free, deny",
-                "--chrights <key0/../key13/free/deny> change file settings access mode: the specified key, free, deny",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id (3 hex bytes, big endian)",
+                "--isoid <hex> application iso id (iso df id) (2 hex bytes, big endian)",
+                "--fid <hex> file id (1 hex byte)",
+                "--amode <plain|mac|encrypt> file access mode",
+                "--rawrights <hex> access rights for file (2 hex bytes) r/w/rw/chg, 0x0 - 0xd key, 0xe free, 0xf denied",
+                "--rrights <key0..key13|free|deny> read file access mode: the specified key, free, deny",
+                "--wrights <key0..key13|free|deny> write file access mode: the specified key, free, deny",
+                "--rwrights <key0..key13|free|deny> read/write file access mode: the specified key, free, deny",
+                "--chrights <key0..key13|free|deny> change file settings access mode: the specified key, free, deny",
                 "--no-auth execute without authentication",
-                "--mackey <hex> aes-128 key for mac (16 hex bytes, big endian). default 0x00..00",
-                "--mackeyver <ver hex 1b> aes key version for mac (1 hex byte). default 0x00"
+                "--mackey <hex> aes-128 key for mac (16 hex bytes, big endian). (def: all zeros)",
+                "--mackeyver <hex> aes key version for mac (1 hex byte). (def: 0x0)"
             ],
-            "usage": "hf mfdes createmacfile [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>] [--appisoid <isoid hex>] [--fid <file id hex>] [--amode <plain/mac/encrypt>] [--rawrights <access rights hex>] [--rrights <key0/../key13/free/deny>] [--wrights <key0/../key13/free/deny>] [--rwrights <key0/../key13/free/deny>] [--chrights <key0/../key13/free/deny>] [--no-auth] [--mackey <hex>] [--mackeyver <ver hex 1b>]"
+            "usage": "hf mfdes createmacfile [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [--isoid <hex>] [--fid <hex>] [--amode <plain|mac|encrypt>] [--rawrights <hex>] [--rrights <key0..key13|free|deny>] [--wrights <key0..key13|free|deny>] [--rwrights <key0..key13|free|deny>] [--chrights <key0..key13|free|deny>] [--no-auth] [--mackey <hex>] [--mackeyver <hex>]"
         },
         "hf mfdes createrecordfile": {
             "command": "hf mfdes createrecordfile",
@@ -4585,36 +4641,36 @@
                 "key/mode/etc of the authentication depends on application settings",
                 "hf mfdes createrecordfile --aid 123456 --fid 01 --size 000010 --maxrecord 000010 --cyclic -> create cyclic record file with parameters. rights from default. authentication with defaults from `default` command",
                 "hf mfdes createrecordfile --aid 123456 --fid 01 --amode plain --rrights free --wrights free --rwrights free --chrights key0 --size 000010 --maxrecord 000010 -> create linear record file app=123456, file=01 and mentioned rights with defaults from `default` command",
-                "hf mfdes createrecordfile -n 0 -t des -k 0000000000000000 -f none --aid 123456 --fid 01 --size 000010 --maxrecord 000010 -> execute with default factory setup"
+                "hf mfdes createrecordfile -n 0 -t des -k 0000000000000000 --kdf none --aid 123456 --fid 01 --size 000010 --maxrecord 000010 -> execute with default factory setup"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id (3 hex bytes, big endian)",
-                "--fid <file id hex> file id (1 hex byte)",
-                "--isofid <iso file id hex> iso file id (2 hex bytes)",
-                "--amode <plain/mac/encrypt> file access mode: plain/mac/encrypt",
-                "--rawrights <access rights hex> access rights for file (hex 2 byte) r/w/rw/chg, 0x0 - 0xd key, 0xe free, 0xf denied",
-                "--rrights <key0/../key13/free/deny> read file access mode: the specified key, free, deny",
-                "--wrights <key0/../key13/free/deny> write file access mode: the specified key, free, deny",
-                "--rwrights <key0/../key13/free/deny> read/write file access mode: the specified key, free, deny",
-                "--chrights <key0/../key13/free/deny> change file settings access mode: the specified key, free, deny",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id (3 hex bytes, big endian)",
+                "--fid <hex> file id (1 hex byte)",
+                "--isofid <hex> iso file id (2 hex bytes)",
+                "--amode <plain|mac|encrypt> file access mode",
+                "--rawrights <hex> access rights for file (2 hex bytes) r/w/rw/chg, 0x0 - 0xd key, 0xe free, 0xf denied",
+                "--rrights <key0..key13|free|deny> read file access mode: the specified key, free, deny",
+                "--wrights <key0..key13|free|deny> write file access mode: the specified key, free, deny",
+                "--rwrights <key0..key13|free|deny> read/write file access mode: the specified key, free, deny",
+                "--chrights <key0..key13|free|deny> change file settings access mode: the specified key, free, deny",
                 "--no-auth execute without authentication",
                 "--size <hex> record size (3 hex bytes, big endian, 000001 to ffffff)",
                 "--maxrecord <hex> max. number of records (3 hex bytes, big endian)",
                 "--cyclic create cyclic record file instead of linear record file"
             ],
-            "usage": "hf mfdes createrecordfile [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>] [--fid <file id hex>] [--isofid <iso file id hex>] [--amode <plain/mac/encrypt>] [--rawrights <access rights hex>] [--rrights <key0/../key13/free/deny>] [--wrights <key0/../key13/free/deny>] [--rwrights <key0/../key13/free/deny>] [--chrights <key0/../key13/free/deny>] [--no-auth] [--size <hex>] [--maxrecord <hex>] [--cyclic]"
+            "usage": "hf mfdes createrecordfile [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [--fid <hex>] [--isofid <hex>] [--amode <plain|mac|encrypt>] [--rawrights <hex>] [--rrights <key0..key13|free|deny>] [--wrights <key0..key13|free|deny>] [--rwrights <key0..key13|free|deny>] [--chrights <key0..key13|free|deny>] [--no-auth] [--size <hex>] [--maxrecord <hex>] [--cyclic]"
         },
         "hf mfdes createvaluefile": {
             "command": "hf mfdes createvaluefile",
@@ -4624,56 +4680,56 @@
                 "key/mode/etc of the authentication depends on application settings",
                 "hf mfdes createvaluefile --aid 123456 --fid 01 --lower 00000010 --upper 00010000 --value 00000100 -> create file with parameters. rights from default. authentication with defaults from `default` command",
                 "hf mfdes createvaluefile --aid 123456 --fid 01 --amode plain --rrights free --wrights free --rwrights free --chrights key0 -> create file app=123456, file=01 and mentioned rights with defaults from `default` command",
-                "hf mfdes createvaluefile -n 0 -t des -k 0000000000000000 -f none --aid 123456 --fid 01 -> execute with default factory setup"
+                "hf mfdes createvaluefile -n 0 -t des -k 0000000000000000 --kdf none --aid 123456 --fid 01 -> execute with default factory setup"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id (3 hex bytes, big endian)",
-                "--fid <file id hex> file id (1 hex byte)",
-                "--amode <plain/mac/encrypt> file access mode: plain/mac/encrypt",
-                "--rawrights <access rights hex> access rights for file (hex 2 byte) r/w/rw/chg, 0x0 - 0xd key, 0xe free, 0xf denied",
-                "--rrights <key0/../key13/free/deny> read file access mode: the specified key, free, deny",
-                "--wrights <key0/../key13/free/deny> write file access mode: the specified key, free, deny",
-                "--rwrights <key0/../key13/free/deny> read/write file access mode: the specified key, free, deny",
-                "--chrights <key0/../key13/free/deny> change file settings access mode: the specified key, free, deny",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id (3 hex bytes, big endian)",
+                "--fid <hex> file id (1 hex byte)",
+                "--amode <plain|mac|encrypt> file access mode",
+                "--rawrights <hex> access rights for file (2 hex bytes) r/w/rw/chg, 0x0 - 0xd key, 0xe free, 0xf denied",
+                "--rrights <key0..key13|free|deny> read file access mode: the specified key, free, deny",
+                "--wrights <key0..key13|free|deny> write file access mode: the specified key, free, deny",
+                "--rwrights <key0..key13|free|deny> read/write file access mode: the specified key, free, deny",
+                "--chrights <key0..key13|free|deny> change file settings access mode: the specified key, free, deny",
                 "--no-auth execute without authentication",
                 "--lower <hex> lower limit (4 hex bytes, big endian)",
                 "--upper <hex> upper limit (4 hex bytes, big endian)",
                 "--value <hex> value (4 hex bytes, big endian)",
                 "--lcredit <dec> limited credit enabled (bit 0 = limited credit, 1 = freevalue)"
             ],
-            "usage": "hf mfdes createvaluefile [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>] [--fid <file id hex>] [--amode <plain/mac/encrypt>] [--rawrights <access rights hex>] [--rrights <key0/../key13/free/deny>] [--wrights <key0/../key13/free/deny>] [--rwrights <key0/../key13/free/deny>] [--chrights <key0/../key13/free/deny>] [--no-auth] [--lower <hex>] [--upper <hex>] [--value <hex>] [--lcredit <dec>]"
+            "usage": "hf mfdes createvaluefile [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [--fid <hex>] [--amode <plain|mac|encrypt>] [--rawrights <hex>] [--rrights <key0..key13|free|deny>] [--wrights <key0..key13|free|deny>] [--rwrights <key0..key13|free|deny>] [--chrights <key0..key13|free|deny>] [--no-auth] [--lower <hex>] [--upper <hex>] [--value <hex>] [--lcredit <dec>]"
         },
         "hf mfdes default": {
             "command": "hf mfdes default",
-            "description": "set default parameters for access to desfire card.",
+            "description": "set default parameters for access to mifare desfire card.",
             "notes": [
-                "hf mfdes default -n 0 -t des -k 0000000000000000 -f none -> save to the default parameters"
+                "hf mfdes default -n 0 -t des -k 0000000000000000 --kdf none -> save to the default parameters"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp"
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel"
             ],
-            "usage": "hf mfdes default [-h] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>]"
+            "usage": "hf mfdes default [-h] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>]"
         },
         "hf mfdes deleteapp": {
             "command": "hf mfdes deleteapp",
@@ -4685,101 +4741,101 @@
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id of delegated application (3 hex bytes, big endian)"
-            ],
-            "usage": "hf mfdes deleteapp [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>]"
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id of delegated application (3 hex bytes, big endian)"
+            ],
+            "usage": "hf mfdes deleteapp [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>]"
         },
         "hf mfdes deletefile": {
             "command": "hf mfdes deletefile",
             "description": "delete file from application. master key needs to be provided or flag --no-auth set (depend on cards settings).",
             "notes": [
                 "hf mfdes deletefile --aid 123456 --fid 01 -> delete file for: app=123456, file=01 with defaults from `default` command",
-                "hf mfdes deletefile --appisoid df01 --fid 0f -s lrp -t aes -> delete file for lrp channel"
+                "hf mfdes deletefile --isoid df01 --fid 0f -s lrp -t aes -> delete file for lrp channel"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id (3 hex bytes, big endian)",
-                "--appisoid <isoid hex> application iso id (iso df id) (2 hex bytes, big endian).",
-                "--fid <file id hex> file id (1 hex byte)",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id (3 hex bytes, big endian)",
+                "--isoid <hex> application iso id (iso df id) (2 hex bytes, big endian)",
+                "--fid <hex> file id (1 hex byte)",
                 "--no-auth execute without authentication"
             ],
-            "usage": "hf mfdes deletefile [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>] [--appisoid <isoid hex>] [--fid <file id hex>] [--no-auth]"
+            "usage": "hf mfdes deletefile [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [--isoid <hex>] [--fid <hex>] [--no-auth]"
         },
         "hf mfdes detect": {
             "command": "hf mfdes detect",
             "description": "detect key type and tries to find one from the list.",
             "notes": [
                 "hf mfdes detect -> detect key 0 from picc level",
-                "hf mfdes detect -s d40 -> detect key 0 from picc level via secure channel d40",
+                "hf mfdes detect --schann d40 -> detect key 0 from picc level via secure channel d40",
                 "hf mfdes detect --dict mfdes_default_keys -> detect key 0 from picc level with help of the standard dictionary",
                 "hf mfdes detect --aid 123456 -n 2 --save -> detect key 2 from app 123456 and if succeed - save params to defaults (`default` command)",
-                "hf mfdes detect --appisoid df01 --save -> detect key 0 and save to defaults with card in the lrp mode"
+                "hf mfdes detect --isoid df01 --save -> detect key 0 and save to defaults with card in the lrp mode"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id (3 hex bytes, big endian)",
-                "--appisoid <isoid hex> application iso id (iso df id) (2 hex bytes, big endian).",
-                "--dict <file> file with keys dictionary",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id (3 hex bytes, big endian)",
+                "--isoid <hex> application iso id (iso df id) (2 hex bytes, big endian).",
+                "--dict <fn> dictionary file name with keys",
                 "--save save found key and parameters to defaults"
             ],
-            "usage": "hf mfdes detect [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>] [--appisoid <isoid hex>] [--dict <file>] [--save]"
+            "usage": "hf mfdes detect [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [--isoid <hex>] [--dict <fn>] [--save]"
         },
         "hf mfdes dump": {
             "command": "hf mfdes dump",
             "description": "for each application show fil list and then file content. key needs to be provided for authentication or flag --no-auth set (depend on cards settings).",
             "notes": [
-                "hf mfdes dump --aid 123456 -> show file dump for: app=123456 with channel defaults from `default` command/nhf mfdes dump --appisoid df01 -s lrp -t aes --length 000090 -> lrp default settings with length limit"
+                "hf mfdes dump --aid 123456 -> show file dump for: app=123456 with channel defaults from `default` command/nhf mfdes dump --isoid df01 -s lrp -t aes --length 000090 -> lrp default settings with length limit"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id (3 hex bytes, big endian)",
-                "--appisoid <isoid hex> application iso id (iso df id) (2 hex bytes, big endian).",
-                "-l, --length <hex> maximum length for read data files (3 hex bytes, big endian).",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id (3 hex bytes, big endian)",
+                "--isoid <hex> application iso id (iso df id) (2 hex bytes, big endian)",
+                "-l, --length <hex> maximum length for read data files (3 hex bytes, big endian)",
                 "--no-auth execute without authentication"
             ],
-            "usage": "hf mfdes dump [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>] [--appisoid <isoid hex>] [-l <hex>] [--no-auth]"
+            "usage": "hf mfdes dump [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [--isoid <hex>] [-l <hex>] [--no-auth]"
         },
         "hf mfdes formatpicc": {
             "command": "hf mfdes formatpicc",
@@ -4791,18 +4847,18 @@
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id of delegated application (3 hex bytes, big endian)"
-            ],
-            "usage": "hf mfdes formatpicc [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>]"
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id of delegated application (3 hex bytes, big endian)"
+            ],
+            "usage": "hf mfdes formatpicc [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>]"
         },
         "hf mfdes freemem": {
             "command": "hf mfdes freemem",
@@ -4814,146 +4870,146 @@
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
                 "--no-auth execute without authentication"
             ],
-            "usage": "hf mfdes getfreemem [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--no-auth]"
+            "usage": "hf mfdes getfreemem [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--no-auth]"
         },
         "hf mfdes getaids": {
             "command": "hf mfdes getaids",
             "description": "get application ids list from card. master key needs to be provided or flag --no-auth set.",
             "notes": [
-                "hf mfdes getaids -n 0 -t des -k 0000000000000000 -f none -> execute with default factory setup"
+                "hf mfdes getaids -n 0 -t des -k 0000000000000000 --kdf none -> execute with default factory setup"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
                 "--no-auth execute without authentication"
             ],
-            "usage": "hf mfdes getaids [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--no-auth]"
+            "usage": "hf mfdes getaids [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--no-auth]"
         },
         "hf mfdes getappnames": {
             "command": "hf mfdes getappnames",
             "description": "get application ids, iso ids and df names from card. master key needs to be provided or flag --no-auth set.",
             "notes": [
-                "hf mfdes getappnames -n 0 -t des -k 0000000000000000 -f none -> execute with default factory setup"
+                "hf mfdes getappnames -n 0 -t des -k 0000000000000000 --kdf none -> execute with default factory setup"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
                 "--no-auth execute without authentication"
             ],
-            "usage": "hf mfdes getappnames [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--no-auth]"
+            "usage": "hf mfdes getappnames [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--no-auth]"
         },
         "hf mfdes getfileids": {
             "command": "hf mfdes getfileids",
             "description": "get file ids list from card. master key needs to be provided or flag --no-auth set.",
             "notes": [
                 "hf mfdes getfileids --aid 123456 -> execute with defaults from `default` command",
-                "hf mfdes getfileids -n 0 -t des -k 0000000000000000 -f none --aid 123456 -> execute with default factory setup"
+                "hf mfdes getfileids -n 0 -t des -k 0000000000000000 --kdf none --aid 123456 -> execute with default factory setup"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id (3 hex bytes, big endian)",
-                "--appisoid <isoid hex> application iso id (iso df id) (2 hex bytes, big endian).",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id (3 hex bytes, big endian)",
+                "--isoid <hex> application iso id (iso df id) (2 hex bytes, big endian).",
                 "--no-auth execute without authentication"
             ],
-            "usage": "hf mfdes getfileids [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>] [--appisoid <isoid hex>] [--no-auth]"
+            "usage": "hf mfdes getfileids [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [--isoid <hex>] [--no-auth]"
         },
         "hf mfdes getfileisoids": {
             "command": "hf mfdes getfileisoids",
             "description": "get file ids list from card. master key needs to be provided or flag --no-auth set.",
             "notes": [
                 "hf mfdes getfileisoids --aid 123456 -> execute with defaults from `default` command",
-                "hf mfdes getfileisoids -n 0 -t des -k 0000000000000000 -f none --aid 123456 -> execute with default factory setup",
-                "hf mfdes getfileisoids --appisoid df01 -> get iso file ids from desfire light with factory card settings",
-                "hf mfdes getfileisoids --appisoid df01 -s lrp -t aes -> get iso file ids from desfire light via lrp channel with default key authentication"
+                "hf mfdes getfileisoids -n 0 -t des -k 0000000000000000 --kdf none --aid 123456 -> execute with default factory setup",
+                "hf mfdes getfileisoids --isoid df01 -> get iso file ids from desfire light with factory card settings",
+                "hf mfdes getfileisoids --isoid df01 -s lrp -t aes -> get iso file ids from desfire light via lrp channel with default key authentication"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id (3 hex bytes, big endian)",
-                "--appisoid <isoid hex> application iso id (iso df id) (2 hex bytes, big endian).",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id (3 hex bytes, big endian)",
+                "--isoid <hex> application iso id (iso df id) (2 hex bytes, big endian).",
                 "--no-auth execute without authentication"
             ],
-            "usage": "hf mfdes getfileisoids [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>] [--appisoid <isoid hex>] [--no-auth]"
+            "usage": "hf mfdes getfileisoids [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [--isoid <hex>] [--no-auth]"
         },
         "hf mfdes getfilesettings": {
             "command": "hf mfdes getfilesettings",
             "description": "get file settings from file from application. master key needs to be provided or flag --no-auth set (depend on cards settings).",
             "notes": [
                 "hf mfdes getfilesettings --aid 123456 --fid 01 -> execute with defaults from `default` command",
-                "hf mfdes getfilesettings --appisoid df01 --fid 00 --no-auth -> get file settings with select by iso id",
-                "hf mfdes getfilesettings -n 0 -t des -k 0000000000000000 -f none --aid 123456 --fid 01 -> execute with default factory setup"
+                "hf mfdes getfilesettings --isoid df01 --fid 00 --no-auth -> get file settings with select by iso id",
+                "hf mfdes getfilesettings -n 0 -t des -k 0000000000000000 --kdf none --aid 123456 --fid 01 -> execute with default factory setup"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id (3 hex bytes, big endian)",
-                "--appisoid <isoid hex> application iso id (iso df id) (2 hex bytes, big endian).",
-                "--fid <file id hex> file id (1 hex byte). default: 1",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id (3 hex bytes, big endian)",
+                "--isoid <hex> application iso id (iso df id) (2 hex bytes, big endian)",
+                "--fid <hex> file id (1 hex byte). (def: 1)",
                 "--no-auth execute without authentication"
             ],
-            "usage": "hf mfdes getfilesettings [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>] [--appisoid <isoid hex>] [--fid <file id hex>] [--no-auth]"
+            "usage": "hf mfdes getfilesettings [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [--isoid <hex>] [--fid <hex>] [--no-auth]"
         },
         "hf mfdes getkeysettings": {
             "command": "hf mfdes getkeysettings",
@@ -4966,18 +5022,18 @@
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id (3 hex bytes, big endian)"
-            ],
-            "usage": "hf mfdes getkeysettings [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>]"
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id (3 hex bytes, big endian)"
+            ],
+            "usage": "hf mfdes getkeysettings [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>]"
         },
         "hf mfdes getkeyversions": {
             "command": "hf mfdes getkeyversions",
@@ -4992,47 +5048,47 @@
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number for authentication",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id (3 hex bytes, big endian)",
-                "--appisoid <isoid hex> application iso id (iso df id) (2 hex bytes, big endian).",
-                "--keynum <key number hex> key number/count (hex 1 byte). default 0x00.",
-                "--keyset <keyset num hex> keyset number (hex 1 byte)",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number for authentication",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id (3 hex bytes, big endian)",
+                "--isoid <hex> application iso id (iso df id) (2 hex bytes, big endian).",
+                "--keynum <hex> key number/count (1 hex byte). (def: 0x00)",
+                "--keyset <hex> keyset number (1 hex byte)",
                 "--no-auth execute without authentication"
             ],
-            "usage": "hf mfdes getkeyversions [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>] [--appisoid <isoid hex>] [--keynum <key number hex>] [--keyset <keyset num hex>] [--no-auth]"
+            "usage": "hf mfdes getkeyversions [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [--isoid <hex>] [--keynum <hex>] [--keyset <hex>] [--no-auth]"
         },
         "hf mfdes getuid": {
             "command": "hf mfdes getuid",
             "description": "get uid from card. get the real uid if the random uid bit is on and get the same uid as in anticollision if not. any card's key needs to be provided.",
             "notes": [
                 "hf mfdes getuid -> execute with default factory setup",
-                "hf mfdes getuid --appisoid df01 -t aes -s lrp -> for desfire lights default settings"
+                "hf mfdes getuid --isoid df01 -t aes -s lrp -> for desfire lights default settings"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id (3 hex bytes, big endian)",
-                "--appisoid <isoid hex> application iso id (iso df id) (2 hex bytes, big endian)."
-            ],
-            "usage": "hf mfdes getuid [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>] [--appisoid <isoid hex>]"
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id (3 hex bytes, big endian)",
+                "--isoid <hex> application iso id (iso df id) (2 hex bytes, big endian)"
+            ],
+            "usage": "hf mfdes getuid [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [--isoid <hex>]"
         },
         "hf mfdes help": {
             "command": "hf mfdes help",
@@ -5072,51 +5128,51 @@
             "description": "show application list. master key needs to be provided or flag --no-auth set (depend on cards settings).",
             "notes": [
                 "hf mfdes lsapp -> show application list with defaults from `default` command",
-                "hf mfdes lsapp --files -> show application list and show each file type/settings/etc for each application"
+                "hf mfdes lsapp --files -> show application list and show each file type/settings/etc"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
                 "--no-auth execute without authentication",
                 "--no-deep not to check authentication commands that avail for any application",
-                "--files scan files and print file settings for each application"
+                "--files scan files and print file settings"
             ],
-            "usage": "hf mfdes lsapp [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--no-auth] [--no-deep] [--files]"
+            "usage": "hf mfdes lsapp [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--no-auth] [--no-deep] [--files]"
         },
         "hf mfdes lsfiles": {
             "command": "hf mfdes lsfiles",
             "description": "show file list. master key needs to be provided or flag --no-auth set (depend on cards settings).",
             "notes": [
-                "hf mfdes lsfiles --aid 123456 -> show file list for: app=123456 with defaults from `default` commandhf mfdes lsfiles --appisoid df01 --no-auth -> show files from desfire light"
+                "hf mfdes lsfiles --aid 123456 -> show file list for: app=123456 with defaults from `default` commandhf mfdes lsfiles --isoid df01 --no-auth -> show files from desfire light"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id (3 hex bytes, big endian)",
-                "--appisoid <isoid hex> application iso id (iso df id) (2 hex bytes, big endian).",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id (3 hex bytes, big endian)",
+                "--isoid <hex> application iso id (iso df id) (2 hex bytes, big endian)",
                 "--no-auth execute without authentication"
             ],
-            "usage": "hf mfdes lsfiles [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>] [--appisoid <isoid hex>] [--no-auth]"
+            "usage": "hf mfdes lsfiles [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [--isoid <hex>] [--no-auth]"
         },
         "hf mfdes mad": {
             "command": "hf mfdes mad",
@@ -5133,19 +5189,19 @@
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id of issuer info file, (non-standard feature!) (3 hex bytes, big endian)",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id of issuer info file, (3 hex bytes, big endian), (non-standard feature!)",
                 "--auth authenticate to get info from getapplicationids command (non-standard feature!)"
             ],
-            "usage": "hf mfdes mad [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>] [--auth]"
+            "usage": "hf mfdes mad [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [--auth]"
         },
         "hf mfdes read": {
             "command": "hf mfdes read",
@@ -5159,36 +5215,36 @@
                 "hf mfdes read --aid 123456 --fid 01 --type record --offset 000000 --length 000001 -> read one last record from record file. use default channel settings from `default` command",
                 "hf mfdes read --aid 123456 --fid 10 --type data -c iso -> read file via iso channel: app=123456, short iso id=10, offset=0.",
                 "hf mfdes read --aid 123456 --fileisoid 1000 --type data -c iso -> read file via iso channel: app=123456, iso id=1000, offset=0. select via native iso wrapper",
-                "hf mfdes read --appisoid 0102 --fileisoid 1000 --type data -c iso -> read file via iso channel: app iso id=0102, iso id=1000, offset=0. select via iso commands",
-                "hf mfdes read --appisoid 0102 --fileisoid 1100 --type record -c iso --offset 000005 --length 000001 -> get one record (number 5) from file 1100 via iso commands",
-                "hf mfdes read --appisoid 0102 --fileisoid 1100 --type record -c iso --offset 000005 --length 000000 -> get all record (from 5 to 1) from file 1100 via iso commands",
-                "hf mfdes read --appisoid df01 --fid 00 -s lrp -t aes --length 000010 -> read via lrp channel",
-                "hf mfdes read --appisoid df01 --fid 00 -s ev2 -t aes --length 000010 --isochain -> read desfire light via ev2 channel"
+                "hf mfdes read --isoid 0102 --fileisoid 1000 --type data -c iso -> read file via iso channel: app iso id=0102, iso id=1000, offset=0. select via iso commands",
+                "hf mfdes read --isoid 0102 --fileisoid 1100 --type record -c iso --offset 000005 --length 000001 -> get one record (number 5) from file 1100 via iso commands",
+                "hf mfdes read --isoid 0102 --fileisoid 1100 --type record -c iso --offset 000005 --length 000000 -> get all record (from 5 to 1) from file 1100 via iso commands",
+                "hf mfdes read --isoid df01 --fid 00 -s lrp -t aes --length 000010 -> read via lrp channel",
+                "hf mfdes read --isoid df01 --fid 00 -s ev2 -t aes --length 000010 --isochain -> read desfire light via ev2 channel"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id (3 hex bytes, big endian)",
-                "--fid <file id hex> file id (1 hex byte)",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id (3 hex bytes, big endian)",
+                "--fid <hex> file id (1 hex byte)",
                 "--no-auth execute without authentication",
-                "--type <auto/data/value/record/mac> file type auto/data(standard/backup)/value/record(linear/cyclic)/mac). auto - check file settings and then read. default: auto",
-                "-o, --offset <hex> file offset (3 hex bytes, big endian). for records - record number (0 - lastest record). default 0",
-                "-l, --length <hex> length to read (3 hex bytes, big endian -> 000000 = read all data). for records - records count (0 - all). default 0.",
-                "--appisoid <isoid hex> application iso id (iso df id) (2 hex bytes, big endian).",
-                "--fileisoid <isoid hex> file iso id (iso df id) (2 hex bytes, big endian). works only for iso read commands.",
+                "--type <auto|data|value|record|mac> file type, auto - check file settings and then read. (def: auto)",
+                "-o, --offset <hex> file offset (3 hex bytes, big endian). for records - record number (0 - lastest record). (def: 0)",
+                "-l, --length <hex> length to read (3 hex bytes, big endian -> 000000 = read all data). for records - records count (0 - all). (def: 0)",
+                "--isoid <hex> application iso id (iso df id) (2 hex bytes, big endian)",
+                "--fileisoid <hex> file iso id (iso df id) (2 hex bytes, big endian). works only for iso read commands",
                 "--isochain use iso chaining commands. switched on by default if secure channel = lrp"
             ],
-            "usage": "hf mfdes read [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>] [--fid <file id hex>] [--no-auth] [--type <auto/data/value/record/mac>] [-o <hex>] [-l <hex>] [--appisoid <isoid hex>] [--fileisoid <isoid hex>] [--isochain]"
+            "usage": "hf mfdes read [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [--fid <hex>] [--no-auth] [--type <auto|data|value|record|mac>] [-o <hex>] [-l <hex>] [--isoid <hex>] [--fileisoid <hex>] [--isochain]"
         },
         "hf mfdes selectapp": {
             "command": "hf mfdes selectapp",
@@ -5205,28 +5261,29 @@
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id of application for some parameters (3 hex bytes, big endian)",
-                "--dfname <df name str> application df name (string, max 16 chars). selects application via iso select command",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id of application for some parameters (3 hex bytes, big endian)",
+                "--dfname <str> application df name (string, max 16 chars). selects application via iso select command",
                 "--mf select mf (master file) via iso channel",
-                "--isoid <isoid hex> application iso id (iso df id) (2 hex bytes, big endian)",
-                "--fileisoid <isoid hex> select file inside application by iso id (iso df id) (2 hex bytes, big endian)."
+                "--isoid <hex> application iso id (iso df id) (2 hex bytes, big endian)",
+                "--fileisoid <hex> select file inside application by iso id (iso df id) (2 hex bytes, big endian)."
             ],
-            "usage": "hf mfdes selectapp [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>] [--dfname <df name str>] [--mf] [--isoid <isoid hex>] [--fileisoid <isoid hex>]"
+            "usage": "hf mfdes selectapp [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [--dfname <str>] [--mf] [--isoid <hex>] [--fileisoid <hex>]"
         },
         "hf mfdes setconfig": {
             "command": "hf mfdes setconfig",
             "description": "set card configuration. warning! danger zone! needs to provide card's master key and works if not blocked by config.",
             "notes": [
-                "more about options mf2dlhx0.pdf. options list:",
+                "more about options mf2dlhx0.pdf.",
+                "options list:",
                 "00h picc configuration.",
                 "02h ats update.",
                 "03h sak update",
@@ -5240,29 +5297,29 @@
                 "",
                 "hf mfdes setconfig --param 03 --data 0428 -> set sak",
                 "hf mfdes setconfig --param 02 --data 0875778102637264 -> set ats (first byte - length)",
-                "hf mfdes setconfig --appisoid df01 -t aes -s ev2 --param 05 --data 00000000020000000000 -> set lrp mode enable for desfire light",
-                "hf mfdes setconfig --appisoid df01 -t aes -s ev2 --param 0a --data 00ffffffff -> disable failed auth counters for desfire light",
-                "hf mfdes setconfig --appisoid df01 -t aes -s lrp --param 0a --data 00ffffffff -> disable failed auth counters for desfire light via lrp channel"
+                "hf mfdes setconfig --isoid df01 -t aes -s ev2 --param 05 --data 00000000020000000000 -> set lrp mode enable for desfire light",
+                "hf mfdes setconfig --isoid df01 -t aes -s ev2 --param 0a --data 00ffffffff -> disable failed auth counters for desfire light",
+                "hf mfdes setconfig --isoid df01 -t aes -s lrp --param 0a --data 00ffffffff -> disable failed auth counters for desfire light via lrp"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id of application for some parameters (3 hex bytes, big endian)",
-                "--appisoid <isoid hex> application iso id (iso df id) (2 hex bytes, big endian).",
-                "-p, --param <hex 1 byte> parameter id (hex 1 byte)",
-                "-d, --data <data hex> data for parameter (hex 1..30 bytes)"
-            ],
-            "usage": "hf mfdes setconfig [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>] [--appisoid <isoid hex>] [-p <hex 1 byte>] [-d <data hex>]"
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id of application for some parameters (3 hex bytes, big endian)",
+                "--isoid <hex> application iso id (iso df id) (2 hex bytes, big endian).",
+                "-p, --param <hex> parameter id (1 hex byte)",
+                "-d, --data <hex> data for parameter (1..30 hex bytes)"
+            ],
+            "usage": "hf mfdes setconfig [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [--isoid <hex>] [-p <hex>] [-d <hex>]"
         },
         "hf mfdes test": {
             "command": "hf mfdes test",
@@ -5282,31 +5339,31 @@
             "notes": [
                 "hf mfdes value --aid 123456 --fid 01 -> get value app=123456, file=01 with defaults from `default` command",
                 "hf mfdes value --aid 123456 --fid 01 --op credit -d 00000001 -> credit value app=123456, file=01 with defaults from `default` command",
-                "hf mfdes value -n 0 -t des -k 0000000000000000 -f none --aid 123456 --fid 01 -> get value with default factory setup",
-                "hf mfdes val --appisoid df01 --fid 03 -s lrp -t aes -n 1 --op credit --d 00000001 -m encrypt -> credit value in the lrp encrypted mode",
-                "hf mfdes val --appisoid df01 --fid 03 -s lrp -t aes -n 1 --op get -m plain -> get value in plain (nevertheless of mode) works for desfire light (look setconfiguration option 0x09)"
+                "hf mfdes value -n 0 -t des -k 0000000000000000 --kdf none --aid 123456 --fid 01 -> get value with default factory setup",
+                "hf mfdes val --isoid df01 --fid 03 -s lrp -t aes -n 1 --op credit --d 00000001 -m encrypt -> credit value in the lrp encrypted mode",
+                "hf mfdes val --isoid df01 --fid 03 -s lrp -t aes -n 1 --op get -m plain -> get value in plain (nevertheless of mode) works for desfire light (look setconfiguration option 0x09)"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id (3 hex bytes, big endian)",
-                "--appisoid <isoid hex> application iso id (iso df id) (2 hex bytes, big endian).",
-                "--fid <file id hex> file id (1 hex byte)",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id (3 hex bytes, big endian)",
+                "--isoid <hex> application iso id (iso df id) (2 hex bytes, big endian)",
+                "--fid <hex> file id (1 hex byte)",
                 "-o, --op <get/credit/limcredit/debit/clear> operation: get(default)/credit/limcredit(limited credit)/debit/clear. operation clear: get-getopt-debit to min value",
-                "-d, --data <value hex> value for operation (hex 4 bytes)",
+                "-d, --data <hex> value for operation (hex 4 bytes)",
                 "--no-auth execute without authentication"
             ],
-            "usage": "hf mfdes value [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>] [--appisoid <isoid hex>] [--fid <file id hex>] [-o <get/credit/limcredit/debit/clear>] [-d <value hex>] [--no-auth]"
+            "usage": "hf mfdes value [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [--isoid <hex>] [--fid <hex>] [-o <get/credit/limcredit/debit/clear>] [-d <hex>] [--no-auth]"
         },
         "hf mfdes write": {
             "command": "hf mfdes write",
@@ -5323,39 +5380,39 @@
                 "hf mfdes write --aid 123456 --fid 01 --type record -d 01020304 -> write data to record file",
                 "hf mfdes write --aid 123456 --fid 01 --type record -d 01020304 --updaterec 0 -> update record in the record file. record 0 - lastest record.",
                 "hf mfdes write --aid 123456 --fid 01 --type record --offset 000000 -d 11223344 -> write record to record file. use default channel settings from `default` command",
-                "hf mfdes write --appisoid 1234 --fileisoid 1000 --type data -c iso -d 01020304 -> write data to std/backup file via iso commandset",
-                "hf mfdes write --appisoid 1234 --fileisoid 2000 --type record -c iso -d 01020304 -> send record to record file via iso commandset",
+                "hf mfdes write --isoid 1234 --fileisoid 1000 --type data -c iso -d 01020304 -> write data to std/backup file via iso commandset",
+                "hf mfdes write --isoid 1234 --fileisoid 2000 --type record -c iso -d 01020304 -> send record to record file via iso commandset",
                 "hf mfdes write --aid 123456 --fid 01 -d 01020304 --readerid 010203 -> write data to file with commitreaderid command before write and committransaction after write",
-                "hf mfdes write --appisoid df01 --fid 04 -d 01020304 --trkey 00112233445566778899aabbccddeeff --readerid 5532 -t aes -s lrp -> advanced commitreaderid via lrp channel sample"
+                "hf mfdes write --isoid df01 --fid 04 -d 01020304 --trkey 00112233445566778899aabbccddeeff --readerid 5532 -t aes -s lrp -> advanced commitreaderid via lrp channel sample"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
                 "-a, --apdu show apdu requests and responses",
-                "-v, --verbose show technical data",
-                "-n, --keyno <keyno> key number",
-                "-t, --algo <des/2tdea/3tdea/aes> crypt algo: des, 2tdea, 3tdea, aes",
-                "-k, --key <key> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
-                "-f, --kdf <none/an10922/gallagher> key derivation function (kdf): none, an10922, gallagher",
-                "-i, --kdfi <kdfi> kdf input (hex 1-31 bytes)",
-                "-m, --cmode <plain/mac/encrypt> communicaton mode: plain/mac/encrypt",
-                "-c, --ccset <native/niso/iso> communicaton command set: native/niso/iso",
-                "-s, --schann <d40/ev1/ev2/lrp> secure channel: d40/ev1/ev2/lrp",
-                "--aid <app id hex> application id (3 hex bytes, big endian)",
-                "--fid <file id hex> file id (1 hex byte)",
+                "-v, --verbose verbose mode",
+                "-n, --keyno <dec> key number",
+                "-t, --algo <des|2tdea|3tdea|aes> crypt algo",
+                "-k, --key <hex> key for authenticate (hex 8(des), 16(2tdea or aes) or 24(3tdea) bytes)",
+                "--kdf <none|an10922|gallagher> key derivation function (kdf)",
+                "-i, --kdfi <hex> kdf input (1-31 hex bytes)",
+                "-m, --cmode <plain|mac|encrypt> communicaton mode",
+                "-c, --ccset <native|niso|iso> communicaton command set",
+                "--schann <d40|ev1|ev2|lrp> secure channel",
+                "--aid <hex> application id (3 hex bytes, big endian)",
+                "--fid <hex> file id (1 hex byte)",
                 "--no-auth execute without authentication",
-                "--type <auto/data/value/record/mac> file type auto/data(standard/backup)/value/record(linear/cyclic)/mac). auto - check file settings and then write. default: auto",
-                "-o, --offset <hex> file offset (3 hex bytes, big endian). for records - record number (0 - lastest record). default 0",
+                "--type <auto|data|value|record|mac> file type, auto - check file settings and then write. (def: auto)",
+                "-o, --offset <hex> file offset (3 hex bytes, big endian). for records - record number (0 - lastest record). (def: 0)",
                 "-d, --data <hex> data for write (data/record file), credit/debit(value file)",
                 "--debit use for value file debit operation instead of credit",
-                "--commit commit needs for backup file only. for the other file types and in the `auto` mode - command set it automatically.",
-                "--updaterec <record number dec> record number for update record command. updates record instead of write. lastest record - 0",
-                "--appisoid <isoid hex> application iso id (iso df id) (2 hex bytes, big endian).",
-                "--fileisoid <isoid hex> file iso id (iso df id) (2 hex bytes, big endian). works only for iso write commands.",
-                "--readerid <hex> reader id for commitreaderid command. if present - the command issued before write command.",
-                "--trkey <hex> key for decode previous reader id."
+                "--commit commit needs for backup file only. for the other file types and in the `auto` mode - command set it automatically",
+                "--updaterec <dec> record number for update record command. updates record instead of write. lastest record - 0",
+                "--isoid <hex> application iso id (iso df id) (2 hex bytes, big endian)",
+                "--fileisoid <hex> file iso id (iso df id) (2 hex bytes, big endian). works only for iso write commands",
+                "--readerid <hex> reader id for commitreaderid command. if present - the command issued before write command",
+                "--trkey <hex> key for decode previous reader id"
             ],
-            "usage": "hf mfdes write [-hav] [-n <keyno>] [-t <des/2tdea/3tdea/aes>] [-k <key>] [-f <none/an10922/gallagher>] [-i <kdfi>] [-m <plain/mac/encrypt>] [-c <native/niso/iso>] [-s <d40/ev1/ev2/lrp>] [--aid <app id hex>] [--fid <file id hex>] [--no-auth] [--type <auto/data/value/record/mac>] [-o <hex>] [-d <hex>] [--debit] [--commit] [--updaterec <record number dec>] [--appisoid <isoid hex>] [--fileisoid <isoid hex>] [--readerid <hex>] [--trkey <hex>]"
+            "usage": "hf mfdes write [-hav] [-n <dec>] [-t <des|2tdea|3tdea|aes>] [-k <hex>] [--kdf <none|an10922|gallagher>] [-i <hex>] [-m <plain|mac|encrypt>] [-c <native|niso|iso>] [--schann <d40|ev1|ev2|lrp>] [--aid <hex>] [--fid <hex>] [--no-auth] [--type <auto|data|value|record|mac>] [-o <hex>] [-d <hex>] [--debit] [--commit] [--updaterec <dec>] [--isoid <hex>] [--fileisoid <hex>] [--readerid <hex>] [--trkey <hex>]"
         },
         "hf mfp auth": {
             "command": "hf mfp auth",
@@ -5367,7 +5424,7 @@
             "offline": false,
             "options": [
                 "-h, --help this help",
-                "-v, --verbose show internal data.",
+                "-v, --verbose verbose mode",
                 "--ki <hex> key number, 2 hex bytes",
                 "--key <hex> key, 16 hex bytes"
             ],
@@ -5375,7 +5432,7 @@
         },
         "hf mfp chk": {
             "command": "hf mfp chk",
-            "description": "checks keys with mifare plus card.",
+            "description": "checks keys on mifare plus card",
             "notes": [
                 "hf mfp chk -k 000102030405060708090a0b0c0d0e0f -> check key on sector 0 as key a and b",
                 "hf mfp chk -s 2 -a -> check default key list on sector 2, key a",
@@ -5386,17 +5443,19 @@
             "offline": false,
             "options": [
                 "-h, --help this help",
-                "-a, --keya check only key a (by default check all keys).",
-                "-b, --keyb check only key b (by default check all keys).",
-                "-k, --key <key> key for checking (hex 16 bytes)",
-                "-d, --dict <file> file with keys dictionary",
+                "-a, --keya check only key a (def: check all keys)",
+                "-b, --keyb check only key b (def: check all keys)",
+                "-s, --startsec <0..255> start sector number",
+                "-e, --endsec <0..255> end sector number",
+                "-k, --key <hex> key for checking (hex 16 bytes)",
+                "-d, --dict <fn> dictionary file with keys",
                 "--pattern1b check all 1-byte combinations of key (0000...0000, 0101...0101, 0202...0202, ...)",
                 "--pattern2b check all 2-byte combinations of key (0000...0000, 0001...0001, 0002...0002, ...)",
                 "--startp2b <pattern> start key (2-byte hex) for 2-byte search (use with `--pattern2b`)",
-                "-j, --json <file> json file to save keys",
-                "-v, --verbose verbose mode."
+                "-j, --json <fn> json filename to save keys",
+                "-v, --verbose verbose mode"
             ],
-            "usage": "hf mfp chk [-habv] [-s start sector num (0..255)] [-e end sector num (0..255)] [-k <key>] [-d <file>] [--pattern1b] [--pattern2b] [--startp2b <pattern>] [-j <file>]"
+            "usage": "hf mfp chk [-habv] [-s <0..255>] [-e <0..255>] [-k <hex>] [-d <fn>] [--pattern1b] [--pattern2b] [--startp2b <pattern>] [-j <fn>]"
         },
         "hf mfp commitp": {
             "command": "hf mfp commitp",
@@ -5407,7 +5466,7 @@
             "offline": false,
             "options": [
                 "-h, --help this help",
-                "-v, --verbose show internal data."
+                "-v, --verbose verbose mode"
             ],
             "usage": "hf mfp commitp [-hv]"
         },
@@ -5433,7 +5492,7 @@
             "offline": false,
             "options": [
                 "-h, --help this help",
-                "-v, --verbose show internal data.",
+                "-v, --verbose verbose mode",
                 "-k, --key <hex> key, 16 hex bytes"
             ],
             "usage": "hf mfp initp [-hv] [-k <hex>]"
@@ -5449,13 +5508,13 @@
             "options": [
                 "-h, --help this help",
                 "-v, --verbose show technical data",
-                "--aid <aid> print all sectors with aid",
-                "-k, --key <key> key for printing sectors",
-                "-b, --keyb use key b for access printing sectors (by default: key a)",
-                "--be (optional, bigendian)",
+                "--aid <hex> print all sectors with aid",
+                "-k, --key <hex> key for printing sectors",
+                "-b, --keyb use key b for access printing sectors (def: key a)",
+                "--be (optional: bigendian)",
                 "--dch decode card holder information"
             ],
-            "usage": "hf mfp mad [-hvb] [--aid <aid>] [-k <key>] [--be] [--dch]"
+            "usage": "hf mfp mad [-hvb] [--aid <hex>] [-k <hex>] [--be] [--dch]"
         },
         "hf mfp ndefread": {
             "command": "hf mfp ndefread",
@@ -5487,14 +5546,14 @@
             "offline": false,
             "options": [
                 "-h, --help this help",
-                "-v, --verbose show internal data",
-                "-n, --count <dec> blocks count (by default 1)",
-                "-b, --keyb use key b (by default keya)",
+                "-v, --verbose verbose mode",
+                "-n, --count <dec> blocks count (def: 1)",
+                "-b, --keyb use key b (def: keya)",
                 "-p, --plain plain communication mode between reader and card",
-                "--blk <dec> block number (0..255)",
+                "--blk <0..255> block number",
                 "--key <hex> key, 16 hex bytes"
             ],
-            "usage": "hf mfp rdbl [-hvbp] [-n <dec>] --blk <dec> [--key <hex>]"
+            "usage": "hf mfp rdbl [-hvbp] [-n <dec>] --blk <0..255> [--key <hex>]"
         },
         "hf mfp rdsc": {
             "command": "hf mfp rdsc",
@@ -5506,13 +5565,13 @@
             "offline": false,
             "options": [
                 "-h, --help this help",
-                "-v, --verbose show internal data.",
-                "-b, --keyb use key b (by default keya).",
-                "-p, --plain plain communication mode between reader and card.",
-                "-s, --sn <dec> sector number (0..255)",
+                "-v, --verbose verbose mode",
+                "-b, --keyb use key b (def: keya)",
+                "-p, --plain plain communication mode between reader and card",
+                "-s, --sn <0..255> sector number",
                 "-k, --key <hex> key, 16 hex bytes"
             ],
-            "usage": "hf mfp rdsc [-hvbp] -s <dec> [-k <hex>]"
+            "usage": "hf mfp rdsc [-hvbp] -s <0..255> [-k <hex>]"
         },
         "hf mfp wrbl": {
             "command": "hf mfp wrbl",
@@ -5524,13 +5583,13 @@
             "offline": false,
             "options": [
                 "-h, --help this help",
-                "-v, --verbose show internal data.",
-                "-b, --keyb use key b (by default keya).",
-                "--blk <dec> block number (0..255)",
+                "-v, --verbose verbose mode",
+                "-b, --keyb use key b (def: keya)",
+                "--blk <0..255> block number",
                 "-d, --data <hex> data, 16 hex bytes",
                 "-k, --key <hex> key, 16 hex bytes"
             ],
-            "usage": "hf mfp wrbl [-hvb] --blk <dec> -d <hex> [-k <hex>]"
+            "usage": "hf mfp wrbl [-hvb] --blk <0..255> -d <hex> [-k <hex>]"
         },
         "hf mfp wrp": {
             "command": "hf mfp wrp",
@@ -5542,7 +5601,7 @@
             "offline": false,
             "options": [
                 "-h, --help this help",
-                "-v, --verbose show internal data.",
+                "-v, --verbose verbose mode",
                 "--ki <hex> key number, 2 hex bytes",
                 "--key <hex> key, 16 hex bytes"
             ],
@@ -5558,17 +5617,17 @@
             "offline": false,
             "options": [
                 "-h, --help this help",
-                "--key <hex> key for authentication (ul-c 16 bytes)",
+                "--key <hex> authentication key (ul-c 16 hex bytes)",
                 "-l swap entered key's endianness",
-                "-k keep field on (only if a password is provided too)"
+                "-k keep field on (only if a password is provided)"
             ],
             "usage": "hf mfu cauth [-hlk] [--key <hex>]"
         },
         "hf mfu dump": {
             "command": "hf mfu dump",
-            "description": "reads all pages from ultralight, ultralight-c, ultralight ev1 ntag 203, ntag 210, ntag 212, ntag 213, ntag 215, ntag 216 and saves data into binary/json files. it autodetects card type.",
+            "description": "dump mifare ultralight/ntag tag to binary/json files. it autodetects card type.supports: ultralight, ultralight-c, ultralight ev1 ntag 203, ntag 210, ntag 212, ntag 213, ntag 215, ntag 216",
             "notes": [
-                "hf mfu dump -f myfile -> dump whole tag, save to `myfile.bin`",
+                "hf mfu dump -f myfile",
                 "hf mfu dump -k aabbccdd -> dump whole tag using pwd aabbccdd",
                 "hf mfu dump -p 10 -> start at page 10 and dump rest of blocks",
                 "hf mfu dump -p 10 -q 2 -> start at page 10 and dump two blocks",
@@ -5587,19 +5646,18 @@
         },
         "hf mfu eload": {
             "command": "hf mfu eload",
-            "description": "load emulator memory with data from `filename.eml` dump file see `script run data_mfu_bin2eml` to convert the .bin to .eml",
+            "description": "load emulator memory with data from (bin/eml/json) dump file",
             "notes": [
-                "hf mfu eload --ul -f hf-mfu-04010203040506.eml",
-                "hf mfu eload --ul -f hf-mfu-04010203040506.eml -q 57 -> load 57 blocks from myfile"
+                "hf mfu eload -f hf-mfu-04010203040506.bin",
+                "hf mfu eload -f hf-mfu-04010203040506.bin -q 57 -> load 57 blocks from myfile"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
                 "-f, --file <fn> filename of dump",
-                "--ul mifare ultralight family",
                 "-q, --qty <dec> number of blocks to load from eml file"
             ],
-            "usage": "hf mfu eload [-h] -f <fn> --ul [-q <dec>]"
+            "usage": "hf mfu eload [-h] -f <fn> [-q <dec>]"
         },
         "hf mfu eview": {
             "command": "hf mfu eview",
@@ -5615,7 +5673,7 @@
         },
         "hf mfu help": {
             "command": "hf mfu help",
-            "description": "help this help keygen generate 3des mifare diversified keys pwdgen generate pwd from known algos --------------------------------------------------------------------------------------- hf mfu keygen available offline: yes set the 3des key on mifare ultralight-c tag.",
+            "description": "help this help keygen generate 3des mifare diversified keys pwdgen generate pwd from known algos view display content from tag dump file --------------------------------------------------------------------------------------- hf mfu keygen available offline: yes set the 3des key on mifare ultralight-c tag.",
             "notes": [
                 "hf mfu keygen -r",
                 "hf mfu keygen --uid 11223344556677"
@@ -5639,7 +5697,7 @@
             "offline": false,
             "options": [
                 "-h, --help this help",
-                "-k, --key <hex> key for authentication (ul-c 16 bytes, ev1/ntag 4 bytes)",
+                "-k, --key <hex> authentication key (ul-c 16 bytes, ev1/ntag 4 bytes)",
                 "-l swap entered key's endianness"
             ],
             "usage": "hf mfu info [-hl] [-k <hex>]"
@@ -5694,7 +5752,7 @@
             "offline": true,
             "options": [
                 "-h, --help this help",
-                "-u, --uid <hex> uid (7 bytes)",
+                "-u, --uid <hex> uid (7 hex bytes)",
                 "-r read uid from tag",
                 "-t selftest"
             ],
@@ -5711,30 +5769,30 @@
             "offline": false,
             "options": [
                 "-h, --help this help",
-                "-k, --key <hex> key for authentication (ul-c 16 bytes, ev1/ntag 4 bytes)",
+                "-k, --key <hex> authentication key (ul-c 16 bytes, ev1/ntag 4 bytes)",
                 "-l swap entered key's endianness",
-                "-b, --block <dec> block number to read",
+                "-b, --block <dec> nlock number to read",
                 "--force force operation even if address is out of range"
             ],
             "usage": "hf mfu rdbl [-hl] [-k <hex>] -b <dec> [--force]"
         },
         "hf mfu restore": {
             "command": "hf mfu restore",
-            "description": "restore dumpfile onto card.",
+            "description": "restore mifare ultralight/ntag dump file to tag.",
             "notes": [
-                "hf mfu restore -f myfile -s -> user specified filename and special write",
-                "hf mfu restore -f myfile -k aabbccdd -s -> user specified filename, special write and use key",
-                "hf mfu restore -f myfile -k aabbccdd -ser -> user specified filename, special write, use key, ..."
+                "hf mfu restore -f myfile -s -> special write",
+                "hf mfu restore -f myfile -k aabbccdd -s -> special write, use key",
+                "hf mfu restore -f myfile -k aabbccdd -ser -> special write, use key, write dump pwd, ..."
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
-                "-f, --file <fn> specify a filename to restore",
+                "-f, --file <fn> specify dump filename (bin/eml/json)",
                 "-k, --key <hex> key for authentication (ul-c 16 bytes, ev1/ntag 4 bytes)",
                 "-l swap entered key's endianness",
                 "-s enable special write uid -magic tag only-",
                 "-e enable special write version/signature -magic ntag 21* only-",
-                "-r use the password found in dumpfile to configure tag. requires '-e' parameter to work",
+                "-r use password found in dumpfile to configure tag. requires '-e' parameter to work",
                 "-v, --verbose verbose"
             ],
             "usage": "hf mfu restore [-hlserv] -f <fn> [-k <hex>]"
@@ -5748,7 +5806,7 @@
             "offline": false,
             "options": [
                 "-h, --help this help",
-                "-k, --key <hex> new key (16 bytes)"
+                "-k, --key <hex> new key (16 hex bytes)"
             ],
             "usage": "hf mfu setpwd [-h] [-k <hex>]"
         },
@@ -5761,7 +5819,7 @@
             "offline": false,
             "options": [
                 "-h, --help this help",
-                "-u, --uid <hex> new uid (7 bytes)"
+                "-u, --uid <hex> new uid (7 hex bytes)"
             ],
             "usage": "hf mfu setuid [-h] [-u <hex>]"
         },
@@ -5775,12 +5833,26 @@
             "offline": false,
             "options": [
                 "-h, --help this help",
-                "-t, --type <1-10> simulation type to use",
-                "-u, --uid <hex> 4, 7 or 10 byte uid",
-                "-n, --num <dec> exit simulation after <numreads> blocks have been read by reader. 0 = infinite",
+                "-t, --type <1..10> simulation type to use",
+                "-u, --uid <hex> <4|7|10> hex bytes uid",
+                "-n, --num <dec> exit simulation after <numreads> blocks. 0 = infinite",
+                "-v, --verbose verbose output"
+            ],
+            "usage": "hf mfu sim [-hv] -t <1..10> [-u <hex>] [-n <dec>]"
+        },
+        "hf mfu view": {
+            "command": "hf mfu view",
+            "description": "print a mifare ultralight/ntag dump file (bin/eml/json)",
+            "notes": [
+                "hf mfu view -f hf-mfu-01020304-dump.bin"
+            ],
+            "offline": true,
+            "options": [
+                "-h, --help this help",
+                "-f, --file <fn> filename of dump",
                 "-v, --verbose verbose output"
             ],
-            "usage": "hf mfu sim [-hv] -t <1-10> [-u <hex>] [-n <dec>]"
+            "usage": "hf mfu view [-hv] -f <fn>"
         },
         "hf mfu wrbl": {
             "command": "hf mfu wrbl",
@@ -5793,7 +5865,7 @@
             "offline": false,
             "options": [
                 "-h, --help this help",
-                "-k, --key <hex> key for authentication (ul-c 16 bytes, ev1/ntag 4 bytes)",
+                "-k, --key <hex> authentication key (ul-c 16 bytes, ev1/ntag 4 bytes)",
                 "-l swap entered key's endianness",
                 "-b, --block <dec> block number to write",
                 "-d, --data <hex> block data (4 or 16 hex bytes, 16 hex bytes will do a compatibility write)",
@@ -10343,8 +10415,8 @@
         }
     },
     "metadata": {
-        "commands_extracted": 603,
+        "commands_extracted": 606,
         "extracted_by": "PM3Help2JSON v1.00",
-        "extracted_on": "2022-02-05T11:30:03"
+        "extracted_on": "2022-02-12T16:44:39"
     }
 }
\ No newline at end of file
diff --git a/doc/commands.md b/doc/commands.md
index 0890285ad..b695ba5b5 100644
--- a/doc/commands.md
+++ b/doc/commands.md
@@ -137,17 +137,17 @@ Check column "offline" for their availability.
 |command                  |offline |description
 |-------                  |------- |-----------
 |`emv help               `|Y       |`This help`
-|`emv exec               `|N       |`Executes EMV contactless transaction.`
-|`emv pse                `|N       |`Execute PPSE. It selects 2PAY.SYS.DDF01 or 1PAY.SYS.DDF01 directory.`
-|`emv search             `|N       |`Try to select all applets from applets list and print installed applets.`
-|`emv select             `|N       |`Select applet.`
-|`emv gpo                `|N       |`Execute GetProcessingOptions.`
-|`emv readrec            `|N       |`Read files from card.`
-|`emv genac              `|N       |`Generate ApplicationCryptogram.`
-|`emv challenge          `|N       |`Generate challenge.`
-|`emv intauth            `|N       |`Internal authentication.`
-|`emv scan               `|N       |`Scan EMV card and save it contents to json file for emulator.`
-|`emv test               `|Y       |`Crypto logic test.`
+|`emv exec               `|N       |`Executes EMV contactless transaction`
+|`emv pse                `|N       |`Execute PPSE. It selects 2PAY.SYS.DDF01 or 1PAY.SYS.DDF01 directory`
+|`emv search             `|N       |`Try to select all applets from applets list and print installed applets`
+|`emv select             `|N       |`Select applet`
+|`emv gpo                `|N       |`Execute GetProcessingOptions`
+|`emv readrec            `|N       |`Read files from card`
+|`emv genac              `|N       |`Generate ApplicationCryptogram`
+|`emv challenge          `|N       |`Generate challenge`
+|`emv intauth            `|N       |`Internal authentication`
+|`emv scan               `|N       |`Scan EMV card and save it contents to json file for emulator`
+|`emv test               `|Y       |`Crypto logic test`
 |`emv list               `|Y       |`List ISO7816 history`
 |`emv roca               `|N       |`Extract public keys and run ROCA test`
 
@@ -252,6 +252,8 @@ Check column "offline" for their availability.
 |`hf cipurse formatall   `|N       |`Erase all the data from chip`
 |`hf cipurse create      `|N       |`Create file, application, key via DGI record`
 |`hf cipurse delete      `|N       |`Delete file`
+|`hf cipurse updkey      `|N       |`Update key`
+|`hf cipurse updakey     `|N       |`Update key attributes`
 |`hf cipurse default     `|N       |`Set default key and file id for all the other commands`
 |`hf cipurse test        `|Y       |`Tests`
 
@@ -519,8 +521,9 @@ Check column "offline" for their availability.
 |`hf mfu ndefread        `|N       |`Prints NDEF records from card`
 |`hf mfu rdbl            `|N       |`Read block`
 |`hf mfu restore         `|N       |`Restore a dump onto a MFU MAGIC tag`
+|`hf mfu view            `|Y       |`Display content from tag dump file`
 |`hf mfu wrbl            `|N       |`Write block`
-|`hf mfu eload           `|N       |`load Ultralight .eml dump file into emulator memory`
+|`hf mfu eload           `|N       |`Load Ultralight .eml dump file into emulator memory`
 |`hf mfu eview           `|N       |`View emulator memory`
 |`hf mfu sim             `|N       |`Simulate MIFARE Ultralight from emulator memory`
 |`hf mfu setpwd          `|N       |`Set 3DES key - Ultralight-C`
