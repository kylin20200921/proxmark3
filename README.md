commit 7efd02b6e0cec3c8954d67495bdf42dc8212aed0
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Jan 6 22:08:28 2022 +0100

    cppcheck fixes

diff --git a/client/src/cmdhfmfu.c b/client/src/cmdhfmfu.c
index a62e60d55..875e9a95c 100644
--- a/client/src/cmdhfmfu.c
+++ b/client/src/cmdhfmfu.c
@@ -1113,7 +1113,7 @@ typedef struct {
     uint8_t mpos;
     uint8_t mlen;
     const char *match;
-    uint32_t (*Pwd)(uint8_t *uid);
+    uint32_t (*Pwd)(const uint8_t *uid);
     uint16_t (*Pack)(uint8_t *uid);
     const char *hint;
 } mfu_identify_t;
diff --git a/common/generator.c b/common/generator.c
index 0c230befc..a5538c973 100644
--- a/common/generator.c
+++ b/common/generator.c
@@ -110,7 +110,7 @@ uint32_t ul_ev1_pwdgenB(const uint8_t *uid) {
 }
 
 // Lego Dimension pwd generation algo nickname C.
-uint32_t ul_ev1_pwdgenC(uint8_t *uid) {
+uint32_t ul_ev1_pwdgenC(const uint8_t *uid) {
     uint32_t pwd = 0;
     uint32_t base[] = {
         0xffffffff, 0x28ffffff,
@@ -182,7 +182,7 @@ uint16_t ul_ev1_packgenD(const uint8_t *uid) {
     return BSWAP_16(p & 0xFFFF);
 }
 
-uint32_t ul_ev1_pwdgen_def(uint8_t *uid) {
+uint32_t ul_ev1_pwdgen_def(const uint8_t *uid) {
     return 0xFFFFFFFF;
 }
 uint16_t ul_ev1_packgen_def(uint8_t *uid) {
diff --git a/common/generator.h b/common/generator.h
index 4e41b2a68..ac13c9e3c 100644
--- a/common/generator.h
+++ b/common/generator.h
@@ -13,10 +13,10 @@
 
 #include "common.h"
 
-uint32_t ul_ev1_pwdgen_def(uint8_t *uid);
+uint32_t ul_ev1_pwdgen_def(const uint8_t *uid);
 uint32_t ul_ev1_pwdgenA(const uint8_t *uid);
 uint32_t ul_ev1_pwdgenB(const uint8_t *uid);
-uint32_t ul_ev1_pwdgenC(uint8_t *uid);
+uint32_t ul_ev1_pwdgenC(const uint8_t *uid);
 uint32_t ul_ev1_pwdgenD(const uint8_t *uid);
 
 uint16_t ul_ev1_packgen_def(uint8_t *uid);
diff --git a/common/lfdemod.c b/common/lfdemod.c
index 976195747..2e244a340 100644
--- a/common/lfdemod.c
+++ b/common/lfdemod.c
@@ -94,7 +94,7 @@ static int cmp_uint8(const void *a, const void *b) {
 }
 #endif
 
-void computeSignalProperties(uint8_t *samples, uint32_t size) {
+void computeSignalProperties(const uint8_t *samples, uint32_t size) {
     resetSignal();
 
     if (samples == NULL || size < SIGNAL_MIN_SAMPLES) return;
@@ -189,9 +189,8 @@ void removeSignalOffset(uint8_t *samples, uint32_t size) {
     }
 }
 
-//by marshmellow
-//get high and low values of a wave with passed in fuzz factor. also return noise test = 1 for passed or 0 for only noise
-//void getHiLo(uint8_t *bits, size_t size, int *high, int *low, uint8_t fuzzHi, uint8_t fuzzLo) {
+// get high and low values of a wave with passed in fuzz factor. also return noise test = 1 for passed or 0 for only noise
+// void getHiLo(uint8_t *bits, size_t size, int *high, int *low, uint8_t fuzzHi, uint8_t fuzzLo) {
 void getHiLo(int *high, int *low, uint8_t fuzzHi, uint8_t fuzzLo) {
     // add fuzz.
     *high = (signalprop.high * fuzzHi) / 100;
@@ -212,14 +211,12 @@ void getHiLo(int *high, int *low, uint8_t fuzzHi, uint8_t fuzzLo) {
     // prnt("getHiLo fuzzed: High %d | Low %d", *high, *low);
 }
 
-// by marshmellow
 // pass bits to be tested in bits, length bits passed in bitLen, and parity type (even=0 | odd=1) in pType
 // returns 1 if passed
 bool parityTest(uint32_t bits, uint8_t bitLen, uint8_t pType) {
     return oddparity32(bits) ^ pType;
 }
 
-//by marshmellow
 // takes a array of binary values, start position, length of bits per parity (includes parity bit - MAX 32),
 //   Parity Type (1 for odd; 0 for even; 2 for Always 1's; 3 for Always 0's), and binary Length (length to run)
 size_t removeParity(uint8_t *bits, size_t startIdx, uint8_t pLen, uint8_t pType, size_t bLen) {
@@ -302,11 +299,10 @@ static size_t removeEm410xParity(uint8_t *bits, size_t startIdx, bool isLong, bo
     }
 }
 
-// by marshmellow
 // takes a array of binary values, length of bits per parity (includes parity bit),
-//   Parity Type (1 for odd; 0 for even; 2 Always 1's; 3 Always 0's), and binary Length (length to run)
-//   Make sure *dest is long enough to store original sourceLen + #_of_parities_to_be_added
-size_t addParity(uint8_t *src, uint8_t *dest, uint8_t sourceLen, uint8_t pLen, uint8_t pType) {
+// Parity Type (1 for odd; 0 for even; 2 Always 1's; 3 Always 0's), and binary Length (length to run)
+// Make sure *dest is long enough to store original sourceLen + #_of_parities_to_be_added
+size_t addParity(const uint8_t *src, uint8_t *dest, uint8_t sourceLen, uint8_t pLen, uint8_t pType) {
     uint32_t parityWd = 0;
     size_t j = 0, bitCnt = 0;
     for (int word = 0; word < sourceLen; word += pLen - 1) {
@@ -353,7 +349,7 @@ uint32_t bytebits_to_byte(uint8_t *src, size_t numbits) {
     return num;
 }
 
-//least significant bit first
+// least significant bit first
 uint32_t bytebits_to_byteLSBF(uint8_t *src, size_t numbits) {
     uint32_t num = 0;
     for (int i = 0 ; i < numbits ; i++) {
@@ -362,15 +358,13 @@ uint32_t bytebits_to_byteLSBF(uint8_t *src, size_t numbits) {
     return num;
 }
 
-//by marshmellow
-//search for given preamble in given BitStream and return success = TRUE or fail = FALSE and startIndex and length
+// search for given preamble in given BitStream and return success = TRUE or fail = FALSE and startIndex and length
 bool preambleSearch(uint8_t *bits, uint8_t *preamble, size_t pLen, size_t *size, size_t *startIdx) {
     return preambleSearchEx(bits, preamble, pLen, size, startIdx, false);
 }
-//by marshmellow
 // search for given preamble in given BitStream and return success=1 or fail=0 and startIndex (where it was found) and length if not fineone
 // fineone does not look for a repeating preamble for em4x05/4x69 sends preamble once, so look for it once in the first pLen bits
-//(iceman) FINDONE,  only finds start index. NOT SIZE!.  I see Em410xDecode (lfdemod.c) uses SIZE to determine success
+// (iceman) FINDONE,  only finds start index. NOT SIZE!.  I see Em410xDecode (lfdemod.c) uses SIZE to determine success
 bool preambleSearchEx(uint8_t *bits, uint8_t *preamble, size_t pLen, size_t *size, size_t *startIdx, bool findone) {
     // Sanity check.  If preamble length is bigger than bits length.
     if (*size <= pLen)
@@ -398,7 +392,7 @@ bool preambleSearchEx(uint8_t *bits, uint8_t *preamble, size_t pLen, size_t *siz
 }
 
 // find start of modulating data (for fsk and psk) in case of beginning noise or slow chip startup.
-static size_t findModStart(uint8_t *src, size_t size, uint8_t expWaveSize) {
+static size_t findModStart(const uint8_t *src, size_t size, uint8_t expWaveSize) {
     size_t i = 0;
     size_t waveSizeCnt = 0;
     uint8_t thresholdCnt = 0;
@@ -485,7 +479,7 @@ bool loadWaveCounters(uint8_t *samples, size_t size, int lowToLowWaveLen[], int
     return true;
 }
 
-size_t pskFindFirstPhaseShift(uint8_t *samples, size_t size, uint8_t *curPhase, size_t waveStart, uint16_t fc, uint16_t *fullWaveLen) {
+size_t pskFindFirstPhaseShift(const uint8_t *samples, size_t size, uint8_t *curPhase, size_t waveStart, uint16_t fc, uint16_t *fullWaveLen) {
     uint16_t loopCnt = (size + 3 < 4096) ? size : 4096; //don't need to loop through entire array...
 
     uint16_t avgWaveVal = 0, lastAvgWaveVal;
@@ -512,8 +506,7 @@ size_t pskFindFirstPhaseShift(uint8_t *samples, size_t size, uint8_t *curPhase,
     return 0;
 }
 
-//by marshmellow
-//amplify based on ask edge detection  -  not accurate enough to use all the time
+// amplify based on ask edge detection  -  not accurate enough to use all the time
 void askAmp(uint8_t *bits, size_t size) {
     uint8_t last = 128;
     for (size_t i = 1; i < size; ++i) {
@@ -548,9 +541,8 @@ void manchesterEncodeUint32(uint32_t data_in, uint8_t bitlen_in, uint8_t *bits_o
     }
 }
 
-//by marshmellow
-//encode binary data into binary manchester
-//NOTE: bitstream must have triple the size of "size" available in memory to do the swap
+// encode binary data into binary manchester
+// NOTE: bitstream must have triple the size of "size" available in memory to do the swap
 int ManchesterEncode(uint8_t *bits, size_t size) {
     //allow up to 4096b out (means bits must be at least 2048+4096 to handle the swap)
     size = (size > 2048) ? 2048 : size;
@@ -566,10 +558,9 @@ int ManchesterEncode(uint8_t *bits, size_t size) {
     return i;
 }
 
-// by marshmellow
 // to detect a wave that has heavily clipped (clean) samples
 // loop 1024 samples,   if 250 of them is deemed maxed out,  we assume the wave is clipped.
-bool DetectCleanAskWave(uint8_t *dest, size_t size, uint8_t high, uint8_t low) {
+bool DetectCleanAskWave(const uint8_t *dest, size_t size, uint8_t high, uint8_t low) {
     bool allArePeaks = true;
     uint16_t cntPeaks = 0;
     size_t loopEnd = 1024 + 160;
@@ -600,8 +591,6 @@ bool DetectCleanAskWave(uint8_t *dest, size_t size, uint8_t high, uint8_t low) {
 // -------------------Clock / Bitrate Detection Section------------------------------------------
 // **********************************************************************************************
 
-
-// by marshmellow
 // to help detect clocks on heavily clipped samples
 // based on count of low to low
 int DetectStrongAskClock(uint8_t *dest, size_t size, int high, int low, int *clock) {
@@ -684,7 +673,6 @@ int DetectStrongAskClock(uint8_t *dest, size_t size, int high, int low, int *clo
     return shortestWaveIdx;
 }
 
-// by marshmellow
 // not perfect especially with lower clocks or VERY good antennas (heavy wave clipping)
 // maybe somehow adjust peak trimming value based on samples to fix?
 // return start index of best starting position for that clock and return clock (by reference)
@@ -843,7 +831,7 @@ int DetectASKClock(uint8_t *dest, size_t size, int *clock, int maxErr) {
     return bestStart[best];
 }
 
-int DetectStrongNRZClk(uint8_t *dest, size_t size, int peak, int low, bool *strong) {
+int DetectStrongNRZClk(const uint8_t *dest, size_t size, int peak, int low, bool *strong) {
     //find shortest transition from high to low
     *strong = false;
     size_t i = 0;
@@ -886,8 +874,7 @@ int DetectStrongNRZClk(uint8_t *dest, size_t size, int peak, int low, bool *stro
     return lowestTransition;
 }
 
-//by marshmellow
-//detect nrz clock by reading #peaks vs no peaks(or errors)
+// detect nrz clock by reading #peaks vs no peaks(or errors)
 int DetectNRZClock(uint8_t *dest, size_t size, int clock, size_t *clockStartIdx) {
     size_t i = 0;
     uint8_t clk[] = {8, 16, 32, 40, 50, 64, 100, 128, 255};
@@ -1014,11 +1001,10 @@ int DetectNRZClock(uint8_t *dest, size_t size, int clock, size_t *clockStartIdx)
     return clk[best];
 }
 
-//by marshmellow
-//countFC is to detect the field clock lengths.
-//counts and returns the 2 most common wave lengths
-//mainly used for FSK field clock detection
-uint16_t countFC(uint8_t *bits, size_t size, bool fskAdj) {
+// countFC is to detect the field clock lengths.
+// counts and returns the 2 most common wave lengths
+// mainly used for FSK field clock detection
+uint16_t countFC(const uint8_t *bits, size_t size, bool fskAdj) {
     uint8_t fcLens[] = {0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0};
     uint16_t fcCnts[] = {0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0};
     uint8_t fcLensFnd = 0;
@@ -1105,8 +1091,7 @@ uint16_t countFC(uint8_t *bits, size_t size, bool fskAdj) {
     return (uint16_t)fcLens[best2] << 8 | fcLens[best1];
 }
 
-//by marshmellow
-//detect psk clock by reading each phase shift
+// detect psk clock by reading each phase shift
 // a phase shift is determined by measuring the sample length of each wave
 int DetectPSKClock(uint8_t *dest, size_t size, int clock, size_t *firstPhaseShift, uint8_t *curPhase, uint8_t *fc) {
     uint8_t clk[] = {255, 16, 32, 40, 50, 64, 100, 128, 255}; //255 is not a valid clock
@@ -1204,9 +1189,8 @@ int DetectPSKClock(uint8_t *dest, size_t size, int clock, size_t *firstPhaseShif
     return clk[best];
 }
 
-//by marshmellow
-//detects the bit clock for FSK given the high and low Field Clocks
-uint8_t detectFSKClk(uint8_t *bits, size_t size, uint8_t fcHigh, uint8_t fcLow, int *firstClockEdge) {
+// detects the bit clock for FSK given the high and low Field Clocks
+uint8_t detectFSKClk(const uint8_t *bits, size_t size, uint8_t fcHigh, uint8_t fcLow, int *firstClockEdge) {
 
     if (size == 0)
         return 0;
@@ -1320,7 +1304,9 @@ uint8_t detectFSKClk(uint8_t *bits, size_t size, uint8_t fcHigh, uint8_t fcLow,
 
 
 // look for Sequence Terminator - should be pulses of clk*(1 or 2), clk*2, clk*(1.5 or 2), by idx we mean graph position index...
-static bool findST(int *stStopLoc, int *stStartIdx, int lowToLowWaveLen[], int highToLowWaveLen[], int clk, int tol, int buffSize, size_t *i) {
+static bool findST(int *stStopLoc, int *stStartIdx, 
+                   const int lowToLowWaveLen[], const int highToLowWaveLen[],
+                   int clk, int tol, int buffSize, size_t *i) {
     if (buffSize < *i + 4) return false;
 
     for (; *i < buffSize - 4; *i += 1) {
@@ -1338,8 +1324,8 @@ static bool findST(int *stStopLoc, int *stStartIdx, int lowToLowWaveLen[], int h
     }
     return false;
 }
-//by marshmellow
-//attempt to identify a Sequence Terminator in ASK modulated raw wave
+
+// attempt to identify a Sequence Terminator in ASK modulated raw wave
 bool DetectST(uint8_t *buffer, size_t *size, int *foundclock, size_t *ststart, size_t *stend) {
     size_t bufsize = *size;
     //need to loop through all samples and identify our clock, look for the ST pattern
@@ -1462,11 +1448,10 @@ bool DetectST(uint8_t *buffer, size_t *size, int *foundclock, size_t *ststart, s
     return true;
 }
 
-//by marshmellow
-//take 11 10 01 11 00 and make 01100 ... miller decoding
-//check for phase errors - should never have half a 1 or 0 by itself and should never exceed 1111 or 0000 in a row
-//decodes miller encoded binary
-//NOTE  askrawdemod will NOT demod miller encoded ask unless the clock is manually set to 1/2 what it is detected as!
+// take 11 10 01 11 00 and make 01100 ... miller decoding
+// check for phase errors - should never have half a 1 or 0 by itself and should never exceed 1111 or 0000 in a row
+// decodes miller encoded binary
+// NOTE  askrawdemod will NOT demod miller encoded ask unless the clock is manually set to 1/2 what it is detected as!
 /*
 static int millerRawDecode(uint8_t *bits, size_t *size, int invert) {
     if (*size < 16) return -1;
@@ -1503,10 +1488,9 @@ static int millerRawDecode(uint8_t *bits, size_t *size, int invert) {
 }
 */
 
-//by marshmellow
-//take 01 or 10 = 1 and 11 or 00 = 0
-//check for phase errors - should never have 111 or 000 should be 01001011 or 10110100 for 1010
-//decodes biphase or if inverted it is AKA conditional dephase encoding AKA differential manchester encoding
+// take 01 or 10 = 1 and 11 or 00 = 0
+// check for phase errors - should never have 111 or 000 should be 01001011 or 10110100 for 1010
+// decodes biphase or if inverted it is AKA conditional dephase encoding AKA differential manchester encoding
 int BiphaseRawDecode(uint8_t *bits, size_t *size, int *offset, int invert) {
     //sanity check
     if (*size < 51) return -1;
@@ -1547,9 +1531,8 @@ int BiphaseRawDecode(uint8_t *bits, size_t *size, int *offset, int invert) {
     return errCnt;
 }
 
-//by marshmellow
-//take 10 and 01 and manchester decode
-//run through 2 times and take least errCnt
+// take 10 and 01 and manchester decode
+// run through 2 times and take least errCnt
 // "," indicates 00 or 11 wrong bit
 uint16_t manrawdecode(uint8_t *bits, size_t *size, uint8_t invert, uint8_t *alignPos) {
 
@@ -1596,9 +1579,8 @@ uint16_t manrawdecode(uint8_t *bits, size_t *size, uint8_t invert, uint8_t *alig
     return bestErr;
 }
 
-//by marshmellow
-//demodulates strong heavily clipped samples
-//RETURN: num of errors.  if 0, is ok.
+// demodulates strong heavily clipped samples
+// RETURN: num of errors.  if 0, is ok.
 static uint16_t cleanAskRawDemod(uint8_t *bits, size_t *size, int clk, int invert, int high, int low, int *startIdx) {
     *startIdx = 0;
     size_t bitCnt = 0, smplCnt = 1, errCnt = 0, pos = 0;
@@ -1687,8 +1669,7 @@ static uint16_t cleanAskRawDemod(uint8_t *bits, size_t *size, int clk, int inver
     return errCnt;
 }
 
-//by marshmellow
-//attempts to demodulate ask modulations, askType == 0 for ask/raw, askType==1 for ask/manchester
+// attempts to demodulate ask modulations, askType == 0 for ask/raw, askType==1 for ask/manchester
 int askdemod_ext(uint8_t *bits, size_t *size, int *clk, int *invert, int maxErr, uint8_t amp, uint8_t askType, int *startIdx) {
 
     if (*size == 0) return -1;
@@ -1792,9 +1773,9 @@ int askdemod(uint8_t *bits, size_t *size, int *clk, int *invert, int maxErr, uin
     return askdemod_ext(bits, size, clk, invert, maxErr, amp, askType, &start);
 }
 
-// by marshmellow - demodulate NRZ wave - requires a read with strong signal
+// demodulate NRZ wave - requires a read with strong signal
 // peaks invert bit (high=1 low=0) each clock cycle = 1 bit determined by last peak
-int nrzRawDemod(uint8_t *dest, size_t *size, int *clk, int *invert, int *startIdx) {
+int nrzRawDemod(uint8_t *dest, size_t *size, int *clk, const int *invert, int *startIdx) {
 
     if (signalprop.isnoise) {
         if (g_debugMode == 2) prnt("DEBUG nrzRawDemod: just noise detected - quitting");
@@ -1836,7 +1817,7 @@ int nrzRawDemod(uint8_t *dest, size_t *size, int *clk, int *invert, int *startId
     return 0;
 }
 
-//translate wave to 11111100000 (1 for each short wave [higher freq] 0 for each long wave [lower freq])
+// translate wave to 11111100000 (1 for each short wave [higher freq] 0 for each long wave [lower freq])
 static size_t fsk_wave_demod(uint8_t *dest, size_t size, uint8_t fchigh, uint8_t fclow, int *startIdx) {
 
     if (size < 1024) return 0;   // not enough samples
@@ -1930,7 +1911,7 @@ static size_t fsk_wave_demod(uint8_t *dest, size_t size, uint8_t fchigh, uint8_t
     return numBits; //Actually, it returns the number of bytes, but each byte represents a bit: 1 or 0
 }
 
-//translate 11111100000 to 10
+// translate 11111100000 to 10
 //rfLen = clock, fchigh = larger field clock, fclow = smaller field clock
 static size_t aggregate_bits(uint8_t *dest, size_t size, uint8_t clk, uint8_t invert, uint8_t fchigh, uint8_t fclow, int *startIdx) {
 
@@ -1990,7 +1971,6 @@ static size_t aggregate_bits(uint8_t *dest, size_t size, uint8_t clk, uint8_t in
     return numBits;
 }
 
-//by marshmellow  (from holiman's base)
 // full fsk demod from GraphBuffer wave to decoded 1s and 0s (no mandemod)
 size_t fskdemod(uint8_t *dest, size_t size, uint8_t rfLen, uint8_t invert, uint8_t fchigh, uint8_t fclow, int *start_idx) {
     if (signalprop.isnoise) return 0;
@@ -2002,10 +1982,9 @@ size_t fskdemod(uint8_t *dest, size_t size, uint8_t rfLen, uint8_t invert, uint8
     return size;
 }
 
-// by marshmellow
 // convert psk1 demod to psk2 demod
 // only transition waves are 1s
-//TODO: Iceman - hard coded value 7,  should be #define
+// TODO: Iceman - hard coded value 7,  should be #define
 void psk1TOpsk2(uint8_t *bits, size_t size) {
     uint8_t lastbit = bits[0];
     for (size_t i = 1; i < size; i++) {
@@ -2021,7 +2000,6 @@ void psk1TOpsk2(uint8_t *bits, size_t size) {
     }
 }
 
-// by marshmellow
 // convert psk2 demod to psk1 demod
 // from only transition waves are 1s to phase shifts change bit
 void psk2TOpsk1(uint8_t *bits, size_t size) {
@@ -2034,10 +2012,10 @@ void psk2TOpsk1(uint8_t *bits, size_t size) {
     }
 }
 
-//by marshmellow - demodulate PSK1 wave
-//uses wave lengths (# Samples)
-//TODO: Iceman - hard coded value 7,  should be #define
-int pskRawDemod_ext(uint8_t *dest, size_t *size, int *clock, int *invert, int *startIdx) {
+// demodulate PSK1 wave
+// uses wave lengths (# Samples)
+// TODO: Iceman - hard coded value 7,  should be #define
+int pskRawDemod_ext(uint8_t *dest, size_t *size, int *clock, const int *invert, int *startIdx) {
 
     // sanity check
     if (*size < 170) return -1;
@@ -2139,8 +2117,6 @@ int pskRawDemod(uint8_t *dest, size_t *size, int *clock, int *invert) {
 // -----------------Tag format detection section-------------------------------------------------
 // **********************************************************************************************
 
-
-// by marshmellow
 // FSK Demod then try to locate an AWID ID
 int detectAWID(uint8_t *dest, size_t *size, int *waveStartIdx) {
     //make sure buffer has enough data (96bits * 50clock samples)
@@ -2165,8 +2141,7 @@ int detectAWID(uint8_t *dest, size_t *size, int *waveStartIdx) {
     return (int)start_idx;
 }
 
-//by marshmellow
-//takes 1s and 0s and searches for EM410x format - output EM ID
+// takes 1s and 0s and searches for EM410x format - output EM ID
 int Em410xDecode(uint8_t *bits, size_t *size, size_t *start_idx, uint32_t *hi, uint64_t *lo) {
     // sanity check
     if (bits[1] > 1) return -1;
diff --git a/common/lfdemod.h b/common/lfdemod.h
index 4b840efc0..52766cd2f 100644
--- a/common/lfdemod.h
+++ b/common/lfdemod.h
@@ -16,14 +16,14 @@
 
 #include "common.h"
 
-//might not be high enough for noisy environments
+// might not be high enough for noisy environments
 #define NOISE_AMPLITUDE_THRESHOLD 8
-//ignore buffer with less than x samples
+// ignore buffer with less than x samples
 #define SIGNAL_MIN_SAMPLES 100
-//ignore first x samples of the buffer
+// ignore first x samples of the buffer
 #define SIGNAL_IGNORE_FIRST_SAMPLES 10
 
-//generic
+// generic
 typedef struct {
     int low;
     int high;
@@ -33,48 +33,48 @@ typedef struct {
 } signal_t;
 signal_t *getSignalProperties(void);
 
-void computeSignalProperties(uint8_t *samples, uint32_t size);
+void computeSignalProperties(const uint8_t *samples, uint32_t size);
 void removeSignalOffset(uint8_t *samples, uint32_t size);
 void getNextLow(const uint8_t *samples, size_t size, int low, size_t *i);
 void getNextHigh(const uint8_t *samples, size_t size, int high, size_t *i);
 bool loadWaveCounters(uint8_t *samples, size_t size, int lowToLowWaveLen[], int highToLowWaveLen[], int *waveCnt, int *skip, int *minClk, int *high, int *low);
-size_t pskFindFirstPhaseShift(uint8_t *samples, size_t size, uint8_t *curPhase, size_t waveStart, uint16_t fc, uint16_t *fullWaveLen);
+size_t pskFindFirstPhaseShift(const uint8_t *samples, size_t size, uint8_t *curPhase, size_t waveStart, uint16_t fc, uint16_t *fullWaveLen);
 
-size_t   addParity(uint8_t *src, uint8_t *dest, uint8_t sourceLen, uint8_t pLen, uint8_t pType);
-int      askdemod(uint8_t *bits, size_t *size, int *clk, int *invert, int maxErr, uint8_t amp, uint8_t askType);
-int      askdemod_ext(uint8_t *bits, size_t *size, int *clk, int *invert, int maxErr, uint8_t amp, uint8_t askType, int *startIdx);
-void     askAmp(uint8_t *bits, size_t size);
-int      BiphaseRawDecode(uint8_t *bits, size_t *size, int *offset, int invert);
-int      bits_to_array(const uint8_t *bits, size_t size, uint8_t *dest);
+size_t addParity(const uint8_t *src, uint8_t *dest, uint8_t sourceLen, uint8_t pLen, uint8_t pType);
+int askdemod(uint8_t *bits, size_t *size, int *clk, int *invert, int maxErr, uint8_t amp, uint8_t askType);
+int askdemod_ext(uint8_t *bits, size_t *size, int *clk, int *invert, int maxErr, uint8_t amp, uint8_t askType, int *startIdx);
+void askAmp(uint8_t *bits, size_t size);
+int BiphaseRawDecode(uint8_t *bits, size_t *size, int *offset, int invert);
+int bits_to_array(const uint8_t *bits, size_t size, uint8_t *dest);
 uint32_t bytebits_to_byte(uint8_t *src, size_t numbits);
 uint32_t bytebits_to_byteLSBF(uint8_t *src, size_t numbits);
-uint16_t countFC(uint8_t *bits, size_t size, bool fskAdj);
-int      DetectASKClock(uint8_t *dest, size_t size, int *clock, int maxErr);
-bool     DetectCleanAskWave(uint8_t *dest, size_t size, uint8_t high, uint8_t low);
-uint8_t  detectFSKClk(uint8_t *bits, size_t size, uint8_t fcHigh, uint8_t fcLow, int *firstClockEdge);
-int      DetectNRZClock(uint8_t *dest, size_t size, int clock, size_t *clockStartIdx);
-int      DetectPSKClock(uint8_t *dest, size_t size, int clock, size_t *firstPhaseShift, uint8_t *curPhase, uint8_t *fc);
-int      DetectStrongAskClock(uint8_t *dest, size_t size, int high, int low, int *clock);
-int      DetectStrongNRZClk(uint8_t *dest, size_t size, int peak, int low, bool *strong);
-bool     DetectST(uint8_t *buffer, size_t *size, int *foundclock, size_t *ststart, size_t *stend);
-size_t   fskdemod(uint8_t *dest, size_t size, uint8_t rfLen, uint8_t invert, uint8_t fchigh, uint8_t fclow, int *start_idx);
-//void     getHiLo(uint8_t *bits, size_t size, int *high, int *low, uint8_t fuzzHi, uint8_t fuzzLo);
-void     getHiLo(int *high, int *low, uint8_t fuzzHi, uint8_t fuzzLo);
+uint16_t countFC(const uint8_t *bits, size_t size, bool fskAdj);
+int DetectASKClock(uint8_t *dest, size_t size, int *clock, int maxErr);
+bool DetectCleanAskWave(const uint8_t *dest, size_t size, uint8_t high, uint8_t low);
+uint8_t detectFSKClk(const uint8_t *bits, size_t size, uint8_t fcHigh, uint8_t fcLow, int *firstClockEdge);
+int DetectNRZClock(uint8_t *dest, size_t size, int clock, size_t *clockStartIdx);
+int DetectPSKClock(uint8_t *dest, size_t size, int clock, size_t *firstPhaseShift, uint8_t *curPhase, uint8_t *fc);
+int DetectStrongAskClock(uint8_t *dest, size_t size, int high, int low, int *clock);
+int DetectStrongNRZClk(const uint8_t *dest, size_t size, int peak, int low, bool *strong);
+bool DetectST(uint8_t *buffer, size_t *size, int *foundclock, size_t *ststart, size_t *stend);
+size_t fskdemod(uint8_t *dest, size_t size, uint8_t rfLen, uint8_t invert, uint8_t fchigh, uint8_t fclow, int *start_idx);
+// void getHiLo(uint8_t *bits, size_t size, int *high, int *low, uint8_t fuzzHi, uint8_t fuzzLo);
+void getHiLo(int *high, int *low, uint8_t fuzzHi, uint8_t fuzzLo);
 uint32_t manchesterEncode2Bytes(uint16_t datain);
-void     manchesterEncodeUint32(uint32_t data_in, uint8_t bitlen_in, uint8_t *bits_out, uint16_t *index);
-int      ManchesterEncode(uint8_t *bits, size_t size);
+void manchesterEncodeUint32(uint32_t data_in, uint8_t bitlen_in, uint8_t *bits_out, uint16_t *index);
+int ManchesterEncode(uint8_t *bits, size_t size);
 uint16_t manrawdecode(uint8_t *bits, size_t *size, uint8_t invert, uint8_t *alignPos);
-int      nrzRawDemod(uint8_t *dest, size_t *size, int *clk, int *invert, int *startIdx);
-bool     parityTest(uint32_t bits, uint8_t bitLen, uint8_t pType);
-bool     preambleSearch(uint8_t *bits, uint8_t *preamble, size_t pLen, size_t *size, size_t *startIdx);
-bool     preambleSearchEx(uint8_t *bits, uint8_t *preamble, size_t pLen, size_t *size, size_t *startIdx, bool findone);
-int      pskRawDemod(uint8_t *dest, size_t *size, int *clock, int *invert);
-int      pskRawDemod_ext(uint8_t *dest, size_t *size, int *clock, int *invert, int *startIdx);
-void     psk2TOpsk1(uint8_t *bits, size_t size);
-void     psk1TOpsk2(uint8_t *bits, size_t size);
-size_t   removeParity(uint8_t *bits, size_t startIdx, uint8_t pLen, uint8_t pType, size_t bLen);
+int nrzRawDemod(uint8_t *dest, size_t *size, int *clk, const int *invert, int *startIdx);
+bool parityTest(uint32_t bits, uint8_t bitLen, uint8_t pType);
+bool preambleSearch(uint8_t *bits, uint8_t *preamble, size_t pLen, size_t *size, size_t *startIdx);
+bool preambleSearchEx(uint8_t *bits, uint8_t *preamble, size_t pLen, size_t *size, size_t *startIdx, bool findone);
+int pskRawDemod(uint8_t *dest, size_t *size, int *clock, int *invert);
+int pskRawDemod_ext(uint8_t *dest, size_t *size, int *clock, const int *invert, int *startIdx);
+void psk2TOpsk1(uint8_t *bits, size_t size);
+void psk1TOpsk2(uint8_t *bits, size_t size);
+size_t removeParity(uint8_t *bits, size_t startIdx, uint8_t pLen, uint8_t pType, size_t bLen);
 
-//tag specific
+// tag specific
 int detectAWID(uint8_t *dest, size_t *size, int *waveStartIdx);
 int Em410xDecode(uint8_t *bits, size_t *size, size_t *start_idx, uint32_t *hi, uint64_t *lo);
 int HIDdemodFSK(uint8_t *dest, size_t *size, uint32_t *hi2, uint32_t *hi, uint32_t *lo, int *waveStartIdx);
