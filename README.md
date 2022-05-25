commit cd825bfac89a878d6717d5a326bd1377d93d067c
Author: iceman1001 <iceman@iuse.se>
Date:   Tue Jan 4 07:28:20 2022 +0100

    cppcheck fixes

diff --git a/client/src/cmdhfwaveshare.c b/client/src/cmdhfwaveshare.c
index 6e6a4fd30..01dc7d63c 100644
--- a/client/src/cmdhfwaveshare.c
+++ b/client/src/cmdhfwaveshare.c
@@ -179,7 +179,7 @@ static void dither_chan_inplace(int16_t *chan, uint16_t width, uint16_t height)
             int16_t newp = oldp > 127 ? 255 : 0;
             chan[X + Y * width] = newp;
             int16_t err = oldp - newp;
-            float m[] = {7, 3, 5, 1};
+            const float m[] = {7, 3, 5, 1};
             if (X < width - 1) {
                 chan[X + 1 +  Y      * width] = chan[X + 1 +  Y      * width] + m[0] / 16 * err;
             }
@@ -239,7 +239,7 @@ static void dither_rgb_inplace(int16_t *chanR, int16_t *chanG, int16_t *chanB, u
             int16_t errR = oldR - newR;
             int16_t errG = oldG - newG;
             int16_t errB = oldB - newB;
-            float m[] = {7, 3, 5, 1};
+            const float m[] = {7, 3, 5, 1};
             if (Y % 2) {
                 if (XX > 0) {
                     chanR[XX - 1 +  Y      * width] = (chanR[XX - 1 +  Y      * width] + m[0] / 16 * errR);
@@ -593,7 +593,7 @@ static int start_drawing_1in54B(uint8_t model_nr, uint8_t *black, uint8_t *red)
     uint8_t step_4[2] = {0xcd, 0x04};
     uint8_t step_6[2] = {0xcd, 0x06};
     uint8_t rx[20] = {0};
-    uint16_t actrxlen[20], i = 0, progress = 0;
+    uint16_t actrxlen[20], i, progress;
 
     if (model_nr == M1in54B) {
         step_5[2] = 100;
@@ -616,6 +616,7 @@ static int start_drawing_1in54B(uint8_t model_nr, uint8_t *black, uint8_t *red)
     if (ret != PM3_SUCCESS) {
         return ret;
     }
+
     PrintAndLogEx(DEBUG, "1.54_Step7: e-paper config2 (red)");
     if (model_nr == M1in54B) {       //1.54inch B Keychain
         for (i = 0; i < 50; i++) {
@@ -635,12 +636,13 @@ static int start_drawing_1in54B(uint8_t model_nr, uint8_t *black, uint8_t *red)
     if (ret != PM3_SUCCESS) {
         return ret;
     }
+
     PrintAndLogEx(DEBUG, "1.54_Step9");
     return PM3_SUCCESS;
 }
 
 static int start_drawing(uint8_t model_nr, uint8_t *black, uint8_t *red) {
-    uint8_t progress = 0;
+    uint8_t progress;
     uint8_t step0[2] = {0xcd, 0x0d};
     uint8_t step1[3] = {0xcd, 0x00, 10};  // select e-paper type and reset e-paper
     //  4 :2.13inch e-Paper
@@ -667,14 +669,12 @@ static int start_drawing(uint8_t model_nr, uint8_t *black, uint8_t *red) {
 // uint8_t step13[2]={0xcd,0x0b};     // Judge whether the power supply is turned off successfully
 // uint8_t step14[2]={0xcd,0x0c};     // The end of the transmission
     uint8_t rx[20];
-    uint16_t actrxlen[20], i = 0;
-
-
+    uint16_t actrxlen[20], i;
 
     clearCommandBuffer();
     SendCommandMIX(CMD_HF_ISO14443A_READER, ISO14A_CONNECT | ISO14A_NO_DISCONNECT, 0, 0, NULL, 0);
     PacketResponseNG resp;
-    if (!WaitForResponseTimeout(CMD_ACK, &resp, 2500)) {
+    if (WaitForResponseTimeout(CMD_ACK, &resp, 2500) == false) {
         PrintAndLogEx(ERR, "No tag found");
         DropField();
         return PM3_ETIMEOUT;
@@ -711,40 +711,41 @@ static int start_drawing(uint8_t model_nr, uint8_t *black, uint8_t *red) {
         return PM3_ESOFT;
     }
     PrintAndLogEx(DEBUG, "model_nr = %d", model_nr);
-    int ret;
+
     PrintAndLogEx(DEBUG, "Step0");
-    ret = transceive_blocking(step0, 2, rx, 20, actrxlen, true);  //cd 0d
+    int ret = transceive_blocking(step0, 2, rx, 20, actrxlen, true);  //cd 0d
     if (ret != PM3_SUCCESS) {
         return ret;
     }
+
     PrintAndLogEx(DEBUG, "Step1: e-paper config");
-    //step1[2] screen model
-    //step8[2] nr of bytes sent at once
-    //step13[2] nr of bytes sent for the second time
+    // step1[2] screen model
+    // step8[2] nr of bytes sent at once
+    // step13[2] nr of bytes sent for the second time
     // generally, step8 sends a black image, step13 sends a red image
-    if (model_nr == M2in13) {        //2.13inch
+    if (model_nr == M2in13) {        // 2.13inch
         step1[2] = EPD_2IN13V2;
         step8[2] = 16;
         step13[2] = 0;
-    } else if (model_nr == M2in9) {  //2.9inch
+    } else if (model_nr == M2in9) {  // 2.9inch
         step1[2] = EPD_2IN9;
         step8[2] = 16;
         step13[2] = 0;
-    } else if (model_nr == M4in2) {  //4.2inch
+    } else if (model_nr == M4in2) {  // 4.2inch
         step1[2] = EPD_4IN2;
         step8[2] = 100;
         step13[2] = 0;
-    } else if (model_nr == M7in5) {  //7.5inch
+    } else if (model_nr == M7in5) {  // 7.5inch
         step1[2] = EPD_7IN5V2;
         step8[2] = 120;
         step13[2] = 0;
-    } else if (model_nr == M2in7) {  //2.7inch
+    } else if (model_nr == M2in7) {  // 2.7inch
         step1[2] = EPD_2IN7;
         step8[2] = 121;
         // Send blank data for the first time, and send other data to 0xff without processing the bottom layer
         step13[2] = 121;
-        //Sending the second data is the real image data. If the previous 0xff is not sent, the last output image is abnormally black
-    } else if (model_nr == M2in13B) {  //2.13inch B
+        // Sending the second data is the real image data. If the previous 0xff is not sent, the last output image is abnormally black
+    } else if (model_nr == M2in13B) {  // 2.13inch B
         step1[2] = EPD_2IN13BC;
         step8[2] = 106;
         step13[2] = 106;
@@ -755,31 +756,35 @@ static int start_drawing(uint8_t model_nr, uint8_t *black, uint8_t *red) {
     }
 
     if (model_nr == M1in54B) {
-        ret = transceive_blocking(step1, 2, rx, 20, actrxlen, true);  //cd 00
+        ret = transceive_blocking(step1, 2, rx, 20, actrxlen, true);  // cd 00
     } else {
         ret = transceive_blocking(step1, 3, rx, 20, actrxlen, true);
     }
     if (ret != PM3_SUCCESS) {
         return ret;
     }
+
     msleep(100);
     PrintAndLogEx(DEBUG, "Step2: e-paper normal mode type");
-    ret = transceive_blocking(step2, 2, rx, 20, actrxlen, true);   //cd 01
+    ret = transceive_blocking(step2, 2, rx, 20, actrxlen, true);  // cd 01
     if (ret != PM3_SUCCESS) {
         return ret;
     }
+
     msleep(100);
     PrintAndLogEx(DEBUG, "Step3: e-paper config1");
-    ret = transceive_blocking(step3, 2, rx, 20, actrxlen, true); //cd 02
+    ret = transceive_blocking(step3, 2, rx, 20, actrxlen, true); // cd 02
     if (ret != PM3_SUCCESS) {
         return ret;
     }
+
     msleep(200);
     PrintAndLogEx(DEBUG, "Step4: e-paper power on");
-    ret = transceive_blocking(step4, 2, rx, 20, actrxlen, true); //cd 03
+    ret = transceive_blocking(step4, 2, rx, 20, actrxlen, true); // cd 03
     if (ret != PM3_SUCCESS) {
         return ret;
     }
+
     if (model_nr == M1in54B) {
         // 1.54B Keychain handler
         PrintAndLogEx(DEBUG, "Start_Drawing_1in54B");
@@ -787,27 +792,27 @@ static int start_drawing(uint8_t model_nr, uint8_t *black, uint8_t *red) {
         if (ret != PM3_SUCCESS) {
             return ret;
         }
-        //1.54B Data transfer is complete and wait for refresh
+        // 1.54B Data transfer is complete and wait for refresh
     } else {
         PrintAndLogEx(DEBUG, "Step5: e-paper config2");
-        ret = transceive_blocking(step5, 2, rx, 20, actrxlen, true);   //cd 05
+        ret = transceive_blocking(step5, 2, rx, 20, actrxlen, true); // cd 05
         if (ret != PM3_SUCCESS) {
             return ret;
         }
         msleep(100);
         PrintAndLogEx(DEBUG, "Step6: EDP load to main") ;
-        ret = transceive_blocking(step6, 2, rx, 20, actrxlen, true); //cd 06
+        ret = transceive_blocking(step6, 2, rx, 20, actrxlen, true); // cd 06
         if (ret != PM3_SUCCESS) {
             return ret;
         }
         msleep(100);
         PrintAndLogEx(DEBUG, "Step7: Data preparation");
-        ret = transceive_blocking(step7, 2, rx, 20, actrxlen, true); //cd 07
+        ret = transceive_blocking(step7, 2, rx, 20, actrxlen, true); // cd 07
         if (ret != PM3_SUCCESS) {
             return ret;
         }
         PrintAndLogEx(DEBUG, "Step8: Start data transfer");
-        if (model_nr == M2in13) {      //2.13inch
+        if (model_nr == M2in13) {      // 2.13inch
             for (i = 0; i < 250; i++) {
                 read_black(i, step8, model_nr, black);
                 ret = transceive_blocking(step8, 19, rx, 20, actrxlen, true); // cd 08
@@ -939,6 +944,7 @@ static int start_drawing(uint8_t model_nr, uint8_t *black, uint8_t *red) {
     } else if (model_nr == M7in5HD) {
         msleep(1000);
     }
+
     uint8_t fail_num = 0;
     while (1) {
         if (model_nr == M1in54B) {
