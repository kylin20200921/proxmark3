commit dca606fa0fff955b05aec28d5b6495179e24005b
Author: iceman1001 <iceman@iuse.se>
Date:   Wed Dec 22 21:51:01 2021 +0100

    adapt script to use current md5 impl etc

diff --git a/CHANGELOG.md b/CHANGELOG.md
index a7fd7f3d3..446aec9bc 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,7 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Added luascript `hf_ntag_bruteforce.lua` - ntag password bruteforce with the option to do NFC Tools MD5 versions of passwords (@keldnorman)
  - Added option `--crc-ht` to `lf cmdread` to compute and add CRC-8/HITAG (@doegox)
  - Added option `-k` to `lf cmdread` to keep field on (@doegox)
  - Added `hf mf acl` - decode and print MIFARE access rights (@iceman1001)
diff --git a/client/luascripts/hf_ntag_bruteforce.lua b/client/luascripts/hf_ntag_bruteforce.lua
index 7ab8b07a6..14d5e61f6 100644
--- a/client/luascripts/hf_ntag_bruteforce.lua
+++ b/client/luascripts/hf_ntag_bruteforce.lua
@@ -1,12 +1,9 @@
-#!/usr/bin/env lua
-os.execute("clear")
-core.console('clear')
+-- #!/usr/bin/env lua
+-- os.execute("clear")
+-- core.console('clear')
+
 local DEBUG = true
--------------------------------------------------------------------------------------------------------------
--- PRE REQ:
--------------------------------------------------------------------------------------------------------------
--- 1. Install lua rocks:             apt install luarocks
--- 2. Then install lua md5 module:   luarocks install md5
+
 -------------------------------------------------------------------------------------------------------------
 -- USAGE:
 -------------------------------------------------------------------------------------------------------------
@@ -68,7 +65,7 @@ local bruteforce_status_file = 'ntag_status.txt'
 copyright = ''
 script    = 'Script      : ntag_bruteforce.lua'
 author    = 'Author      : Keld Norman'
