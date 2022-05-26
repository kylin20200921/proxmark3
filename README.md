commit 1f470483ec473e88424c7e6540b61056bb143e86
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Feb 18 22:31:34 2022 +0100

    make style

diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index cdd1bf278..b8275f4b2 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -6208,10 +6208,10 @@ static bool mfc_value(const uint8_t *d, uint32_t *val) {
     uint32_t b = MemLeToUint4byte(d + 8);
 
     int val_checks = (
-        (a == b) && (a == ~a_inv) &&
-        (d[12] == (~d[13] & 0xFF)) &&
-        (d[14] == (~d[15] & 0xFF))
-    );
+                         (a == b) && (a == ~a_inv) &&
+                         (d[12] == (~d[13] & 0xFF)) &&
+                         (d[14] == (~d[15] & 0xFF))
+                     );
 
     if (val) {
         *val = a;
@@ -6241,7 +6241,7 @@ static int CmdHF14AMfValue(const char *Cmd) {
     uint32_t value = 0;
 
     if (mfc_value(data, &value))  {
-        PrintAndLogEx(SUCCESS, "Dec... " _YELLOW_("%" PRIu32 ), value);
+        PrintAndLogEx(SUCCESS, "Dec... " _YELLOW_("%" PRIu32), value);
         PrintAndLogEx(SUCCESS, "Hex... " _YELLOW_("0x%" PRIX32), value);
     } else {
         PrintAndLogEx(FAILED, "No value block detected");
diff --git a/client/src/pm3line_vocabulory.h b/client/src/pm3line_vocabulory.h
index 8590b0b26..2c514f7cb 100644
--- a/client/src/pm3line_vocabulory.h
+++ b/client/src/pm3line_vocabulory.h
@@ -305,6 +305,7 @@ const static vocabulory_t vocabulory[] = {
     { 0, "hf mf rdsc" }, 
     { 0, "hf mf restore" }, 
     { 0, "hf mf setmod" }, 
+    { 1, "hf mf value" }, 
     { 1, "hf mf view" }, 
     { 0, "hf mf wipe" }, 
     { 0, "hf mf wrbl" }, 
diff --git a/doc/commands.json b/doc/commands.json
index 1e63c0baf..829e4c93d 100644
--- a/doc/commands.json
+++ b/doc/commands.json
@@ -4098,7 +4098,7 @@
         },
         "hf mf help": {
             "command": "hf mf help",
-            "description": "help this help list list mifare history hardnested nested attack for hardened mifare classic cards decrypt [nt] [ar_enc] [at_enc] [data] - to decrypt sniff or trace acl decode and print mifare classic access rights bytes view display content from tag dump file",
+            "description": "help this help list list mifare history hardnested nested attack for hardened mifare classic cards decrypt [nt] [ar_enc] [at_enc] [data] - to decrypt sniff or trace acl decode and print mifare classic access rights bytes value decode a value block view display content from tag dump file",
             "notes": [],
             "offline": true,
             "options": [],
@@ -4358,6 +4358,19 @@
             ],
             "usage": "hf mf supercard [-hr]"
         },
+        "hf mf value": {
+            "command": "hf mf value",
+            "description": "decode of a mifare value block",
+            "notes": [
+                "hf mf value -d 87d612007829edff87d6120011ee11ee"
+            ],
+            "offline": true,
+            "options": [
+                "-h, --help this help",
+                "-d, --data <hex> 16 hex bytes"
+            ],
+            "usage": "hf mf value [-h] -d <hex>"
+        },
         "hf mf view": {
             "command": "hf mf view",
             "description": "print a mifare classic dump file (bin/eml/json)",
@@ -7162,10 +7175,10 @@
                 "--4205 target chip type em 4205",
                 "--4305 target chip type em 4305 (default)",
                 "--4369 target chip type em 4369",
-                "--4369 target chip type em 4469",
+                "--4469 target chip type em 4469",
                 "-p, --pwd <hex> optional - password, 4 bytes hex"
             ],
-            "usage": "lf em 4x05 wipe [-h] [--4205] [--4305] [--4369] [--4369] [-p <hex>]"
+            "usage": "lf em 4x05 wipe [-h] [--4205] [--4305] [--4369] [--4469] [-p <hex>]"
         },
         "lf em 4x05 write": {
             "command": "lf em 4x05 write",
@@ -10887,8 +10900,8 @@
         }
     },
     "metadata": {
-        "commands_extracted": 687,
+        "commands_extracted": 688,
         "extracted_by": "PM3Help2JSON v1.00",
-        "extracted_on": "2022-02-17T00:41:11"
+        "extracted_on": "2022-02-18T21:30:00"
     }
 }
\ No newline at end of file
diff --git a/doc/commands.md b/doc/commands.md
index cf1be550e..6437758bd 100644
--- a/doc/commands.md
+++ b/doc/commands.md
@@ -460,6 +460,7 @@ Check column "offline" for their availability.
 |`hf mf rdsc             `|N       |`Read MIFARE Classic sector`
 |`hf mf restore          `|N       |`Restore MIFARE Classic binary file to BLANK tag`
 |`hf mf setmod           `|N       |`Set MIFARE Classic EV1 load modulation strength`
+|`hf mf value            `|Y       |`Decode a value block`
 |`hf mf view             `|Y       |`Display content from tag dump file`
 |`hf mf wipe             `|N       |`Wipe card to zeros and default keys/acc`
 |`hf mf wrbl             `|N       |`Write MIFARE Classic block`
