commit 8ac5d5cba1dc632acca0d580cb5a2123a8e153b3
Author: pwpiwi <micki.held@gmx.de>
Date:   Fri Jan 29 08:26:33 2021 +0100

    Fix gcc10 compiler warnings
    - fix attribute format for MINGW in tinycbor/cbor.h
    - add pragma to prevent "unaligned pointer" warning in cmddata.c. We know what we are doing there.
    - whitespace fixes

diff --git a/client/cmddata.c b/client/cmddata.c
index 8f93ba1..f5416fe 100644
--- a/client/cmddata.c
+++ b/client/cmddata.c
@@ -37,7 +37,7 @@ static int CmdHelp(const char *Cmd);
 //by marshmellow
 void setDemodBuf(uint8_t *buff, size_t size, size_t startIdx)
 {
-	if (buff == NULL) 
+	if (buff == NULL)
 		return;
 
 	if ( size > MAX_DEMOD_BUF_LEN - startIdx)
@@ -102,7 +102,7 @@ int usage_data_printdemodbuf(){
 		PrintAndLog("       x          output in hex (omit for binary output)");
 		PrintAndLog("       o <offset> enter offset in # of bits");
 		PrintAndLog("       l <length> enter length to print in # of bits or hex characters respectively");
-		return 0;	
+		return 0;
 }
 
 //by marshmellow
@@ -162,7 +162,7 @@ int CmdPrintDemodBuff(const char *Cmd)
 	}
 	//Validations
 	if(errors) return usage_data_printdemodbuf();
-	length = (length > (DemodBufferLen-offset)) ? DemodBufferLen-offset : length; 
+	length = (length > (DemodBufferLen-offset)) ? DemodBufferLen-offset : length;
 	int numBits = (length) & 0x00FFC; //make sure we don't exceed our string
 
 	if (hexMode){
@@ -170,7 +170,7 @@ int CmdPrintDemodBuff(const char *Cmd)
 		numBits = (numBits > sizeof(hex)) ? sizeof(hex) : numBits;
 		numBits = binarraytohex(hex, buf, numBits);
 		if (numBits==0) return 0;
-		PrintAndLog("DemodBuffer: %s",hex);		
+		PrintAndLog("DemodBuffer: %s",hex);
 	} else {
 		PrintAndLog("DemodBuffer:\n%s", sprint_bin_break(DemodBuffer+offset,numBits,16));
 	}
@@ -199,7 +199,7 @@ int CmdGetBitStream(const char *Cmd)
 //   (amp may not be needed anymore)
 //verbose will print results and demoding messages
 //emSearch will auto search for EM410x format in bitstream
-//askType switches decode: ask/raw = 0, ask/manchester = 1 
+//askType switches decode: ask/raw = 0, ask/manchester = 1
 int ASKDemod_ext(const char *Cmd, bool verbose, bool emSearch, uint8_t askType, bool *stCheck) {
 	int invert=0;
 	int clk=0;
@@ -225,7 +225,7 @@ int ASKDemod_ext(const char *Cmd, bool verbose, bool emSearch, uint8_t askType,
 	int foundclk = 0;
 	//amp before ST check
 	if (amp == 'a' || amp == 'A') {
-		askAmp(BitStream, BitLen); 
+		askAmp(BitStream, BitLen);
 	}
 	bool st = false;
 	size_t ststart = 0, stend = 0;
@@ -263,7 +263,7 @@ int ASKDemod_ext(const char *Cmd, bool verbose, bool emSearch, uint8_t askType,
 		else PrintAndLog("ASK/Raw - Clock: %d - Decoded bitstream:",clk);
 		// Now output the bitstream to the scrollback by line of 16 bits
 		printDemodBuff();
-		
+
 	}
 	uint64_t lo = 0;
 	uint32_t hi = 0;
@@ -301,7 +301,7 @@ int Cmdaskmandemod(const char *Cmd)
 		return 0;
 	}
 	bool st = true;
-	if (Cmd[0]=='s') 
+	if (Cmd[0]=='s')
 		return ASKDemod_ext(Cmd++, true, false, 1, &st);
 	else if (Cmd[1] == 's')
 		return ASKDemod_ext(Cmd+=2, true, false, 1, &st);
@@ -324,8 +324,8 @@ int Cmdmandecoderaw(const char *Cmd)
 		PrintAndLog("Usage:  data manrawdecode [invert] [maxErr]");
 		PrintAndLog("     Takes 10 and 01 and converts to 0 and 1 respectively");
 		PrintAndLog("     --must have binary sequence in demodbuffer (run data askrawdemod first)");
-		PrintAndLog("  [invert]  invert output");		
-		PrintAndLog("  [maxErr]  set number of errors allowed (default = 20)");		
+		PrintAndLog("  [invert]  invert output");
+		PrintAndLog("  [maxErr]  set number of errors allowed (default = 20)");
 		PrintAndLog("");
 		PrintAndLog("    sample: data manrawdecode   = decode manchester bitstream from the demodbuffer");
 		return 0;
@@ -367,13 +367,13 @@ int Cmdmandecoderaw(const char *Cmd)
 	return 1;
 }
 
