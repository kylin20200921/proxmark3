commit 0d38da5de2ad62ad8ac9961bb77da18c37df84bd
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Dec 27 19:36:42 2021 +0100

    make style

diff --git a/armsrc/Standalone/hf_craftbyte.c b/armsrc/Standalone/hf_craftbyte.c
index 2e8704e5e..d579a7de2 100644
--- a/armsrc/Standalone/hf_craftbyte.c
+++ b/armsrc/Standalone/hf_craftbyte.c
@@ -86,25 +86,25 @@ void RunMod(void) {
                 }
 
                 Dbprintf("Starting simulation, press pm3-button to stop and go back to search state.");
-                   if (card.sak == 0x08 && card.atqa[0] == 0x04 && card.atqa[1] == 0) {
-					    DbpString("Mifare Classic 1k");
-						SimulateIso14443aTag(1, flags, card.uid, 0); 
-                    } else if (card.sak == 0x08 && card.atqa[0] == 0x44 && card.atqa[1] == 0) {
-                        DbpString("Mifare Classic 4k ");
-                        SimulateIso14443aTag(8, flags, card.uid, 0);
-                    } else if (card.sak == 0x00 && card.atqa[0] == 0x44 && card.atqa[1] == 0) {
-                        DbpString("Mifare Ultralight");
-                        SimulateIso14443aTag(2, flags, card.uid, 0);
-                    } else if (card.sak == 0x20 && card.atqa[0] == 0x04 && card.atqa[1] == 0x03) {
-                        DbpString("Mifare DESFire");
-                        SimulateIso14443aTag(3, flags, card.uid, 0);
-                    } else if (card.sak == 0x20 && card.atqa[0] == 0x44 && card.atqa[1] == 0x03) {
-                        DbpString("Mifare DESFire Ev1/Plus/JCOP");
-                        SimulateIso14443aTag(3, flags, card.uid, 0);
-                    } else {
-                        Dbprintf("Unrecognized tag type -- defaulting to Mifare Classic emulation");
-                        SimulateIso14443aTag(1, flags, card.uid, 0);
-                    }    
+                if (card.sak == 0x08 && card.atqa[0] == 0x04 && card.atqa[1] == 0) {
+                    DbpString("Mifare Classic 1k");
+                    SimulateIso14443aTag(1, flags, card.uid, 0);
+                } else if (card.sak == 0x08 && card.atqa[0] == 0x44 && card.atqa[1] == 0) {
+                    DbpString("Mifare Classic 4k ");
+                    SimulateIso14443aTag(8, flags, card.uid, 0);
+                } else if (card.sak == 0x00 && card.atqa[0] == 0x44 && card.atqa[1] == 0) {
+                    DbpString("Mifare Ultralight");
+                    SimulateIso14443aTag(2, flags, card.uid, 0);
+                } else if (card.sak == 0x20 && card.atqa[0] == 0x04 && card.atqa[1] == 0x03) {
+                    DbpString("Mifare DESFire");
+                    SimulateIso14443aTag(3, flags, card.uid, 0);
+                } else if (card.sak == 0x20 && card.atqa[0] == 0x44 && card.atqa[1] == 0x03) {
+                    DbpString("Mifare DESFire Ev1/Plus/JCOP");
+                    SimulateIso14443aTag(3, flags, card.uid, 0);
+                } else {
+                    Dbprintf("Unrecognized tag type -- defaulting to Mifare Classic emulation");
+                    SimulateIso14443aTag(1, flags, card.uid, 0);
+                }
 
                 // Go back to search state if user presses pm3-button
                 state = STATE_READ;
