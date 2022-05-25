commit 293cd21e28040d31d1b138e28c37d88bc81262fb
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Jun 21 09:03:46 2021 +0200

    textual

diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index ba9cb97c0..b08de964c 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -2577,9 +2577,9 @@ static int CmdHF14AMfChk_fast(const char *Cmd) {
                   "hf mf fchk --2k -k FFFFFFFFFFFF                --> Key recovery against MIFARE 2k\n"
                   "hf mf fchk --4k -k FFFFFFFFFFFF                --> Key recovery against MIFARE 4k\n"
                   "hf mf fchk --1k -f mfc_default_keys.dic        --> Target 1K using default dictionary file\n"
-                  "hf mf fchk --1k --emu                          --> Target 1K, write to emulator memory\n"
-                  "hf mf fchk --1k --dump                         --> Target 1K, write to file\n"
-                  "hf mf fchk --1k --mem                          --> Target 1K, use dictionary from flashmemory");
+                  "hf mf fchk --1k --emu                          --> Target 1K, write keys to emulator memory\n"
+                  "hf mf fchk --1k --dump                         --> Target 1K, write keys to file\n"
+                  "hf mf fchk --1k --mem                          --> Target 1K, use dictionary from flash memory");
 
     void *argtable[] = {
         arg_param_begin,
