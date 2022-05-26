commit a96777d9c3481623d3dcd09db603af646dff35cb
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Feb 13 12:56:16 2022 +0100

    style

diff --git a/doc/commands.json b/doc/commands.json
index 972730c6e..d4b576162 100644
--- a/doc/commands.json
+++ b/doc/commands.json
@@ -1957,7 +1957,7 @@
         },
         "hf cipurse updkey": {
             "command": "hf cipurse updkey",
-            "description": "update key.",
+            "description": "update key",
             "notes": [
                 "hf cipurse updkey --aid 4144204631 --newkeyn 2 --newkeya 00 --newkey 73737373737373737373737373737373 -> update default application key 2 with default value 73..73",
                 "hf cipurse updkey --newkeyn 1 --newkeya 00 --newkey 0102030405060708090a0b0c0d0e0f10 --commit -> for key 1"
@@ -1982,7 +1982,7 @@
                 "--no-auth execute without authentication",
                 "--commit commit"
             ],
-            "usage": "hf cipurse updakey [-hav] [-n <dec>] [-k <hex>] [--aid <hex 1..16 bytes>] [--fid <hex 2 bytes>] [--mfd] [--newkeyn <dec>] [--newkey <hex 16 byte>] [--newkeya <hex 1 byte>] [--enckeyn <dec>] [--enckey <hex 16 byte>] [--sreq <plain|mac(default)|encode>] [--sresp <plain|mac(default)|encode>] [--no-auth] [--commit]"
+            "usage": "hf cipurse updkey [-hav] [-n <dec>] [-k <hex>] [--aid <hex 1..16 bytes>] [--fid <hex 2 bytes>] [--mfd] [--newkeyn <dec>] [--newkey <hex 16 byte>] [--newkeya <hex 1 byte>] [--enckeyn <dec>] [--enckey <hex 16 byte>] [--sreq <plain|mac(default)|encode>] [--sresp <plain|mac(default)|encode>] [--no-auth] [--commit]"
         },
         "hf cipurse write": {
             "command": "hf cipurse write",
@@ -5625,7 +5625,7 @@
         },
         "hf mfu dump": {
             "command": "hf mfu dump",
-            "description": "dump mifare ultralight/ntag tag to binary/json files. it autodetects card type.supports: ultralight, ultralight-c, ultralight ev1 ntag 203, ntag 210, ntag 212, ntag 213, ntag 215, ntag 216",
+            "description": "dump mifare ultralight/ntag tag to binary/eml/json files. it autodetects card type.supports: ultralight, ultralight-c, ultralight ev1 ntag 203, ntag 210, ntag 212, ntag 213, ntag 215, ntag 216",
             "notes": [
                 "hf mfu dump -f myfile",
                 "hf mfu dump -k aabbccdd -> dump whole tag using pwd aabbccdd",
@@ -10417,6 +10417,6 @@
     "metadata": {
         "commands_extracted": 606,
         "extracted_by": "PM3Help2JSON v1.00",
-        "extracted_on": "2022-02-12T16:50:14"
+        "extracted_on": "2022-02-13T11:23:34"
     }
 }
\ No newline at end of file
