commit 8dc169cd5a9dc3f22b156b288b6722ffdcd354e8
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Nov 20 15:18:43 2021 +0100

    maur keys from Mifare Classic Tool repo

diff --git a/client/dictionaries/mfc_default_keys.dic b/client/dictionaries/mfc_default_keys.dic
index 381226869..c3562bfa8 100644
--- a/client/dictionaries/mfc_default_keys.dic
+++ b/client/dictionaries/mfc_default_keys.dic
@@ -1427,4 +1427,29 @@ B5244E79B0C8 # Key B
 #
 # Ukraine hotel
 f5c1c4c5de34
+#
+# Data from Mifare Classic Tool repo
+# Rotterdam University of applied sciences campus card
+BB7923232725
+A95BD5BB4FC5
+B099335628DF
+A34DA4FAC6C8
+AD7C2A07114B
+53864975068A
+549945110B6C
+B6303CD5B2C6
+AFE444C4BCAA
+B80CC6DE9A03
+A833FE5A4B55
+B533CCD5F6BF
+B7513BFF587C
+B6DF25353654
+9128A4EF4C05
+A9D4B933B07A
+A000D42D2445
+AA5B6C7D88B4
+B5ADEFCA46C4
+BF3FE47637EC
+B290401B0CAD
+AD11006B0601
 
diff --git a/doc/commands.json b/doc/commands.json
index 0db2dc478..a6142b489 100644
--- a/doc/commands.json
+++ b/doc/commands.json
@@ -992,7 +992,7 @@
         },
         "help": {
             "command": "help",
-            "description": "help use `<command> help` for details of a command prefs { edit client/device preferences... } -------- ----------------------- technology ----------------------- analyse { analyse utils... } data { plot window / data buffer manipulation... } emv { emv iso-14443 / iso-7816... } hf { high frequency commands... } hw { hardware commands... } lf { low frequency commands... } nfc { nfc commands... } reveng { crc calculations from reveng software... } smart { smart card iso-7816 commands... } script { scripting commands... } trace { trace manipulation... } wiegand { wiegand format manipulation... } -------- ----------------------- general ----------------------- clear clear screen hints turn hints on / off msleep add a pause in milliseconds rem add a text line in log file quit exit exit program [=] session log /home/iceman/.proxmark3/logs/log_20211108.txt --------------------------------------------------------------------------------------- auto available offline: no run lf search / hf search / data plot / data save",
+            "description": "help use `<command> help` for details of a command prefs { edit client/device preferences... } -------- ----------------------- technology ----------------------- analyse { analyse utils... } data { plot window / data buffer manipulation... } emv { emv iso-14443 / iso-7816... } hf { high frequency commands... } hw { hardware commands... } lf { low frequency commands... } nfc { nfc commands... } reveng { crc calculations from reveng software... } smart { smart card iso-7816 commands... } script { scripting commands... } trace { trace manipulation... } wiegand { wiegand format manipulation... } -------- ----------------------- general ----------------------- clear clear screen hints turn hints on / off msleep add a pause in milliseconds rem add a text line in log file quit exit exit program [=] session log /home/iceman/.proxmark3/logs/log_20211118.txt --------------------------------------------------------------------------------------- auto available offline: no run lf search / hf search / data plot / data save",
             "notes": [
                 "auto"
             ],
@@ -1145,7 +1145,8 @@
             "description": "sends raw bytes over iso14443a. with option to use topaz 14a mode.",
             "notes": [
                 "hf 14a raw -sc 3000 -> select, crc, where 3000 == 'read block 00'",
-                "hf 14a raw -ak -b 7 40 -> send 7 bit byte 0x40"
+                "hf 14a raw -ak -b 7 40 -> send 7 bit byte 0x40",
+                "hf 14a raw --ecp -s -> send ecp before select"
             ],
             "offline": false,
             "options": [
@@ -10056,6 +10057,6 @@
     "metadata": {
         "commands_extracted": 590,
         "extracted_by": "PM3Help2JSON v1.00",
-        "extracted_on": "2021-11-08T09:54:30"
+        "extracted_on": "2021-11-18T23:46:29"
     }
 }
\ No newline at end of file
