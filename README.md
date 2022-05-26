commit 549779d9881894d5690a87ab642906f7bdb0e69b
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Feb 13 12:19:06 2022 +0100

    textual

diff --git a/client/luascripts/hf_ntag_bruteforce.lua b/client/luascripts/hf_ntag_bruteforce.lua
index 77ccfa408..e7a347560 100644
--- a/client/luascripts/hf_ntag_bruteforce.lua
+++ b/client/luascripts/hf_ntag_bruteforce.lua
@@ -337,7 +337,7 @@ local function main(args)
   -----------------------------------------------------
   -- START BRUTEFORCE WITH CONTINUOUSLY HEX NUMBERS  --
   -----------------------------------------------------
-  command = 'hf mfu i -k %08X'
+  command = 'hf mfu info -k %08X'
   msg('Bruteforcing NTAG Passwords\n\nStart value: '..start_id..'\nStop value : '..end_id..'\nDelay between tests: '..timeout..' ms')
   for hexvalue = start_id, end_id do
    if core.kbd_enter_pressed() then -- abort if key is pressed
@@ -409,7 +409,7 @@ local function main(args)
     end
    end
    if skip_to_next == 0 then
-   command = 'hf mfu i -k %4s'
+   command = 'hf mfu info -k %4s'
     local cmd = string.format( command, password )
     core.console(cmd)
     if lines[counter] ~= password then -- show hex value (if not the password was a hex value already)
diff --git a/doc/cheatsheet.md b/doc/cheatsheet.md
index ae41a4745..a1f04faf1 100644
--- a/doc/cheatsheet.md
+++ b/doc/cheatsheet.md
@@ -377,7 +377,7 @@ pm3 --> hf mfu info
 Clone MIFARE Ultralight EV1 Sequence
 ```
 pm3 --> hf mfu dump -k FFFFFFFF
-pm3 --> hf mfu eload -u -f hf-mfu-XXXX-dump.bin
+pm3 --> hf mfu eload -f hf-mfu-XXXX-dump.bin
 pm3 --> hf mfu sim -t 7
 ```
 
diff --git a/tools/pm3_amii_bin2eml.pl b/tools/pm3_amii_bin2eml.pl
index 2e96ddd42..7ac3d7322 100755
--- a/tools/pm3_amii_bin2eml.pl
+++ b/tools/pm3_amii_bin2eml.pl
@@ -6,7 +6,7 @@
 #
 # -samy kamkar 05/28/2017
 #
-# hf mf eload --ul FILENAME_MINUS_EML
+# hf mf eload -f FILENAME_MINUS_EML
 # hf 14a sim -t 7 -u UID
 
 # perl -lne 'chomp; s/\s+(\S+)$//;$f=$1;if($f=~s/-(\S+)//){$g=hex($1);}else{$g=hex($f)}$f=hex($f); for$m($f..$g){print "0x" . substr(unpack("H4",pack("n",$m)),1) ." => \"$_\","}' /tmp/game  >> game2
@@ -684,7 +684,7 @@ print STDERR "\n";
 $uid = uc $uid;
 #print STDERR "amiitool -d -k ../client/amiitool/key_retail.bin -i $input -o $input.decrypted\n";
 $input =~ s/\....$//;
-print STDERR "hf mfu eload --u $input\n";
+print STDERR "hf mfu eload -f $input\n";
 print STDERR "hf 14a sim -t 7 -u $uid\n";
 
 
