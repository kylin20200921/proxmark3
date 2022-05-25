commit 41c755e12b61ffca55c0df0c9f1fd0dc805da693
Author: iceman1001 <iceman@iuse.se>
Date:   Tue Jun 15 11:38:06 2021 +0200

    some lua colors

diff --git a/client/luascripts/hf_mfu_magicwrite.lua b/client/luascripts/hf_mfu_magicwrite.lua
index c01f043fe..199aec7c0 100644
--- a/client/luascripts/hf_mfu_magicwrite.lua
+++ b/client/luascripts/hf_mfu_magicwrite.lua
@@ -12,29 +12,30 @@ local err_lock = 'use -k or change cfg0 block'
 
 copyright = 'Copyright (c) 2017 IceSQL AB. All rights reserved.'
 author = 'Christian Herrmann'
-version = 'v1.1.3'
+version = 'v1.1.4'
 desc = 'This script enables easy programming of a MAGIC NTAG 21* card'
 example = [[
-    -- wipe tag
-    script run hf_mfu_magicwrite -w
-
-    -- wipe a locked down tag by giving the password
-    script run hf_mfu_magicwrite -k ffffffff -w
-
-    --read magic tag configuration
-    script run hf_mfu_magicwrite -c
+    -- read magic tag configuration
+    ]]..ansicolors.yellow..[[script run hf_mfu_magicwrite -c  ]]..ansicolors.reset..[[ 
 
     -- set uid
-    script run hf_mfu_magicwrite -u 04112233445566
+    ]]..ansicolors.yellow..[[script run hf_mfu_magicwrite -u 04112233445566 ]]..ansicolors.reset..[[ 
 
     -- set pwd / pack
-    script run hf_mfu_magicwrite -p 11223344 -a 8080
+    ]]..ansicolors.yellow..[[script run hf_mfu_magicwrite -p 11223344 -a 8080 ]]..ansicolors.reset..[[ 
 
     -- set version to NTAG213
-    script run hf_mfu_magicwrite -v 0004040201000f03
+    ]]..ansicolors.yellow..[[script run hf_mfu_magicwrite -v 0004040201000f03 ]]..ansicolors.reset..[[ 
 
     -- set signature
-    script run hf_mfu_magicwrite -s 1122334455667788990011223344556677889900112233445566778899001122
+    ]]..ansicolors.yellow..[[script run hf_mfu_magicwrite -s 1122334455667788990011223344556677889900112233445566778899001122 ]]..ansicolors.reset..[[ 
+
+    -- wipe tag
+    ]]..ansicolors.yellow..[[script run hf_mfu_magicwrite -w ]]..ansicolors.reset..[[ 
+
+    -- wipe a locked down tag by giving the password
+    ]]..ansicolors.yellow..[[script run hf_mfu_magicwrite -k ffffffff -w ]]..ansicolors.reset..[[ 
+
 ]]
 usage = [[
 script run hf_mfu_easywrite -h -k <passwd> -c -w -u <uid> -t <type> -p <passwd> -a <pack> -s <signature> -o <otp> -v <version>