diff --git a/armsrc/lfops.c b/armsrc/lfops.c
index 065577106..44b98ae2c 100644
--- a/armsrc/lfops.c
+++ b/armsrc/lfops.c
@@ -394,7 +394,7 @@ static bool prev_keep = false;
  */
 void ModThenAcquireRawAdcSamples125k(uint32_t delay_off, uint16_t period_0, uint16_t period_1, uint8_t *symbol_extra, uint16_t *period_extra, uint8_t *command, bool verbose, bool keep_field_on, uint32_t samples, bool ledcontrol) {
 
-    if (!prev_keep){
+    if (!prev_keep) {
         FpgaDownloadAndGo(FPGA_BITSTREAM_LF);
     }
     // use lf config settings
@@ -402,7 +402,7 @@ void ModThenAcquireRawAdcSamples125k(uint32_t delay_off, uint16_t period_0, uint
     LFSetupFPGAForADC(sc->divisor, true);
     // this causes the field to turn on for uncontrolled amount of time, so we'll turn it off
 
-    if (!prev_keep){
+    if (!prev_keep) {
 
         // Make sure the tag is reset
         FpgaWriteConfWord(FPGA_MAJOR_MODE_OFF);
diff --git a/armsrc/lfzx.c b/armsrc/lfzx.c
index f34c94d68..364af4abc 100644
--- a/armsrc/lfzx.c
+++ b/armsrc/lfzx.c
@@ -55,7 +55,7 @@ Tx = 8us = 1fc
 #define ZX_WAIT_GAP         90
 #define ZX_GAP              8    // 4 - 10
 #define ZX_T0               18
-#define ZX_T0_MIN           14   
+#define ZX_T0_MIN           14
 #define ZX_T0_MAX           22
 #define ZX_T1               28
 #define ZX_T1_MIN           26
diff --git a/armsrc/lfzx.h b/armsrc/lfzx.h
index 6b80b1c8a..3acb79543 100644
--- a/armsrc/lfzx.h
+++ b/armsrc/lfzx.h
@@ -16,4 +16,4 @@
 int zx8211_read(zx8211_data_t *zxd, bool ledcontrol);
 int zx8211_write(zx8211_data_t *zxd, bool ledcontrol);
 
-#endif
\ No newline at end of file
+#endif
diff --git a/client/luascripts/hf_ntag_bruteforce.lua b/client/luascripts/hf_ntag_bruteforce.lua
index 14d5e61f6..77ccfa408 100644
--- a/client/luascripts/hf_ntag_bruteforce.lua
+++ b/client/luascripts/hf_ntag_bruteforce.lua
@@ -24,22 +24,22 @@ local DEBUG = true
 -- SPEEDTEST
 -------------------------------------------------------------------------------------------------------------
 -- BRUTEFORCE ALL HEX COMBINATIONS:
--- 
+--
 -- With the -t 10 ( lowest possible delay ) and FFFFFFFF attempts or in decimal 4.294.967.295 combinations
--- 
+--
 -- My test showed that this script can do 255 password attempts in approxemately 170 seconds
--- 
--- That is : 255 / 170 = 1,5 attempt/second                                                                                                                                                                                      
---                                                                                                                                                                                                                               
--- So ..  4.294.967.295 combinations / 1,5 per second = 2.863.311.530 seconds and it is roughly 90 years                                                                                                                         
---                                                                                                                                                                                                                               
--------------------------------------------------------------------------------------------------------------                                                                                                                    
--- PASSWORD LISTS:                                                                                                                                                                                                               
+--
+-- That is : 255 / 170 = 1,5 attempt/second
+--
+-- So ..  4.294.967.295 combinations / 1,5 per second = 2.863.311.530 seconds and it is roughly 90 years
+--
+-------------------------------------------------------------------------------------------------------------
+-- PASSWORD LISTS:
 -------------------------------------------------------------------------------------------------------------
--- Crunch can generate all (14.776.336) combinations of 4 chars with a-z + A-Z + 0-9 like this: 
+-- Crunch can generate all (14.776.336) combinations of 4 chars with a-z + A-Z + 0-9 like this:
 --
 -- crunch 4 4 "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789" -o keys/4_chars_and_digits.list
--- 
+--
 -- for LINE in $(cat keys/4_chars_and_digits.list) ; do echo -n ${LINE} |xxd -p -u;done > keys/4_chars_and_digits_hex.list
 --
 -------------------------------------------------------------------------------------------------------------
@@ -79,11 +79,11 @@ desc      = [[Description : Bruteforces 7 byte UID NTAG protected with a 32 bit
 example = [[
 Example of how to run the script with bruteforcing of continuously HEX numbers with 1 secound delay between tests:
 
-    script run ntag_bruteforce -s 00000000 -e FFFFFFFF -t 1000  -o /var/log/ntag_bruteforce.log 
+    script run ntag_bruteforce -s 00000000 -e FFFFFFFF -t 1000  -o /var/log/ntag_bruteforce.log
 
 Example of how to run the script and bruteforc the card using passwords from the input file with 1s delay between tests
 
-    script run ntag_bruteforce -i /home/my_4_char_passwords_list.txt -o /var/log/ntag_bruteforce.log 
+    script run ntag_bruteforce -i /home/my_4_char_passwords_list.txt -o /var/log/ntag_bruteforce.log
 ]]
 
 -------------------------------------------------------------------------------------------------------------
@@ -91,7 +91,7 @@ usage = [[
 script run ntag_bruteforce [-s <start_id>] [-e <end_id>] [-t <timeout>] [ -o <output_file> ] [ -p ] [ -h for help ]
 script run ntag_bruteforce [-i <input_file>] [-t <timeout>] [ -o <output_file> ] [ -n | -x ] [ -p ] [ -h for help ]
 
-DESCRIPTION 
+DESCRIPTION
 This script will test either an 8 digit hexadecimal code or 4 char stings (will be converted to an 8 digit hex string )
 against NFC cards of the type NTAG21x protected by a 32 bit password.
 Read more about NTAGs here: https://www.nxp.com/docs/en/data-sheet/NTAG213_215_216.pdf
@@ -109,9 +109,9 @@ arguments = [[
     -s       0-0xFFFFFFFF         Start HEX value
     -e       0-0xFFFFFFFF         End   HEX value
 
-    # Or use a list of passwords from a file: 
+    # Or use a list of passwords from a file:
     -x       Passwords in HEX     Password file (-i) contains HEX values (4 x 2hex -> 32 bit/line like: 00112233)
-    -n       NTAG Tools format    Bruteforce with first 8 hex values of a md5 hash of the password 
+    -n       NTAG Tools format    Bruteforce with first 8 hex values of a md5 hash of the password
                                   The password will be prefixed with hex value 20 (space) if the string/password is < 4 chars
 ]]
 -------------------------------------------------------------------------------------------------------------
@@ -132,7 +132,7 @@ end
 local function read_lines_from(file)
  print(ansicolors.yellow..'\nPlease wait while loading password file..'..ansicolors.reset)
  readlines = {}
- for line in io.lines(file) do 
+ for line in io.lines(file) do
   readlines[#readlines + 1] = line
  end
  print(ansicolors.yellow..'\nLoading password file finished'..ansicolors.reset)
@@ -142,7 +142,7 @@ end
 -- write to file
 local function writeOutputBytes(bytes, outfile)
  local fileout,err = io.open(outfile,"wb")
- if err then 
+ if err then
   print("### ERROR - Faild to open output-file "..outfile)
   return false
  end
@@ -265,34 +265,34 @@ local function main(args)
    return help()
   end
   -- start hex value
-  if option == 's' then 
+  if option == 's' then
    start_id = argument
   end
   -- end hex value
-  if option == 'e' then 
+  if option == 'e' then
    end_id = argument
   end
   -- timeout
-  if option == 't' then 
+  if option == 't' then
    timeout = argument
   end
   -- input file
-  if option == 'i' then 
+  if option == 'i' then
    infile = argument
-   if (file_check(infile) == false) then 
-    return oops('Input file: '..infile..' not found') 
+   if (file_check(infile) == false) then
+    return oops('Input file: '..infile..' not found')
    else
     input_file_valid = true
    end
    bruteforce = false
   end
   -- skip ping
-  if option == 'p' then 
+  if option == 'p' then
    use_ping = false
   end
   -- passwordlist is hex values
-  if option == 'x' then 
-   password_is_ascii = false 
+  if option == 'x' then
+   password_is_ascii = false
    pass_text = "Passwords in file is treated as: HEX"
    bruteforce = false
   end
@@ -301,8 +301,8 @@ local function main(args)
    outfile = argument
    if (file_check(argument)) then
     local answer = utils.confirm('\nThe output-file '..argument..' already exists!\nthis will delete the previous content!\ncontinue?')
-    if (answer == false) then 
-     return oops('Quiting') 
+    if (answer == false) then
+     return oops('Quiting')
     end
    end
   end
@@ -312,21 +312,21 @@ local function main(args)
    bruteforce = false
   end
   -- help
-  if option == 'h' then 
+  if option == 'h' then
    return help()
   end
  end
  -- min timeout is set to 1 sec if it is empty
  timeout = tonumber(timeout)
- if timeout < 10 then 
-  timeout = 10 
- end 
+ if timeout < 10 then
+  timeout = 10
+ end
 -------------------------------------------------------------------------------------------------------------
 -- BRUTEFORCE
 -------------------------------------------------------------------------------------------------------------
  -- select bruteforce method
  if bruteforce then
-  if not check_if_number_is_hex(start_id) then 
+  if not check_if_number_is_hex(start_id) then
    print(ansicolors.red..'\n### ERROR - start_id value '..start_id..' is out of the range of a 32-bit integer (0 to 0xFFFFFFFF) - Did you forget to add 0x ?'..ansicolors.reset)
    return
   end
@@ -346,7 +346,7 @@ local function main(args)
    end
    local cmd = string.format( command, hexvalue )
    core.console(cmd)
-   print('[=] Tested password '..ansicolors.yellow..ansicolors.bright..string.format("%08X",hexvalue)..ansicolors.reset) 
+   print('[=] Tested password '..ansicolors.yellow..ansicolors.bright..string.format("%08X",hexvalue)..ansicolors.reset)
    print('[=] Ran command: "'..cmd..'"')
    --core.console('msleep -t'..timeout);
    if use_ping then
@@ -359,8 +359,8 @@ local function main(args)
   -- END BRUTEFORCE WITH CONTINUOUSLY HEX NUMBERS    --
   -----------------------------------------------------
  else
-  if not input_file_valid then 
-   return oops('Can not bruteforce without a password list file ( -i password_list_file.txt ) ') 
+  if not input_file_valid then
+   return oops('Can not bruteforce without a password list file ( -i password_list_file.txt ) ')
   end
   -----------------------------------------------------
   -- START BRUTEFORCE WITH PASSWORDS FROM A FILE    --
@@ -385,7 +385,7 @@ local function main(args)
    else
     if password_is_ascii then
      ------------
-     -- ASCII 
+     -- ASCII
      ------------
      if string.len(password) > 4 then
       print('[!] Skipping password to long: '..password)
diff --git a/client/src/cmdhf15.c b/client/src/cmdhf15.c
index 92db238e9..73652837b 100644
--- a/client/src/cmdhf15.c
+++ b/client/src/cmdhf15.c
@@ -1391,7 +1391,7 @@ static int CmdHF15Dump(const char *Cmd) {
             retry = 0;
             blocknum++;
 
-            PrintAndLogEx(INPLACE, "blk %3d", blocknum );
+            PrintAndLogEx(INPLACE, "blk %3d", blocknum);
         }
     }
 
diff --git a/client/src/cmdhfksx6924.c b/client/src/cmdhfksx6924.c
index edcc11ab5..05a838bb0 100644
--- a/client/src/cmdhfksx6924.c
+++ b/client/src/cmdhfksx6924.c
@@ -72,7 +72,7 @@ static int CmdHFKSX6924Balance(const char *Cmd) {
     CLIParserFree(ctx);
     SetAPDULogging(APDULogging);
 
-    if ( KSX6924TrySelect()) {
+    if (KSX6924TrySelect()) {
         get_and_print_balance();
     }
 
@@ -272,7 +272,7 @@ static int CmdHFKSX6924Initialize(const char *Cmd) {
     PrintAndLogEx(SUCCESS, "Initialize Card : Mpda -> %02X %02X %02X %02X", data[0], data[1], data[2], data[3]);
 
     uint8_t response[25] = {0};
-    if (KSX6924InitializeCard(data[0], data[1], data[2], data[3], response) ) {
+    if (KSX6924InitializeCard(data[0], data[1], data[2], data[3], response)) {
         PrintAndLogEx(SUCCESS, "Response : %s", sprint_hex(response, sizeof(response)));
     } else {
         PrintAndLogEx(FAILED, "Initialize Card Error");
diff --git a/client/src/cmdlf.c b/client/src/cmdlf.c
index 36c98c6f0..eb7190c7b 100644
--- a/client/src/cmdlf.c
+++ b/client/src/cmdlf.c
@@ -288,14 +288,14 @@ int CmdLFCommandRead(const char *Cmd) {
         uint8_t n = 0;
         crc_init_ref(&crc, 8, 0x1d, 0xff, 0, false, false);
         uint8_t i;
-        for (i=0;i<cmd_len;i++) {
+        for (i = 0; i < cmd_len; i++) {
             if ((cmd[i] != '0') && (cmd[i] != '1')) {
                 continue;
             }
             data <<= 1;
             data += cmd[i] - '0';
             n += 1;
-            if (n==8) {
+            if (n == 8) {
                 crc_update2(&crc, data, n);
                 n = 0;
                 data = 0;
@@ -305,7 +305,7 @@ int CmdLFCommandRead(const char *Cmd) {
             crc_update2(&crc, data, n);
         }
         uint8_t crc_final = crc_finish(&crc);
-        for (int j=7; j>=0; j--) {
+        for (int j = 7; j >= 0; j--) {
             cmd[cmd_len] = ((crc_final >> j) & 1) ? '1' : '0';
             cmd_len++;
         }
diff --git a/client/src/rl_vocabulory.h b/client/src/rl_vocabulory.h
index 42b64fbe3..685c9eb8f 100644
--- a/client/src/rl_vocabulory.h
+++ b/client/src/rl_vocabulory.h
@@ -216,6 +216,12 @@ const static vocabulory_t vocabulory[] = {
     { 0, "hf fido auth" }, 
     { 0, "hf fido make" }, 
     { 0, "hf fido assert" }, 
+    { 1, "hf ksx6924 help" }, 
+    { 0, "hf ksx6924 balance" }, 
+    { 0, "hf ksx6924 info" }, 
+    { 0, "hf ksx6924 initialize" }, 
+    { 0, "hf ksx6924 prec" }, 
+    { 0, "hf ksx6924 select" }, 
     { 1, "hf jooki help" }, 
     { 0, "hf jooki clone" }, 
     { 1, "hf jooki decode" }, 
@@ -635,6 +641,9 @@ const static vocabulory_t vocabulory[] = {
     { 0, "lf visa2000 reader" }, 
     { 0, "lf visa2000 clone" }, 
     { 0, "lf visa2000 sim" }, 
+    { 1, "lf zx help" }, 
+    { 1, "lf zx demod" }, 
+    { 0, "lf zx reader" }, 
     { 1, "mem help" }, 
     { 0, "mem baudrate" }, 
     { 0, "mem dump" }, 
diff --git a/common/crc.c b/common/crc.c
index 9c327f5bb..e7f169691 100644
--- a/common/crc.c
+++ b/common/crc.c
@@ -146,4 +146,4 @@ uint32_t CRC8Hitag1(uint8_t *buff, size_t size) {
         crc_update2(&crc, buff[i], 8);
     }
     return crc_finish(&crc);
-}
\ No newline at end of file
+}
diff --git a/doc/commands.json b/doc/commands.json
index c3b854e9a..299a10884 100644
--- a/doc/commands.json
+++ b/doc/commands.json
@@ -2317,7 +2317,7 @@
         },
         "hf help": {
             "command": "hf help",
-            "description": "-------- ----------------------- high frequency ----------------------- 14a { iso14443a rfids... } 14b { iso14443b rfids... } 15 { iso15693 rfids... } cipurse { cipurse transport cards... } epa { german identification card... } emrtd { machine readable travel document... } felica { iso18092 / felica rfids... } fido { fido and fido2 authenticators... } jooki { jooki rfids... } iclass { iclass rfids... } legic { legic rfids... } lto { lto cartridge memory rfids... } mf { mifare rfids... } mfp { mifare plus rfids... } mfu { mifare ultralight rfids... } mfdes { mifare desfire rfids... } seos { seos rfids... } st25ta { st25ta rfids... } thinfilm { thinfilm rfids... } topaz { topaz (nfc type 1) rfids... } waveshare { waveshare nfc epaper... } ----------- --------------------- general --------------------- help this help list list protocol data in trace buffer search search for known hf tags --------------------------------------------------------------------------------------- hf list available offline: yes alias of `trace list -t raw` with selected protocol data to annotate trace buffer you can load a trace from file (see `trace load -h`) or it be downloaded from device by default it accepts all other arguments of `trace list`. note that some might not be relevant for this specific protocol",
+            "description": "-------- ----------------------- high frequency ----------------------- 14a { iso14443a rfids... } 14b { iso14443b rfids... } 15 { iso15693 rfids... } cipurse { cipurse transport cards... } epa { german identification card... } emrtd { machine readable travel document... } felica { iso18092 / felica rfids... } fido { fido and fido2 authenticators... } ksx6924 { ks x 6924 (t-money, snapper+) rfids } jooki { jooki rfids... } iclass { iclass rfids... } legic { legic rfids... } lto { lto cartridge memory rfids... } mf { mifare rfids... } mfp { mifare plus rfids... } mfu { mifare ultralight rfids... } mfdes { mifare desfire rfids... } seos { seos rfids... } st25ta { st25ta rfids... } thinfilm { thinfilm rfids... } topaz { topaz (nfc type 1) rfids... } waveshare { waveshare nfc epaper... } ----------- --------------------- general --------------------- help this help list list protocol data in trace buffer search search for known hf tags --------------------------------------------------------------------------------------- hf list available offline: yes alias of `trace list -t raw` with selected protocol data to annotate trace buffer you can load a trace from file (see `trace load -h`) or it be downloaded from device by default it accepts all other arguments of `trace list`. note that some might not be relevant for this specific protocol",
             "notes": [
                 "hf list -f -> show frame delay times",
                 "hf list -1 -> use trace buffer"
@@ -2834,6 +2834,75 @@
             ],
             "usage": "hf jooki sim [-h] [-b <base64>]"
         },
+        "hf ksx6924 help": {
+            "command": "hf ksx6924 help",
+            "description": "help this help --------------------------------------------------------------------------------------- hf ksx6924 balance available offline: no gets the current purse balance",
+            "notes": [
+                "hf ksx6924 balance"
+            ],
+            "offline": true,
+            "options": [
+                "-h, --help this help",
+                "-k, --keep keep field on for next command",
+                "-a, --apdu show apdu reqests and responses"
+            ],
+            "usage": "hf ksx6924 balance [-hka]"
+        },
+        "hf ksx6924 info": {
+            "command": "hf ksx6924 info",
+            "description": "get info about a ks x 6924 transit card. this application is used by t-money (south korea) and snapper+ (wellington, new zealand).",
+            "notes": [
+                "hf ksx6924 info"
+            ],
+            "offline": false,
+            "options": [
+                "-h, --help this help",
+                "-k, --keep keep field on for next command",
+                "-a, --apdu show apdu reqests and responses"
+            ],
+            "usage": "hf ksx6924 info [-hka]"
+        },
+        "hf ksx6924 initialize": {
+            "command": "hf ksx6924 initialize",
+            "description": "perform transaction initialization (mpda)",
+            "notes": [
+                "hf ksx6924 initialize 000003e8 -> mpda"
+            ],
+            "offline": false,
+            "options": [
+                "-h, --help this help",
+                "-k, --keep keep field on for next command",
+                "-a, --apdu show apdu reqests and responses"
+            ],
+            "usage": "hf ksx6924 initialize [-hka] <mpda 4byte hex> [<mpda 4byte hex>]..."
+        },
+        "hf ksx6924 prec": {
+            "command": "hf ksx6924 prec",
+            "description": "executes proprietary read record command. data format is unknown. other records are available with 'emv getrec'.",
+            "notes": [
+                "hf ksx6924 prec 0b -> read proprietary record 0x0b"
+            ],
+            "offline": false,
+            "options": [
+                "-h, --help this help",
+                "-k, --keep keep field on for next command",
+                "-a, --apdu show apdu reqests and responses"
+            ],
+            "usage": "hf ksx6924 prec [-hka] <record 1byte hex> [<record 1byte hex>]..."
+        },
+        "hf ksx6924 select": {
+            "command": "hf ksx6924 select",
+            "description": "selects ks x 6924 application, and leaves field up",
+            "notes": [
+                "hf ksx6924 select"
+            ],
+            "offline": false,
+            "options": [
+                "-h, --help this help",
+                "-a, --apdu show apdu reqests and responses"
+            ],
+            "usage": "hf ksx6924 select [-ha]"
+        },
         "hf legic crc": {
             "command": "hf legic crc",
             "description": "calculates the legic crc8/crc16 on the given data",
@@ -6236,9 +6305,11 @@
                 "-z, --zero <us> zero time period",
                 "-s, --samples <dec> number of samples to collect",
                 "-v, --verbose verbose output",
+                "-k, --keep keep signal field on after receive",
+                "--crc-ht calculate and append crc-8/hitag (also for zx8211)",
                 "-@ continuous mode"
             ],
-            "usage": "lf cmdread [-hv@] [-d <us>] [-c <0|1|...>] [-e <us>]... [-o <us>] [-z <us>] [-s <dec>]"
+            "usage": "lf cmdread [-hvk@] [-d <us>] [-c <0|1|...>] [-e <us>]... [-o <us>] [-z <us>] [-s <dec>] [--crc-ht]"
         },
         "lf cotag help": {
             "command": "lf cotag help",
@@ -7099,7 +7170,7 @@
         },
         "lf help": {
             "command": "lf help",
-            "description": "help this help ----------- -------------- low frequency -------------- awid { awid rfids... } cotag { cotag chips... } destron { fdx-a destron rfids... } em { em chips & rfids... } fdxb { fdx-b rfids... } gallagher { gallagher rfids... } gproxii { guardall prox ii rfids... } hid { hid prox rfids... } hitag { hitag chips... } idteck { idteck rfids... } indala { indala rfids... } io { ioprox rfids... } jablotron { jablotron rfids... } keri { keri rfids... } motorola { motorola rfids... } nedap { nedap rfids... } nexwatch { nexwatch rfids... } noralsy { noralsy rfids... } pac { pac/stanley rfids... } paradox { paradox rfids... } pcf7931 { pcf7931 chips... } presco { presco rfids... } pyramid { farpointe/pyramid rfids... } securakey { securakey rfids... } ti { ti chips... } t55xx { t55xx chips... } viking { viking rfids... } visa2000 { visa2000 rfids... } ----------- --------------------- general --------------------- search read and search for valid known tag --------------------------------------------------------------------------------------- lf config available offline: no get/set config for lf sampling, bit/sample, decimation, frequency these changes are temporary, will be reset after a power cycle. - use `lf read` performs a read (active field) - use `lf sniff` performs a sniff (no active field)",
+            "description": "help this help ----------- -------------- low frequency -------------- awid { awid rfids... } cotag { cotag chips... } destron { fdx-a destron rfids... } em { em chips & rfids... } fdxb { fdx-b rfids... } gallagher { gallagher rfids... } gproxii { guardall prox ii rfids... } hid { hid prox rfids... } hitag { hitag chips... } idteck { idteck rfids... } indala { indala rfids... } io { ioprox rfids... } jablotron { jablotron rfids... } keri { keri rfids... } motorola { motorola rfids... } nedap { nedap rfids... } nexwatch { nexwatch rfids... } noralsy { noralsy rfids... } pac { pac/stanley rfids... } paradox { paradox rfids... } pcf7931 { pcf7931 chips... } presco { presco rfids... } pyramid { farpointe/pyramid rfids... } securakey { securakey rfids... } ti { ti chips... } t55xx { t55xx chips... } viking { viking rfids... } visa2000 { visa2000 rfids... } zx { zx8211 rfids... } ----------- --------------------- general --------------------- search read and search for valid known tag --------------------------------------------------------------------------------------- lf config available offline: no get/set config for lf sampling, bit/sample, decimation, frequency these changes are temporary, will be reset after a power cycle. - use `lf read` performs a read (active field) - use `lf sniff` performs a sniff (no active field)",
             "notes": [
                 "lf config -> shows current config",
                 "lf config -b 8 --125 -> samples at 125 khz, 8 bps",
@@ -9020,6 +9091,31 @@
             ],
             "usage": "lf visa2000 sim [-h] --cn <dec>"
         },
+        "lf zx help": {
+            "command": "lf zx help",
+            "description": "help this help demod demodulate an zx 8211 tag from the graphbuffer --------------------------------------------------------------------------------------- lf zx demod available offline: yes try to find zx8211 preamble, if found decode / descramble data",
+            "notes": [
+                "lf zx demod"
+            ],
+            "offline": true,
+            "options": [
+                "-h, --help this help"
+            ],
+            "usage": "lf zx demod [-h]"
+        },
+        "lf zx reader": {
+            "command": "lf zx reader",
+            "description": "read a zx tag",
+            "notes": [
+                "lf zx reader -@ -> continuous reader mode"
+            ],
+            "offline": false,
+            "options": [
+                "-h, --help this help",
+                "-@ optional - continuous reader mode"
+            ],
+            "usage": "lf zx reader [-h@]"
+        },
         "mem dump": {
             "command": "mem dump",
             "description": "dumps flash memory on device into a file or view in console",
@@ -10053,8 +10149,8 @@
         }
     },
     "metadata": {
-        "commands_extracted": 591,
+        "commands_extracted": 597,
         "extracted_by": "PM3Help2JSON v1.00",
-        "extracted_on": "2021-12-17T02:04:29"
+        "extracted_on": "2021-12-27T18:30:34"
     }
 }
\ No newline at end of file
diff --git a/doc/commands.md b/doc/commands.md
index 0ce8b0341..c9abff59f 100644
--- a/doc/commands.md
+++ b/doc/commands.md
@@ -317,6 +317,20 @@ Check column "offline" for their availability.
 |`hf fido assert         `|N       |`FIDO2 GetAssertion command.`
 
 
+### hf ksx6924
+
+ { KS X 6924 (T-Money, Snapper+) RFIDs }
+
+|command                  |offline |description
+|-------                  |------- |-----------
+|`hf ksx6924 help        `|Y       |`This help`
+|`hf ksx6924 balance     `|N       |`Get current purse balance`
+|`hf ksx6924 info        `|N       |`Get info about a KS X 6924 (T-Money, Snapper+) transit card`
+|`hf ksx6924 initialize  `|N       |`Perform transaction initialization (Mpda)`
+|`hf ksx6924 prec        `|N       |`Send proprietary get record command (CLA=90, INS=4C)`
+|`hf ksx6924 select      `|N       |`Select application, and leave field up`
+
+
 ### hf jooki
 
  { Jooki RFIDs...                      }
@@ -1112,6 +1126,17 @@ Check column "offline" for their availability.
 |`lf visa2000 sim        `|N       |`simulate Visa2000 tag`
 
 
+### lf zx
+
+ { ZX8211 RFIDs...            }
+
+|command                  |offline |description
+|-------                  |------- |-----------
+|`lf zx help             `|Y       |`This help`
+|`lf zx demod            `|Y       |`demodulate an ZX 8211 tag from the GraphBuffer`
+|`lf zx reader           `|N       |`attempt to read and extract tag data`
+
+
 ### mem
 
  { Flash memory manipulation... }
diff --git a/include/pm3_cmd.h b/include/pm3_cmd.h
index 5b2300d6c..88b7cb730 100644
--- a/include/pm3_cmd.h
+++ b/include/pm3_cmd.h
@@ -581,7 +581,7 @@ typedef struct {
 #define CMD_LF_T55XX_DANGERRAW                                            0x0231
 
 
-// ZX8211 
+// ZX8211
 #define CMD_LF_ZX_READ                                                    0x0270
 #define CMD_LF_ZX_WRITE                                                   0x0271
 
diff --git a/include/zx8211.h b/include/zx8211.h
index bc8fd35f9..918f8c30f 100644
--- a/include/zx8211.h
+++ b/include/zx8211.h
@@ -21,4 +21,4 @@ typedef struct {
     bool parity;
 } zx8211_data_t;
 
-#endif // ZX8211_H__ 
+#endif // ZX8211_H__
