commit 0bad12fcac8e9f8bedfe32ff28c2176c3c8e8857
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 1 17:45:43 2022 +0100

    cppcheck

diff --git a/client/src/cmdlft55xx.c b/client/src/cmdlft55xx.c
index e90ebed1b..029f595fb 100644
--- a/client/src/cmdlft55xx.c
+++ b/client/src/cmdlft55xx.c
@@ -1368,7 +1368,7 @@ static bool testQ5Modulation(uint8_t mode, uint8_t modread) {
 }
 
 static int convertQ5bitRate(uint8_t bitRateRead) {
-    uint8_t expected[] = {8, 16, 32, 40, 50, 64, 100, 128};
+    const uint8_t expected[] = {8, 16, 32, 40, 50, 64, 100, 128};
     for (int i = 0; i < 8; i++)
         if (expected[i] == bitRateRead)
             return i;
@@ -1425,7 +1425,7 @@ static bool testQ5(uint8_t mode, uint8_t *offset, int *fndBitRate, uint8_t clk)
 }
 
 static bool testBitRate(uint8_t readRate, uint8_t clk) {
-    uint8_t expected[] = {8, 16, 32, 40, 50, 64, 100, 128};
+    const uint8_t expected[] = {8, 16, 32, 40, 50, 64, 100, 128};
     if (expected[readRate] == clk)
         return true;
 
diff --git a/common/lfdemod.c b/common/lfdemod.c
index 16dc8a56b..976195747 100644
--- a/common/lfdemod.c
+++ b/common/lfdemod.c
@@ -424,8 +424,8 @@ static size_t findModStart(uint8_t *src, size_t size, uint8_t expWaveSize) {
 }
 
 static int getClosestClock(int testclk) {
-    uint16_t clocks[] = {8, 16, 32, 40, 50, 64, 100, 128, 256, 384};
-    uint8_t limit[]  = {1,  2,  4,  4,  5,  8,   8,   8,   8,   8};
+    const uint16_t clocks[] = {8, 16, 32, 40, 50, 64, 100, 128, 256, 384};
+    const uint8_t limit[]  = {1,  2,  4,  4,  5,  8,   8,   8,   8,   8};
 
     for (uint8_t i = 0; i < 10; i++) {
         if (testclk >= clocks[i] - limit[i] && testclk <= clocks[i] + limit[i])
@@ -434,12 +434,12 @@ static int getClosestClock(int testclk) {
     return 0;
 }
 
-void getNextLow(uint8_t *samples, size_t size, int low, size_t *i) {
+void getNextLow(const uint8_t *samples, size_t size, int low, size_t *i) {
     while ((samples[*i] > low) && (*i < size))
         *i += 1;
 }
 
-void getNextHigh(uint8_t *samples, size_t size, int high, size_t *i) {
+void getNextHigh(const uint8_t *samples, size_t size, int high, size_t *i) {
     while ((samples[*i] < high) && (*i < size))
         *i += 1;
 }
diff --git a/common/lfdemod.h b/common/lfdemod.h
index e8d516604..4b840efc0 100644
--- a/common/lfdemod.h
+++ b/common/lfdemod.h
@@ -35,8 +35,8 @@ signal_t *getSignalProperties(void);
 
 void computeSignalProperties(uint8_t *samples, uint32_t size);
 void removeSignalOffset(uint8_t *samples, uint32_t size);
-void getNextLow(uint8_t *samples, size_t size, int low, size_t *i);
-void getNextHigh(uint8_t *samples, size_t size, int high, size_t *i);
+void getNextLow(const uint8_t *samples, size_t size, int low, size_t *i);
+void getNextHigh(const uint8_t *samples, size_t size, int high, size_t *i);
 bool loadWaveCounters(uint8_t *samples, size_t size, int lowToLowWaveLen[], int highToLowWaveLen[], int *waveCnt, int *skip, int *minClk, int *high, int *low);
 size_t pskFindFirstPhaseShift(uint8_t *samples, size_t size, uint8_t *curPhase, size_t waveStart, uint16_t fc, uint16_t *fullWaveLen);
 
