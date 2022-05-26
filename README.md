commit a4bc2c552134705370bf7ce1b09040e6babc6ed3
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Feb 14 21:44:15 2022 +0100

    make style

diff --git a/armsrc/appmain.c b/armsrc/appmain.c
index c6e188ec0..afe10694c 100644
--- a/armsrc/appmain.c
+++ b/armsrc/appmain.c
@@ -1285,12 +1285,12 @@ static void PacketReceived(PacketCommandNG *packet) {
             break;
         }
         case CMD_HF_LEGIC_WRITER: {
-            legic_packet_t *payload = (legic_packet_t*) packet->data.asBytes;
+            legic_packet_t *payload = (legic_packet_t *) packet->data.asBytes;
             LegicRfWriter(payload->offset, payload->len, payload->iv, payload->data);
             break;
         }
         case CMD_HF_LEGIC_READER: {
-            legic_packet_t *payload = (legic_packet_t*) packet->data.asBytes;
+            legic_packet_t *payload = (legic_packet_t *) packet->data.asBytes;
             LegicRfReader(payload->offset, payload->len, payload->iv);
             break;
         }
@@ -1305,7 +1305,7 @@ static void PacketReceived(PacketCommandNG *packet) {
             // destroy the Emulator Memory.
             //-----------------------------------------------------------------------------
             FpgaDownloadAndGo(FPGA_BITSTREAM_HF);
-            legic_packet_t *payload = (legic_packet_t*) packet->data.asBytes;
+            legic_packet_t *payload = (legic_packet_t *) packet->data.asBytes;
             emlSet(payload->data, payload->offset, payload->len);
             break;
         }
diff --git a/armsrc/legicrf.c b/armsrc/legicrf.c
index 31f9691fc..637d22fee 100644
--- a/armsrc/legicrf.c
+++ b/armsrc/legicrf.c
@@ -520,7 +520,7 @@ void LegicRfReader(uint16_t offset, uint16_t len, uint8_t iv) {
     }
 
     // OK
-    reply_ng(CMD_HF_LEGIC_READER, PM3_SUCCESS, (uint8_t*)&len, sizeof(len));
+    reply_ng(CMD_HF_LEGIC_READER, PM3_SUCCESS, (uint8_t *)&len, sizeof(len));
 
 OUT:
     switch_off();
@@ -559,7 +559,7 @@ void LegicRfWriter(uint16_t offset, uint16_t len, uint8_t iv, uint8_t *data) {
     }
 
     // OK
-    reply_ng(CMD_HF_LEGIC_WRITER, PM3_SUCCESS, (uint8_t*)&len, sizeof(len));
+    reply_ng(CMD_HF_LEGIC_WRITER, PM3_SUCCESS, (uint8_t *)&len, sizeof(len));
 
 OUT:
     switch_off();
