commit 08284414b00ccaee47befc55f03bfb008bd93f3d
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Mar 27 10:49:08 2022 +0200

    text

diff --git a/doc/commands.json b/doc/commands.json
index cef3438bb..8d302b9f1 100644
--- a/doc/commands.json
+++ b/doc/commands.json
@@ -3785,9 +3785,10 @@
                 "--mini mifare classic mini / s20",
                 "--1k mifare classic 1k / s50 (def)",
                 "--2k mifare classic/plus 2k",
-                "--4k mifare classic 4k / s70"
+                "--4k mifare classic 4k / s70",
+                "-v, --verbose verbose output"
             ],
-            "usage": "hf mf cview [-h] [--mini] [--1k] [--2k] [--4k]"
+            "usage": "hf mf cview [-hv] [--mini] [--1k] [--2k] [--4k]"
         },
         "hf mf cwipe": {
             "command": "hf mf cwipe",
@@ -4005,9 +4006,10 @@
                 "--mini mifare classic mini / s20",
                 "--1k mifare classic 1k / s50 (def)",
                 "--2k mifare classic/plus 2k",
-                "--4k mifare classic 4k / s70"
+                "--4k mifare classic 4k / s70",
+                "-v, --verbose verbose output"
             ],
-            "usage": "hf mf eview [-h] [--mini] [--1k] [--2k] [--4k]"
+            "usage": "hf mf eview [-hv] [--mini] [--1k] [--2k] [--4k]"
         },
         "hf mf fchk": {
             "command": "hf mf fchk",
@@ -4081,9 +4083,10 @@
                 "--1k mifare classic 1k / s50 (def)",
                 "--2k mifare classic/plus 2k",
                 "--4k mifare classic 4k / s70",
-                "-p, --pwd <hex> password 4bytes"
+                "-p, --pwd <hex> password 4bytes",
+                "-v, --verbose verbose output"
             ],
-            "usage": "hf mf gview [-h] [--mini] [--1k] [--2k] [--4k] [-p <hex>]"
+            "usage": "hf mf gview [-hv] [--mini] [--1k] [--2k] [--4k] [-p <hex>]"
         },
         "hf mf hardnested": {
             "command": "hf mf hardnested",
@@ -10934,6 +10937,6 @@
     "metadata": {
         "commands_extracted": 689,
         "extracted_by": "PM3Help2JSON v1.00",
-        "extracted_on": "2022-03-24T10:21:12"
+        "extracted_on": "2022-03-26T19:01:37"
     }
 }
\ No newline at end of file
