commit b888bba70d6012235558e83851bb4b9ffdd4eb88
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Nov 8 10:56:19 2021 +0100

    textual

diff --git a/doc/commands.json b/doc/commands.json
index 405d1c236..0db2dc478 100644
--- a/doc/commands.json
+++ b/doc/commands.json
@@ -357,7 +357,7 @@
         },
         "data help": {
             "command": "data help",
-            "description": "help this help ----------- ------------------------- modulation------------------------- biphaserawdecode biphase decode bin stream in demodbuffer detectclock detect ask, fsk, nrz, psk clock rate of wave in graphbuffer fsktonrz convert fsk2 to nrz wave for alternate fsk demodulating (for weak fsk) manrawdecode manchester decode binary stream in demodbuffer modulation identify lf signal for clock and modulation rawdemod demodulate the data in the graphbuffer and output binary ----------- ------------------------- graph------------------------- askedgedetect adjust graph for manual ask demod using the length of sample differences to detect the edge of a wave autocorr autocorrelation over window dirthreshold max rising higher up-thres/ min falling lower down-thres, keep rest as prev. decimate decimate samples undecimate un-decimate samples hide hide graph window hpf remove dc offset from trace iir apply iir buttersworth filter on plot data grid overlay grid on graph window ltrim trim samples from left of trace mtrim trim out samples from the specified start to the specified stop norm normalize max/min to +/-128 plot show graph window rtrim trim samples from right of trace setgraphmarkers set blue and orange marker in graph window shiftgraphzero shift 0 for graphed wave + or - shift value timescale set a timescale to get a differential reading between the yellow and purple markers as time duration zerocrossings count time between zero-crossings convertbitstream convert graphbuffer's 0/1 values to 127 / -127 getbitstream convert graphbuffer's >=1 values to 1 and <1 to 0 ----------- ------------------------- general------------------------- asn1 asn1 decoder bin2hex converts binary to hexadecimal clear clears bigbuf on deviceside and graph window hex2bin converts hexadecimal to binary load load contents of file into graph window print print the data in the demodbuffer save save signal trace data (from graph window) setdebugmode set debugging level on client side --------------------------------------------------------------------------------------- data biphaserawdecode available offline: yes biphase decode binary stream in demodbuffer converts 10 or 01 -> 1 and 11 or 00 -> 0 - must have binary sequence in demodbuffer (run `data rawdemod --ar` before) - invert for conditional dephase encoding (cdp) aka differential manchester",
+            "description": "help this help ----------- ------------------------- modulation------------------------- biphaserawdecode biphase decode bin stream in demodbuffer detectclock detect ask, fsk, nrz, psk clock rate of wave in graphbuffer fsktonrz convert fsk2 to nrz wave for alternate fsk demodulating (for weak fsk) manrawdecode manchester decode binary stream in demodbuffer modulation identify lf signal for clock and modulation pwmdemod pwm demodulate the data in the graphbuffer and output binary rawdemod demodulate the data in the graphbuffer and output binary ----------- ------------------------- graph------------------------- askedgedetect adjust graph for manual ask demod using the length of sample differences to detect the edge of a wave autocorr autocorrelation over window dirthreshold max rising higher up-thres/ min falling lower down-thres, keep rest as prev. decimate decimate samples undecimate un-decimate samples hide hide graph window hpf remove dc offset from trace iir apply iir buttersworth filter on plot data grid overlay grid on graph window ltrim trim samples from left of trace mtrim trim out samples from the specified start to the specified stop norm normalize max/min to +/-128 plot show graph window rtrim trim samples from right of trace setgraphmarkers set blue and orange marker in graph window shiftgraphzero shift 0 for graphed wave + or - shift value timescale set a timescale to get a differential reading between the yellow and purple markers as time duration zerocrossings count time between zero-crossings convertbitstream convert graphbuffer's 0/1 values to 127 / -127 getbitstream convert graphbuffer's >=1 values to 1 and <1 to 0 ----------- ------------------------- general------------------------- asn1 asn1 decoder bin2hex converts binary to hexadecimal clear clears bigbuf on deviceside and graph window hex2bin converts hexadecimal to binary load load contents of file into graph window print print the data in the demodbuffer save save signal trace data (from graph window) setdebugmode set debugging level on client side --------------------------------------------------------------------------------------- data biphaserawdecode available offline: yes biphase decode binary stream in demodbuffer converts 10 or 01 -> 1 and 11 or 00 -> 0 - must have binary sequence in demodbuffer (run `data rawdemod --ar` before) - invert for conditional dephase encoding (cdp) aka differential manchester",
             "notes": [
                 "data biphaserawdecode -> decode biphase bitstream from the demodbuffer",
                 "data biphaserawdecode -oi -> decode biphase bitstream from the demodbuffer, adjust offset, and invert output"
@@ -542,6 +542,22 @@
             ],
             "usage": "data print [-hisx] [-o <dec>]"
         },
