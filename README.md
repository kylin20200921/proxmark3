commit 01154712a9b594c6d35e7d5babff825a96b2bc43
Author: iceman1001 <iceman@iuse.se>
Date:   Mon May 17 20:36:03 2021 +0200

    hf mfu info - search for NeXT implant known pwd

diff --git a/client/src/cmdhfmfu.c b/client/src/cmdhfmfu.c
index e9d90c0c4..85222000c 100644
--- a/client/src/cmdhfmfu.c
+++ b/client/src/cmdhfmfu.c
@@ -56,6 +56,7 @@ uint8_t default_3des_keys[][16] = {
 
 uint8_t default_pwd_pack[][4] = {
     {0xFF, 0xFF, 0xFF, 0xFF}, // PACK 0x00,0x00 -- factory default
+    {0x4E, 0x45, 0x78, 0x54},
 };
 
 uint32_t UL_TYPES_ARRAY[] = {