-/** 
+/**
  * @author marshmellow
  * biphase decode
  * decdoes  01 or 10 to 0 and 11 or 00 to 1
  * param offset adjust start position
  * param invert invert output
- * param maxErr maximum tolerated errors 
+ * param maxErr maximum tolerated errors
  */
 int CmdBiphaseDecodeRaw(const char *Cmd)
 {
@@ -418,7 +418,7 @@ int CmdBiphaseDecodeRaw(const char *Cmd)
 
 	PrintAndLog("Biphase Decoded using offset: %d - # invert:%d - data:",offset,invert);
 	PrintAndLog("%s", sprint_bin_break(BitStream, size, 16));
-	
+
 	if (offset) setDemodBuf(DemodBuffer,DemodBufferLen-offset, offset);  //remove first bit from raw demod
 	setClockGrid(g_DemodClock, g_DemodStartIdx + g_DemodClock*offset/2);
 	return 1;
@@ -432,14 +432,14 @@ int ASKbiphaseDemod(const char *Cmd, bool verbose)
 	int offset=0, clk=0, invert=0, maxErr=100;
 	sscanf(Cmd, "%i %i %i %i", &offset, &clk, &invert, &maxErr);
 
-	uint8_t BitStream[MAX_GRAPH_TRACE_LEN];	  
+	uint8_t BitStream[MAX_GRAPH_TRACE_LEN];
 	size_t size = getFromGraphBuf(BitStream);
 	int startIdx = 0;
 	//invert here inverts the ask raw demoded bits which has no effect on the demod, but we need the pointer
-	int errCnt = askdemod_ext(BitStream, &size, &clk, &invert, maxErr, 0, 0, &startIdx);  
-	if ( errCnt < 0 || errCnt > maxErr ) {   
-		if (g_debugMode) PrintAndLog("DEBUG: no data or error found %d, clock: %d", errCnt, clk);  
-			return 0;  
+	int errCnt = askdemod_ext(BitStream, &size, &clk, &invert, maxErr, 0, 0, &startIdx);
+	if ( errCnt < 0 || errCnt > maxErr ) {
+		if (g_debugMode) PrintAndLog("DEBUG: no data or error found %d, clock: %d", errCnt, clk);
+			return 0;
 	}
 
 	//attempt to Biphase decode BitStream
@@ -553,7 +553,7 @@ int AutoCorrelate(const int *in, int *out, size_t len, int window, bool SaveGrph
 	if (SaveGrph) {
 		//GraphTraceLen = GraphTraceLen - window;
 		memcpy(out, CorrelBuffer, len * sizeof(int));
-		RepaintGraphWindow();  
+		RepaintGraphWindow();
 	}
 	return Correlation;
 }
@@ -572,7 +572,7 @@ int usage_data_autocorr(void)
 int CmdAutoCorr(const char *Cmd)
 {
 	char cmdp = param_getchar(Cmd, 0);
-	if (cmdp == 'h' || cmdp == 'H') 
+	if (cmdp == 'h' || cmdp == 'H')
 		return usage_data_autocorr();
 	int window = 4000; //set default
 	char grph=0;
@@ -677,9 +677,9 @@ int CmdGraphShiftZero(const char *Cmd)
 	int shiftedVal=0;
 	for(int i = 0; i<GraphTraceLen; i++){
 		shiftedVal=GraphBuffer[i]+shift;
-		if (shiftedVal>127) 
+		if (shiftedVal>127)
 			shiftedVal=127;
-		else if (shiftedVal<-127) 
+		else if (shiftedVal<-127)
 			shiftedVal=-127;
 		GraphBuffer[i]= shiftedVal;
 	}
@@ -701,13 +701,13 @@ int AskEdgeDetect(const int *in, int *out, int len, int threshold) {
 
 //by marshmellow
 //use large jumps in read samples to identify edges of waves and then amplify that wave to max
-//similar to dirtheshold, threshold commands 
+//similar to dirtheshold, threshold commands
 //takes a threshold length which is the measured length between two samples then determines an edge
 int CmdAskEdgeDetect(const char *Cmd)
 {
 	int thresLen = 25;
 	int ans = 0;
-	sscanf(Cmd, "%i", &thresLen); 
+	sscanf(Cmd, "%i", &thresLen);
 
 	ans = AskEdgeDetect(GraphBuffer, GraphBuffer, GraphTraceLen, thresLen);
 	RepaintGraphWindow();
@@ -815,7 +815,7 @@ int FSKrawDemod(const char *Cmd, bool verbose)
 		setDemodBuf(BitStream,size,0);
 		setClockGrid(rfLen, startIdx);
 
-    // Now output the bitstream to the scrollback by line of 16 bits
+	// Now output the bitstream to the scrollback by line of 16 bits
 		if (verbose || g_debugMode) {
 			PrintAndLog("\nUsing Clock:%u, invert:%u, fchigh:%u, fclow:%u", (unsigned int)rfLen, (unsigned int)invert, (unsigned int)fchigh, (unsigned int)fclow);
 			PrintAndLog("%s decoded bitstream:",GetFSKType(fchigh,fclow,invert));
@@ -845,7 +845,7 @@ int CmdFSKrawdemod(const char *Cmd)
 		PrintAndLog("");
 		PrintAndLog("    sample: data rawdemod fs           = demod an fsk tag from GraphBuffer using autodetect");
 		PrintAndLog("          : data rawdemod fs 32        = demod an fsk tag from GraphBuffer using a clock of RF/32, autodetect fc");
-		PrintAndLog("          : data rawdemod fs 1         = demod an fsk tag from GraphBuffer using autodetect, invert output");   
+		PrintAndLog("          : data rawdemod fs 1         = demod an fsk tag from GraphBuffer using autodetect, invert output");
 		PrintAndLog("          : data rawdemod fs 32 1      = demod an fsk tag from GraphBuffer using a clock of RF/32, invert output, autodetect fc");
 		PrintAndLog("          : data rawdemod fs 64 0 8 5  = demod an fsk1 RF/64 tag from GraphBuffer");
 		PrintAndLog("          : data rawdemod fs 50 0 10 8 = demod an fsk2 RF/50 tag from GraphBuffer");
@@ -880,7 +880,7 @@ int PSKDemod(const char *Cmd, bool verbose)
 	if (errCnt > maxErr){
 		if (g_debugMode || verbose) PrintAndLog("Too many errors found, clk: %d, invert: %d, numbits: %d, errCnt: %d",clk,invert,BitLen,errCnt);
 		return 0;
-	} 
+	}
 	if (errCnt<0|| BitLen<16){  //throw away static - allow 1 and -1 (in case of threshold command first)
 		if (g_debugMode || verbose) PrintAndLog("no data found, clk: %d, invert: %d, numbits: %d, errCnt: %d",clk,invert,BitLen,errCnt);
 		return 0;
@@ -925,7 +925,7 @@ int NRZrawDemod(const char *Cmd, bool verbose)
 	if (errCnt > maxErr){
 		if (g_debugMode) PrintAndLog("Too many errors found, clk: %d, invert: %d, numbits: %d, errCnt: %d",clk,invert,BitLen,errCnt);
 		return 0;
-	} 
+	}
 	if (errCnt<0 || BitLen<16){  //throw away static - allow 1 and -1 (in case of threshold command first)
 		if (g_debugMode) PrintAndLog("no data found, clk: %d, invert: %d, numbits: %d, errCnt: %d",clk,invert,BitLen,errCnt);
 		return 0;
@@ -942,7 +942,7 @@ int NRZrawDemod(const char *Cmd, bool verbose)
 		// Now output the bitstream to the scrollback by line of 16 bits
 		printDemodBuff();
 	}
-	return 1; 
+	return 1;
 }
 
 int CmdNRZrawDemod(const char *Cmd)
@@ -988,10 +988,10 @@ int CmdPSK1rawDemod(const char *Cmd)
 	ans = PSKDemod(Cmd, true);
 	//output
 	if (!ans){
-		if (g_debugMode) PrintAndLog("Error demoding: %d",ans); 
+		if (g_debugMode) PrintAndLog("Error demoding: %d",ans);
 		return 0;
 	}
- 
+
 	PrintAndLog("PSK1 demoded bitstream:");
 	// Now output the bitstream to the scrollback by line of 16 bits
 	printDemodBuff();
@@ -1019,13 +1019,13 @@ int CmdPSK2rawDemod(const char *Cmd)
 	}
 	ans=PSKDemod(Cmd, true);
 	if (!ans){
-		if (g_debugMode) PrintAndLog("Error demoding: %d",ans);  
+		if (g_debugMode) PrintAndLog("Error demoding: %d",ans);
 		return 0;
-	} 
+	}
 	psk1TOpsk2(DemodBuffer, DemodBufferLen);
 	PrintAndLog("PSK2 demoded bitstream:");
 	// Now output the bitstream to the scrollback by line of 16 bits
-	printDemodBuff();  
+	printDemodBuff();
 	return 1;
 }
 
@@ -1036,10 +1036,10 @@ int CmdRawDemod(const char *Cmd)
 
 	if (strlen(Cmd) > 35 || cmdp == 'h' || cmdp == 'H' || strlen(Cmd)<2) {
 		PrintAndLog("Usage:  data rawdemod [modulation] <help>|<options>");
-		PrintAndLog("   [modulation] as 2 char, 'ab' for ask/biphase, 'am' for ask/manchester, 'ar' for ask/raw, 'fs' for fsk, ...");		
+		PrintAndLog("   [modulation] as 2 char, 'ab' for ask/biphase, 'am' for ask/manchester, 'ar' for ask/raw, 'fs' for fsk, ...");
 		PrintAndLog("         'nr' for nrz/direct, 'p1' for psk1, 'p2' for psk2");
-		PrintAndLog("   <help> as 'h', prints the help for the specific modulation");	
-		PrintAndLog("   <options> see specific modulation help for optional parameters");				
+		PrintAndLog("   <help> as 'h', prints the help for the specific modulation");
+		PrintAndLog("   <options> see specific modulation help for optional parameters");
 		PrintAndLog("");
 		PrintAndLog("    sample: data rawdemod fs h         = print help specific to fsk demod");
 		PrintAndLog("          : data rawdemod fs           = demod GraphBuffer using: fsk - autodetect");
@@ -1067,7 +1067,7 @@ int CmdRawDemod(const char *Cmd)
 		ans = CmdPSK1rawDemod(Cmd+2);
 	} else if(cmdp == 'p' && cmdp2 == '2'){
 		ans = CmdPSK2rawDemod(Cmd+2);
-	} else { 
+	} else {
 		PrintAndLog("unknown modulation entered - see help ('h') for parameter structure");
 	}
 	return ans;
@@ -1206,13 +1206,16 @@ int getSamples(int n, bool silent)
 	uint8_t bits_per_sample = 8;
 
 	//Old devices without this feature would send 0 at arg[0]
-	if(response.arg[0] > 0)
-	{
+	if(response.arg[0] > 0) {
+		#pragma GCC diagnostic push
+		#pragma GCC diagnostic ignored "-Waddress-of-packed-member"
 		sample_config *sc = (sample_config *) response.d.asBytes;
+		#pragma GCC diagnostic pop
 		if (!silent) PrintAndLog("Samples @ %d bits/smpl, decimation 1:%d ", sc->bits_per_sample
 		    , sc->decimation);
 		bits_per_sample = sc->bits_per_sample;
 	}
+
 	if(bits_per_sample < 8)
 	{
 		if (!silent) PrintAndLog("Unpacking...");
@@ -1332,7 +1335,7 @@ int CmdLoad(const char *Cmd)
 	len = strlen(Cmd);
 	if (len > FILE_PATH_SIZE) len = FILE_PATH_SIZE;
 	memcpy(filename, Cmd, len);
-	
+
 	FILE *f = fopen(filename, "r");
 	if (!f) {
 		 PrintAndLog("couldn't open '%s'", filename);
@@ -1428,7 +1431,7 @@ int CmdSave(const char *Cmd)
 	len = strlen(Cmd);
 	if (len > FILE_PATH_SIZE) len = FILE_PATH_SIZE;
 	memcpy(filename, Cmd, len);
-	 
+
 
 	FILE *f = fopen(filename, "w");
 	if(!f) {
@@ -1640,7 +1643,7 @@ void GetHiLoTone(int *LowTone, int *HighTone, int clk, int LowToneFC, int HighTo
 	//int HighToneMod = clk mod HighToneFC;
 	int LeftHalfFCCnt = (LowToneFC % 2) + (LowToneFC/2); //truncate
 	int FCs_per_clk = clk/LowToneFC;
-	
+
 	// need to correctly split up the clock to field clocks.
 	// First attempt uses modifiers on each end to make up for when FCs don't evenly divide into Clk
 
@@ -1694,7 +1697,7 @@ void GetHiLoTone(int *LowTone, int *HighTone, int clk, int LowToneFC, int HighTo
 	}
 }
 
-//old CmdFSKdemod adapted by marshmellow 
+//old CmdFSKdemod adapted by marshmellow
 //converts FSK to clear NRZ style wave.  (or demodulates)
 int FSKToNRZ(int *data, int *dataLen, int clk, int LowToneFC, int HighToneFC) {
 	uint8_t ans=0;
@@ -1715,7 +1718,7 @@ int FSKToNRZ(int *data, int *dataLen, int clk, int LowToneFC, int HighToneFC) {
 	int LowTone[clk];
 	int HighTone[clk];
 	GetHiLoTone(LowTone, HighTone, clk, LowToneFC, HighToneFC);
-	
+
 	int i, j;
 
 	// loop through ([all samples] - clk)
@@ -1735,7 +1738,7 @@ int FSKToNRZ(int *data, int *dataLen, int clk, int LowToneFC, int HighToneFC) {
 	}
 
 	// now we have the abs( [average sample value per clk] * 100 ) for each tone
-	//   loop through again [all samples] - clk - 16  
+	//   loop through again [all samples] - clk - 16
 	//                  note why 16???  is 16 the largest FC? changed to LowToneFC as that should be the > fc
 	for(i = 0; i < *dataLen - clk - LowToneFC; ++i) {
 		int lowTot = 0, highTot = 0;
@@ -1748,8 +1751,8 @@ int FSKToNRZ(int *data, int *dataLen, int clk, int LowToneFC, int HighToneFC) {
 		  highTot += (data[i + j] >> 16);
 		}
 
-		// subtract the sum of lowTone averages by the sum of highTone averages as it 
-		//   and write back the new graph value 
+		// subtract the sum of lowTone averages by the sum of highTone averages as it
+		//   and write back the new graph value
 		data[i] = lowTot - highTot;
 	}
 	// update dataLen to what we put back to the data sample buffer
@@ -1764,11 +1767,11 @@ int usage_data_fsktonrz() {
 		PrintAndLog("       c <clock>    enter the a clock (omit to autodetect)");
 		PrintAndLog("       l <fc_low>   enter a field clock (omit to autodetect)");
 		PrintAndLog("       f <fc_high>  enter a field clock (omit to autodetect)");
-		return 0;	
+		return 0;
 }
 
 int CmdFSKToNRZ(const char *Cmd) {
-	// take clk, fc_low, fc_high 
+	// take clk, fc_low, fc_high
 	//   blank = auto;
 	bool errors = false;
 	int clk = 0;
@@ -1841,7 +1844,7 @@ static command_t CommandTable[] =
 	{"norm",            CmdNorm,            1, "Normalize max/min to +/-128"},
 	{"plot",            CmdPlot,            1, "Show graph window (hit 'h' in window for keystroke help)"},
 	{"printdemodbuffer",CmdPrintDemodBuff,  1, "[x] [o] <offset> [l] <length> -- print the data in the DemodBuffer - 'x' for hex output"},
-	{"rawdemod",        CmdRawDemod,        1, "[modulation] ... <options> -see help (h option) -- Demodulate the data in the GraphBuffer and output binary"},  
+	{"rawdemod",        CmdRawDemod,        1, "[modulation] ... <options> -see help (h option) -- Demodulate the data in the GraphBuffer and output binary"},
 	{"samples",         CmdSamples,         0, "[512 - 40000] -- Get raw samples for graph window (GraphBuffer)"},
 	{"save",            CmdSave,            1, "<filename> -- Save trace (from graph window)"},
 	{"setgraphmarkers", CmdSetGraphMarkers, 1, "[orange_marker] [blue_marker] (in graph window)"},
diff --git a/client/tinycbor/cbor.h b/client/tinycbor/cbor.h
index fd145be..9ae93ea 100644
--- a/client/tinycbor/cbor.h
+++ b/client/tinycbor/cbor.h
@@ -581,7 +581,11 @@ enum CborPrettyFlags {
 
 typedef CborError (*CborStreamFunction)(void *token, const char *fmt, ...)
 #ifdef __GNUC__
-    __attribute__((__format__(printf, 2, 3)))
+	#if defined(__MINGW32__) || defined(__MINGW64__)
+		__attribute__((__format__(__MINGW_PRINTF_FORMAT, 2, 3)))
+	#else
+		__attribute__((__format__(printf, 2, 3)))
+	#endif
 #endif
 ;
 
