commit 8f32d4fc9d672b61f27091212f49a61ff7d86d8d
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Nov 19 00:44:41 2021 +0100

    textual

diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index 217d9781c..1f65a5fc8 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -1214,6 +1214,7 @@ static int CmdHF14ACmdRaw(const char *Cmd) {
                   "Sends raw bytes over ISO14443a. With option to use TOPAZ 14a mode.",
                   "hf 14a raw -sc 3000     -> select, crc, where 3000 == 'read block 00'\n"
                   "hf 14a raw -ak -b 7 40  -> send 7 bit byte 0x40\n"
+                  "hf 14a raw --ecp -s     -> send ECP before select"
                  );
 
     void *argtable[] = {
@@ -1228,8 +1229,8 @@ static int CmdHF14ACmdRaw(const char *Cmd) {
         arg_int0("t",  "timeout", "<ms>", "timeout in milliseconds"),
         arg_lit0("v",  "verbose", "Verbose output"),
         arg_lit0(NULL, "topaz", "use Topaz protocol to send command"),
-        arg_lit0(NULL, "ecp", "Use enhanced contactless polling"),
-        arg_lit0(NULL, "mag", "Use Apple magsafe polling"),
+        arg_lit0(NULL, "ecp", "use enhanced contactless polling"),
+        arg_lit0(NULL, "mag", "use Apple magsafe polling"),
         arg_strx1(NULL, NULL, "<hex>", "raw bytes to send"),
         arg_param_end
     };