+        "data pwmdemod": {
+            "command": "data pwmdemod",
+            "description": "pulse width demodulate the data in the graphbuffer and output binary",
+            "notes": [
+                "data pwmdemod",
+                "data pwmdemod -t 65 -> specify first wave index"
+            ],
+            "offline": true,
+            "options": [
+                "-h, --help this help",
+                "-t, --tol <dec> set tolerance level (default 5)",
+                "-o, --one <dec> set samples width for one pulse (default auto)",
+                "-z, --zero <dec> set samples width for zero pulse (default auto)"
+            ],
+            "usage": "data pwmdemod [-h] [-t <dec>] [-o <dec>] [-z <dec>]"
+        },
         "data rawdemod": {
             "command": "data rawdemod",
             "description": "demodulate the data in the graphbuffer and output binary",
@@ -976,7 +992,7 @@
         },
         "help": {
             "command": "help",
-            "description": "help use `<command> help` for details of a command prefs { edit client/device preferences... } -------- ----------------------- technology ----------------------- analyse { analyse utils... } data { plot window / data buffer manipulation... } emv { emv iso-14443 / iso-7816... } hf { high frequency commands... } hw { hardware commands... } lf { low frequency commands... } nfc { nfc commands... } reveng { crc calculations from reveng software... } smart { smart card iso-7816 commands... } script { scripting commands... } trace { trace manipulation... } wiegand { wiegand format manipulation... } -------- ----------------------- general ----------------------- clear clear screen hints turn hints on / off msleep add a pause in milliseconds rem add a text line in log file quit exit exit program [=] session log /home/phil/.proxmark3/logs/log_20211016.txt --------------------------------------------------------------------------------------- auto available offline: no run lf search / hf search / data plot / data save",
+            "description": "help use `<command> help` for details of a command prefs { edit client/device preferences... } -------- ----------------------- technology ----------------------- analyse { analyse utils... } data { plot window / data buffer manipulation... } emv { emv iso-14443 / iso-7816... } hf { high frequency commands... } hw { hardware commands... } lf { low frequency commands... } nfc { nfc commands... } reveng { crc calculations from reveng software... } smart { smart card iso-7816 commands... } script { scripting commands... } trace { trace manipulation... } wiegand { wiegand format manipulation... } -------- ----------------------- general ----------------------- clear clear screen hints turn hints on / off msleep add a pause in milliseconds rem add a text line in log file quit exit exit program [=] session log /home/iceman/.proxmark3/logs/log_20211108.txt --------------------------------------------------------------------------------------- auto available offline: no run lf search / hf search / data plot / data save",
             "notes": [
                 "auto"
             ],
@@ -1144,15 +1160,20 @@
                 "-t, --timeout <ms> timeout in milliseconds",
                 "-v, --verbose verbose output",
                 "--topaz use topaz protocol to send command",
+                "--ecp use enhanced contactless polling",
+                "--mag use apple magsafe polling",
                 "<hex> raw bytes to send"
             ],
-            "usage": "hf 14a raw [-hack3rsv] [-b <dec>] [-t <ms>] [--topaz] <hex> [<hex>]..."
+            "usage": "hf 14a raw [-hack3rsv] [-b <dec>] [-t <ms>] [--topaz] [--ecp] [--mag] <hex> [<hex>]..."
         },
         "hf 14a reader": {
             "command": "hf 14a reader",
             "description": "act as a iso-14443a reader to identify tag. look for iso-14443a tags until enter or the pm3 button is pressed",
             "notes": [
-                "hf 14a reader -@ -> continuous mode"
+                "hf 14a reader",
+                "hf 14a reader -@ -> continuous mode",
+                "hf 14a reader --ecp -> trigger apple enhanced contactless polling",
+                "hf 14a reader --mag -> trigger apple magsafe polling"
             ],
             "offline": false,
             "options": [
@@ -1161,9 +1182,11 @@
                 "-s, --silent silent (no messages)",
                 "--drop just drop the signal field",
                 "--skip iso14443-3 select only (skip rats)",
+                "--ecp use enhanced contactless polling",
+                "--mag use apple magsafe polling",
                 "-@ continuous reader mode"
             ],
-            "usage": "hf 14a reader [-hks@] [--drop] [--skip]"
+            "usage": "hf 14a reader [-hks@] [--drop] [--skip] [--ecp] [--mag]"
         },
         "hf 14a sim": {
             "command": "hf 14a sim",
@@ -10031,8 +10054,8 @@
         }
     },
     "metadata": {
-        "commands_extracted": 589,
+        "commands_extracted": 590,
         "extracted_by": "PM3Help2JSON v1.00",
-        "extracted_on": "2021-10-16T21:43:45"
+        "extracted_on": "2021-11-08T09:54:30"
     }
 }
\ No newline at end of file
diff --git a/include/protocols.h b/include/protocols.h
index fc0369f3e..da67d42f0 100644
--- a/include/protocols.h
+++ b/include/protocols.h
@@ -423,7 +423,7 @@ ISO 7816-4 Basic interindustry commands. For command APDU's.
 #define MFDES_AUTHENTICATE_AES          0xAA
 
 //  Leakage Resilient Primitive (LRP)
-#define MFDES_AUTHENTICATE_EV2F         0x71  // LRP, AuthenticateLRPFirst    
+#define MFDES_AUTHENTICATE_EV2F         0x71  // LRP, AuthenticateLRPFirst
 #define MFDES_AUTHENTICATE_EV2NF        0x77  // LRP, AuthenticateLRPNonFirst
 
 #define MFDES_CREDIT                    0x0C