diff --git a/client/src/cmdhflegic.c b/client/src/cmdhflegic.c
index d0c6f80f6..38abf668b 100644
--- a/client/src/cmdhflegic.c
+++ b/client/src/cmdhflegic.c
@@ -448,7 +448,7 @@ static int CmdLegicSim(const char *Cmd) {
                   "Simulates a LEGIC Prime tag.\n"
                   "Following types supported (MIM22, MIM256, MIM1024)",
                   "hf legic sim --22\n"
-                  );
+                 );
 
     void *argtable[] = {
         arg_param_begin,
@@ -580,7 +580,7 @@ static int CmdLegicWrbl(const char *Cmd) {
 
     PacketResponseNG resp;
     clearCommandBuffer();
-    SendCommandNG(CMD_HF_LEGIC_WRITER, (uint8_t*)payload, sizeof(legic_packet_t) + dlen);
+    SendCommandNG(CMD_HF_LEGIC_WRITER, (uint8_t *)payload, sizeof(legic_packet_t) + dlen);
     free(payload);
 
     uint8_t timeout = 0;
@@ -593,7 +593,7 @@ static int CmdLegicWrbl(const char *Cmd) {
         }
     }
     PrintAndLogEx(NORMAL, "");
-    
+
     if (resp.status != PM3_SUCCESS) {
         PrintAndLogEx(WARNING, "Failed writing tag");
         return PM3_ERFTRANS;
@@ -655,7 +655,7 @@ int legic_read_mem(uint32_t offset, uint32_t len, uint32_t iv, uint8_t *out, uin
     payload->len = len;
 
     clearCommandBuffer();
-    SendCommandNG(CMD_HF_LEGIC_READER, (uint8_t*)payload, sizeof(legic_packet_t));
+    SendCommandNG(CMD_HF_LEGIC_READER, (uint8_t *)payload, sizeof(legic_packet_t));
     PacketResponseNG resp;
 
     uint8_t timeout = 0;
@@ -750,7 +750,7 @@ void legic_seteml(uint8_t *src, uint32_t offset, uint32_t numofbytes) {
         memcpy(payload->data, src + i, len);
 
         clearCommandBuffer();
-        SendCommandNG(CMD_HF_LEGIC_ESET, (uint8_t*)payload, sizeof(legic_packet_t) + len);
+        SendCommandNG(CMD_HF_LEGIC_ESET, (uint8_t *)payload, sizeof(legic_packet_t) + len);
         free(payload);
     }
 }
@@ -818,7 +818,7 @@ static int CmdLegicDump(const char *Cmd) {
     payload->len = dumplen;
 
     clearCommandBuffer();
-    SendCommandNG(CMD_HF_LEGIC_READER, (uint8_t*)payload, sizeof(legic_packet_t));
+    SendCommandNG(CMD_HF_LEGIC_READER, (uint8_t *)payload, sizeof(legic_packet_t));
     PacketResponseNG resp;
 
     uint8_t timeout = 0;
@@ -985,7 +985,7 @@ static int CmdLegicRestore(const char *Cmd) {
         memcpy(payload->data, data + i, len);
 
         clearCommandBuffer();
-        SendCommandNG(CMD_HF_LEGIC_WRITER, (uint8_t*)payload, sizeof(legic_packet_t) + len);
+        SendCommandNG(CMD_HF_LEGIC_WRITER, (uint8_t *)payload, sizeof(legic_packet_t) + len);
         free(payload);
 
         uint8_t timeout = 0;
@@ -1019,7 +1019,7 @@ static int CmdLegicELoad(const char *Cmd) {
                   "Loads a LEGIC Prime dump file into emulator memory",
                   "hf legic eload -f myfile\n"
                   "hf legic eload -f myfile --obfuscate\n"
-                );
+                 );
 
     void *argtable[] = {
         arg_param_begin,
@@ -1073,8 +1073,8 @@ static int CmdLegicELoad(const char *Cmd) {
 
     // validation
     if (bytes_read != LEGIC_PRIME_MIM22 &&
-        bytes_read != LEGIC_PRIME_MIM256 &&
-        bytes_read != LEGIC_PRIME_MIM1024) {
+            bytes_read != LEGIC_PRIME_MIM256 &&
+            bytes_read != LEGIC_PRIME_MIM1024) {
         PrintAndLogEx(ERR, "File content error. Read %zu bytes", bytes_read);
         free(data);
         return PM3_EFILE;
@@ -1099,7 +1099,7 @@ static int CmdLegicESave(const char *Cmd) {
                   "hf legic esave                    --> uses UID as filename\n"
                   "hf legic esave -f myfile --22\n"
                   "hf legic esave -f myfile --22 --de\n"
-                );
+                 );
 
     void *argtable[] = {
         arg_param_begin,
@@ -1282,7 +1282,7 @@ static int CmdLegicWipe(const char *Cmd) {
         memcpy(payload->data, data + i, len);
 
         clearCommandBuffer();
-        SendCommandNG(CMD_HF_LEGIC_WRITER, (uint8_t*)payload, sizeof(legic_packet_t) + len);
+        SendCommandNG(CMD_HF_LEGIC_WRITER, (uint8_t *)payload, sizeof(legic_packet_t) + len);
         free(payload);
 
         uint8_t timeout = 0;
@@ -1369,11 +1369,11 @@ static int CmdLegicView(const char *Cmd) {
     PrintAndLogEx(INFO, "---+-------------------------------------------------+-----------------");
     print_hex_break(dump, bytes_read, 16);
     free(dump);
-    return PM3_SUCCESS;    
+    return PM3_SUCCESS;
 }
 
 static command_t CommandTable[] =  {
-    {"-----------", CmdHelp,      AlwaysAvailable, "--------------------- " _CYAN_("operations") " ---------------------"},    
+    {"-----------", CmdHelp,      AlwaysAvailable, "--------------------- " _CYAN_("operations") " ---------------------"},
     {"help",    CmdHelp,          AlwaysAvailable, "This help"},
     {"dump",    CmdLegicDump,     IfPm3Legicrf,    "Dump LEGIC Prime tag to binary file"},
     {"info",    CmdLegicInfo,     IfPm3Legicrf,    "Display deobfuscated and decoded LEGIC Prime tag data"},
diff --git a/client/src/pm3line_vocabulory.h b/client/src/pm3line_vocabulory.h
index a54adb525..8590b0b26 100644
--- a/client/src/pm3line_vocabulory.h
+++ b/client/src/pm3line_vocabulory.h
@@ -262,18 +262,20 @@ const static vocabulory_t vocabulory[] = {
     { 1, "hf iclass permutekey" }, 
     { 1, "hf iclass view" }, 
     { 1, "hf legic help" }, 
+    { 0, "hf legic dump" }, 
+    { 0, "hf legic info" }, 
     { 1, "hf legic list" }, 
+    { 0, "hf legic rdbl" }, 
     { 0, "hf legic reader" }, 
-    { 0, "hf legic info" }, 
-    { 0, "hf legic dump" }, 
     { 0, "hf legic restore" }, 
-    { 0, "hf legic rdbl" }, 
-    { 0, "hf legic sim" }, 
+    { 0, "hf legic wipe" }, 
     { 0, "hf legic wrbl" }, 
+    { 0, "hf legic sim" }, 
+    { 0, "hf legic eload" }, 
+    { 0, "hf legic esave" }, 
+    { 0, "hf legic eview" }, 
     { 1, "hf legic crc" }, 
-    { 1, "hf legic eload" }, 
-    { 1, "hf legic esave" }, 
-    { 0, "hf legic wipe" }, 
+    { 1, "hf legic view" }, 
     { 1, "hf lto help" }, 
     { 0, "hf lto dump" }, 
     { 0, "hf lto restore" }, 
diff --git a/doc/commands.json b/doc/commands.json
index d4b576162..dc72eb427 100644
--- a/doc/commands.json
+++ b/doc/commands.json
@@ -3165,77 +3165,71 @@
             ],
             "usage": "hf legic crc [-h] -d <hex> [--mcc <hex>] [-t <dec>]"
         },
-        "hf legic dump": {
-            "command": "hf legic dump",
-            "description": "read all memory from legic prime mim22, mim256, mim1024 and saves bin/eml/json dump file it autodetects card type.",
-            "notes": [
-                "hf legic dump -> use uid as filename",
-                "hf legic dump -f myfile -> use user specified filename",
-                "hf legic dump --de -> use uid as filename and deobfuscate data"
-            ],
-            "offline": false,
-            "options": [
-                "-h, --help this help",
-                "-f, --file <fn> dump filename",
-                "--de deobfuscate dump data (xor with mcc)"
-            ],
-            "usage": "hf legic dump [-h] [-f <fn>] [--de]"
-        },
         "hf legic eload": {
             "command": "hf legic eload",
-            "description": "loads a legic binary dump into emulator memory",
+            "description": "loads a legic prime dump file into emulator memory",
             "notes": [
-                "hf legic eload -f myfile -t 0 -> simulate type mim22",
-                "hf legic eload -f myfile -t 1 -> simulate type mim256 (default)",
-                "hf legic eload -f myfile -t 2 -> simulate type mim1024"
+                "hf legic eload -f myfile",
+                "hf legic eload -f myfile --obfuscate"
             ],
-            "offline": true,
+            "offline": false,
             "options": [
                 "-h, --help this help",
-                "-f, --file <fn> filename to restore",
-                "-t, --type <dec> tag type to simulate.",
+                "-f, --file <fn> filename to load",
                 "--obfuscate obfuscate dump data (xor with mcc)"
             ],
-            "usage": "hf legic eload [-h] -f <fn> [-t <dec>] [--obfuscate]"
+            "usage": "hf legic eload [-h] -f <fn> [--obfuscate]"
         },
         "hf legic esave": {
             "command": "hf legic esave",
-            "description": "saves bin/eml/json dump file of emulator memory",
+            "description": "saves a (bin/eml/json) dump file of emulator memory",
             "notes": [
                 "hf legic esave -> uses uid as filename",
-                "hf legic esave -f myfile -t 0 -> type mim22",
-                "hf legic esave -f myfile -t 1 -> type mim256 (default)",
-                "hf legic esave -f myfile -t 2 -> type mim1024"
+                "hf legic esave -f myfile --22",
+                "hf legic esave -f myfile --22 --de"
             ],
-            "offline": true,
+            "offline": false,
             "options": [
                 "-h, --help this help",
                 "-f, --file <fn> filename to save",
-                "-t, --type <dec> tag type",
-                "--deobfuscate de-obfuscate dump data (xor with mcc)"
+                "--22 legic prime mim22",
+                "--256 legic prime mim256 (def)",
+                "--1024 legic prime mim1024",
+                "--de de-obfuscate dump data (xor with mcc)"
             ],
-            "usage": "hf legic esave [-h] [-f <fn>] [-t <dec>] [--deobfuscate]"
+            "usage": "hf legic esave [-h] [-f <fn>] [--22] [--256] [--1024] [--de]"
+        },
+        "hf legic eview": {
+            "command": "hf legic eview",
+            "description": "it displays emulator memory",
+            "notes": [
+                "hf legic eview",
+                "hf legic eview --22"
+            ],
+            "offline": false,
+            "options": [
+                "-h, --help this help",
+                "--22 legic prime mim22",
+                "--256 legic prime mim256 (def)",
+                "--1024 legic prime mim1024"
+            ],
+            "usage": "hf legic eview [-h] [--22] [--256] [--1024]"
         },
         "hf legic help": {
             "command": "hf legic help",
-            "description": "help this help list list legic history crc calculate legic crc over given bytes eload load binary dump to emulator memory esave save emulator memory to binary file --------------------------------------------------------------------------------------- hf legic list available offline: yes alias of `trace list -t legic` with selected protocol data to annotate trace buffer you can load a trace from file (see `trace load -h`) or it be downloaded from device by default it accepts all other arguments of `trace list`. note that some might not be relevant for this specific protocol",
+            "description": "----------- --------------------- operations --------------------- help this help list list legic history ----------- --------------------- simulation --------------------- ----------- --------------------- utils --------------------- crc calculate legic crc over given bytes view display content from tag dump file --------------------------------------------------------------------------------------- hf legic dump available offline: no read all memory from legic prime tags and saves to (bin/eml/json) dump file it autodetects card type (mim22, mim256, mim1024)",
             "notes": [
-                "hf legic list -f -> show frame delay times",
-                "hf legic list -1 -> use trace buffer"
+                "hf legic dump -> use uid as filename",
+                "hf legic dump -f myfile",
+                "hf legic dump --de -> use uid as filename and deobfuscate data"
             ],
             "offline": true,
             "options": [
                 "-h, --help this help",
-                "-1, --buffer use data from trace buffer",
-                "-f show frame delay times",
-                "-c mark crc bytes",
-                "-r show relative times (gap and duration)",
-                "-u display times in microseconds instead of clock cycles",
-                "-x show hexdump to convert to pcap(ng)",
-                "or to import into wireshark using encapsulation type \"iso 14443\"",
-                "--dict <file> use dictionary keys file"
+                "-f, --file <fn> dump filename",
+                "--de deobfuscate dump data (xor with mcc)"
             ],
-            "usage": "hf legic list [-h1fcrux] [--dict <file>]"
+            "usage": "hf legic dump [-h] [-f <fn>] [--de]"
         },
         "hf legic info": {
             "command": "hf legic info",
@@ -3249,13 +3243,34 @@
             ],
             "usage": "hf legic info [-h]"
         },
+        "hf legic list": {
+            "command": "hf legic list",
+            "description": "alias of `trace list -t legic` with selected protocol data to annotate trace buffer you can load a trace from file (see `trace load -h`) or it be downloaded from device by default it accepts all other arguments of `trace list`. note that some might not be relevant for this specific protocol",
+            "notes": [
+                "hf legic list -f -> show frame delay times",
+                "hf legic list -1 -> use trace buffer"
+            ],
+            "offline": true,
+            "options": [
+                "-h, --help this help",
+                "-1, --buffer use data from trace buffer",
+                "-f show frame delay times",
+                "-c mark crc bytes",
+                "-r show relative times (gap and duration)",
+                "-u display times in microseconds instead of clock cycles",
+                "-x show hexdump to convert to pcap(ng)",
+                "or to import into wireshark using encapsulation type \"iso 14443\"",
+                "--dict <file> use dictionary keys file"
+            ],
+            "usage": "hf legic list [-h1fcrux] [--dict <file>]"
+        },
         "hf legic rdbl": {
             "command": "hf legic rdbl",
             "description": "read data from a legic prime tag",
             "notes": [
-                "hf legic rdbl -o 0 -l 16 -> reads from byte[0] 16 bytes(system header)",
-                "hf legic rdbl -o 0 -l 4 --iv 55 -> reads from byte[0] 4 bytes with iv 0x55",
-                "hf legic rdbl -o 0 -l 256 --iv 55 -> reads from byte[0] 256 bytes with iv 0x55"
+                "hf legic rdbl -o 0 -l 16 -> read 16 bytes from offset 0 (system header)",
+                "hf legic rdbl -o 0 -l 4 --iv 55 -> read 4 bytes from offset 0",
+                "hf legic rdbl -o 0 -l 256 --iv 55 -> read 256 bytes from offset 0"
             ],
             "offline": false,
             "options": [
@@ -3264,7 +3279,7 @@
                 "-l, --length <dec> number of bytes to read",
                 "--iv <hex> initialization vector to use. must be odd and 7bits max"
             ],
-            "usage": "hf legic rdbl [-h] -o <dec> -l <dec> [--iv <hex>]"
+            "usage": "hf legic rdbl [-h] [-o <dec>] [-l <dec>] [--iv <hex>]"
         },
         "hf legic reader": {
             "command": "hf legic reader",
@@ -3281,7 +3296,7 @@
         },
         "hf legic restore": {
             "command": "hf legic restore",
-            "description": "reads binary file and it autodetects card type and verifies that the file has the same size then write the data back to card. all bytes except the first 7bytes [uid(4) mcc(1) dcf(2)]",
+            "description": "reads (bin/eml/json) file and it autodetects card type and verifies that the file has the same size then write the data back to card. all bytes except the first 7bytes [uid(4) mcc(1) dcf(2)]",
             "notes": [
                 "hf legic restore -f myfile -> use user specified filename",
                 "hf legic restore -f myfile --ob -> use uid as filename and obfuscate data"
@@ -3296,18 +3311,31 @@
         },
         "hf legic sim": {
             "command": "hf legic sim",
-            "description": "simulates a legic prime tag. mim22, mim256, mim1024 types can be emulated",
+            "description": "simulates a legic prime tag. following types supported (mim22, mim256, mim1024)",
             "notes": [
-                "hf legic sim -t 0 -> simulate type mim22",
-                "hf legic sim -t 1 -> simulate type mim256 (default)",
-                "hf legic sim -t 2 -> simulate type mim1024"
+                "hf legic sim --22"
             ],
             "offline": false,
             "options": [
                 "-h, --help this help",
-                "-t, --type <dec> tag type to simulate."
+                "--22 legic prime mim22",
+                "--256 legic prime mim256 (def)",
+                "--1024 legic prime mim1024"
+            ],
+            "usage": "hf legic sim [-h] [--22] [--256] [--1024]"
+        },
+        "hf legic view": {
+            "command": "hf legic view",
+            "description": "print a legic prime dump file (bin/eml/json)",
+            "notes": [
+                "hf legic view -f hf-legic-01020304-dump.bin"
+            ],
+            "offline": true,
+            "options": [
+                "-h, --help this help",
+                "-f, --file <fn> filename of dump"
             ],
-            "usage": "hf legic sim [-h] [-t <dec>]"
+            "usage": "hf legic view [-h] -f <fn>"
         },
         "hf legic wipe": {
             "command": "hf legic wipe",
@@ -6258,8 +6286,8 @@
             "offline": false,
             "options": [
                 "-h, --help this help",
-                "-l, --lf detect low frequence 125/134 khz",
-                "-h, --hf detect high frequence 13.56 mhz"
+                "-l, --lf detect low frequency 125/134 khz",
+                "-h, --hf detect high frequency 13.56 mhz"
             ],
             "usage": "hw detectreader [-hlh]"
         },
@@ -10415,8 +10443,8 @@
         }
     },
     "metadata": {
-        "commands_extracted": 606,
+        "commands_extracted": 608,
         "extracted_by": "PM3Help2JSON v1.00",
-        "extracted_on": "2022-02-13T11:23:34"
+        "extracted_on": "2022-02-14T20:43:01"
     }
 }
\ No newline at end of file
diff --git a/doc/commands.md b/doc/commands.md
index b695ba5b5..cf1be550e 100644
--- a/doc/commands.md
+++ b/doc/commands.md
@@ -401,18 +401,20 @@ Check column "offline" for their availability.
 |command                  |offline |description
 |-------                  |------- |-----------
 |`hf legic help          `|Y       |`This help`
+|`hf legic dump          `|N       |`Dump LEGIC Prime tag to binary file`
+|`hf legic info          `|N       |`Display deobfuscated and decoded LEGIC Prime tag data`
 |`hf legic list          `|Y       |`List LEGIC history`
+|`hf legic rdbl          `|N       |`Read bytes from a LEGIC Prime tag`
 |`hf legic reader        `|N       |`LEGIC Prime Reader UID and tag info`
-|`hf legic info          `|N       |`Display deobfuscated and decoded LEGIC Prime tag data`
-|`hf legic dump          `|N       |`Dump LEGIC Prime tag to binary file`
 |`hf legic restore       `|N       |`Restore a dump file onto a LEGIC Prime tag`
-|`hf legic rdbl          `|N       |`Read bytes from a LEGIC Prime tag`
-|`hf legic sim           `|N       |`Start tag simulator`
+|`hf legic wipe          `|N       |`Wipe a LEGIC Prime tag`
 |`hf legic wrbl          `|N       |`Write data to a LEGIC Prime tag`
+|`hf legic sim           `|N       |`Start tag simulator`
+|`hf legic eload         `|N       |`Load binary dump to emulator memory`
+|`hf legic esave         `|N       |`Save emulator memory to binary file`
+|`hf legic eview         `|N       |`View emulator memory`
 |`hf legic crc           `|Y       |`Calculate Legic CRC over given bytes`
-|`hf legic eload         `|Y       |`Load binary dump to emulator memory`
-|`hf legic esave         `|Y       |`Save emulator memory to binary file`
-|`hf legic wipe          `|N       |`Wipe a LEGIC Prime tag`
+|`hf legic view          `|Y       |`Display content from tag dump file`
 
 
 ### hf lto