-version   = 'Version     : 1.0.0'
+version   = 'Version     : 1.3.0'
 -------------------------------------------------------------------------------------------------------------
 desc      = [[Description : Bruteforces 7 byte UID NTAG protected with a 32 bit password
       .-.
@@ -79,31 +76,40 @@ desc      = [[Description : Bruteforces 7 byte UID NTAG protected with a 32 bit
  /           '-'         '-'
  ]]
 -------------------------------------------------------------------------------------------------------------
-example   = [[
+example = [[
 Example of how to run the script with bruteforcing of continuously HEX numbers with 1 secound delay between tests:
- script run ntag_bruteforce -s 00000000 -e FFFFFFFF -t 1000  -o /var/log/ntag_bruteforce.log 
+
+    script run ntag_bruteforce -s 00000000 -e FFFFFFFF -t 1000  -o /var/log/ntag_bruteforce.log 
+
 Example of how to run the script and bruteforc the card using passwords from the input file with 1s delay between tests
- script run ntag_bruteforce -i /home/my_4_char_passwords_list.txt -o /var/log/ntag_bruteforce.log ]]
+
+    script run ntag_bruteforce -i /home/my_4_char_passwords_list.txt -o /var/log/ntag_bruteforce.log 
+]]
+
 -------------------------------------------------------------------------------------------------------------
-usage      = [[
+usage = [[
 script run ntag_bruteforce [-s <start_id>] [-e <end_id>] [-t <timeout>] [ -o <output_file> ] [ -p ] [ -h for help ]
 script run ntag_bruteforce [-i <input_file>] [-t <timeout>] [ -o <output_file> ] [ -n | -x ] [ -p ] [ -h for help ]
+
 DESCRIPTION 
 This script will test either an 8 digit hexadecimal code or 4 char stings (will be converted to an 8 digit hex string )
 against NFC cards of the type NTAG21x protected by a 32 bit password.
 Read more about NTAGs here: https://www.nxp.com/docs/en/data-sheet/NTAG213_215_216.pdf
+
 ]]
 -------------------------------------------------------------------------------------------------------------
-arguments   = [[
+arguments = [[
     -h                            This help
     -i       input_file           Read 4 char ASCII values to test from this file (will override -s and -e )
     -o       output_file          Write output to this file
     -t       0-99999, pause       Timeout (ms) between cards 1000 = 1 second (use the word 'pause' to wait for user input)
     -p                            Skip Ping
+
     # Either use the continuously test:
     -s       0-0xFFFFFFFF         Start HEX value
     -e       0-0xFFFFFFFF         End   HEX value
-   # Or use a list of passwords from a file: 
+
+    # Or use a list of passwords from a file: 
     -x       Passwords in HEX     Password file (-i) contains HEX values (4 x 2hex -> 32 bit/line like: 00112233)
     -n       NTAG Tools format    Bruteforce with first 8 hex values of a md5 hash of the password 
                                   The password will be prefixed with hex value 20 (space) if the string/password is < 4 chars
@@ -121,8 +127,9 @@ local function file_check(file_name)
  end
  return exists
 end
+
 -- read lines from a file
-function read_lines_from(file)
+local function read_lines_from(file)
  print(ansicolors.yellow..'\nPlease wait while loading password file..'..ansicolors.reset)
  readlines = {}
  for line in io.lines(file) do 
@@ -131,8 +138,9 @@ function read_lines_from(file)
  print(ansicolors.yellow..'\nLoading password file finished'..ansicolors.reset)
  return readlines
 end
+
 -- write to file
-function writeOutputBytes(bytes, outfile)
+local function writeOutputBytes(bytes, outfile)
  local fileout,err = io.open(outfile,"wb")
  if err then 
   print("### ERROR - Faild to open output-file "..outfile)
@@ -145,12 +153,14 @@ function writeOutputBytes(bytes, outfile)
  print("\nwrote "..#bytes.." bytes to "..outfile)
  return true
 end
+
 -- find number of entrys in a table
-function tablelength(table)
+local function tablelength(table)
  local count = 0
  for _ in pairs(table) do count = count + 1 end
  return count
 end
+
 -- debug print function
 local function dbg(args)
     if not DEBUG then return end
@@ -164,12 +174,14 @@ local function dbg(args)
         print('###', args)
     end
 end
+
 -- when errors occur
 local function oops(err)
  print(ansicolors.red..'\n### ERROR - '..err..ansicolors.reset)
  core.clearCommandBuffer()
  return nil, err
 end
+
 -- Usage help
 local function help()
  print(copyright)
@@ -184,6 +196,7 @@ local function help()
  print(ansicolors.cyan..'Example usage'..ansicolors.reset)
  print(example)
 end
+
 --- Print user message
 local function msg(msg)
  print( string.rep('--',20) )
@@ -192,8 +205,9 @@ local function msg(msg)
  print('')
  print( string.rep('--',20) )
 end
+
 -- Convert a string in to a hex string
-function convert_string_to_hex(str)
+local function convert_string_to_hex(str)
  return (
   str:gsub('.', function (c)
     return string.format('%02X', string.byte(c))
@@ -201,8 +215,9 @@ function convert_string_to_hex(str)
   )
  )
 end
+
 -- Check if string is 4 chars ascii (32 bit) = 8 chars hex
-function check_if_string_is_hex(value)
+local function check_if_string_is_hex(value)
  local patt = "%x%x%x%x%x%x%x%x"
  if string.find(value, patt) then
   return true
@@ -210,8 +225,9 @@ function check_if_string_is_hex(value)
   return false
  end
 end
+
 -- Check if number is a 0x???????? numbe (32 bit hex value)
-function check_if_number_is_hex(value)
+local function check_if_number_is_hex(value)
  local num = tonumber(value)  -- would return nil...
  if not num then
   return false
@@ -221,6 +237,7 @@ function check_if_number_is_hex(value)
  end
  return true
 end
+
 -------------------------------------------------------------------------------------------------------------
 -- MAIN FUNCTION
 -------------------------------------------------------------------------------------------------------------
@@ -233,8 +250,7 @@ local function main(args)
  -- stop if no args is given
  if #args == 0 then
   print(ansicolors.red..'\n### ERROR - Missing parameters'..ansicolors.reset)
-  help()
-  return
+  return help()
  end
 -------------------------------------------------------------------------------------------------------------
 -- Get arguments
