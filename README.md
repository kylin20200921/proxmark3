commit bff057d9774370128822ffea65e8fd966247eaa9
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Oct 23 18:57:50 2021 +0200

    style

diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index 9417d7337..217d9781c 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -421,7 +421,7 @@ static int CmdHF14AReader(const char *Cmd) {
                   "hf 14a reader -@     -> Continuous mode\n"
                   "hf 14a reader --ecp  -> trigger apple enhanced contactless polling\n"
                   "hf 14a reader --mag  -> trigger apple magsafe polling\n"
-                  );
+                 );
 
     void *argtable[] = {
         arg_param_begin,
@@ -1308,13 +1308,13 @@ static int CmdHF14ACmdRaw(const char *Cmd) {
         flags |= ISO14A_NO_RATS;
     }
 
-    if (use_ecp){
+    if (use_ecp) {
         flags |= ISO14A_USE_ECP;
     }
 
-    if (use_magsafe){
+    if (use_magsafe) {
         flags |= ISO14A_USE_MAGSAFE;
-    }    
+    }
 
     // Max buffer is PM3_CMD_DATA_SIZE
     datalen = (datalen > PM3_CMD_DATA_SIZE) ? PM3_CMD_DATA_SIZE : datalen;
diff --git a/include/protocols.h b/include/protocols.h
index 55d0874d7..f70a84850 100644
--- a/include/protocols.h
+++ b/include/protocols.h
@@ -143,7 +143,7 @@ ISO 7816-4 Basic interindustry commands. For command APDU's.
 #define ICLASS_DEBIT(x)             (((x) & 0x80) == 0x80)
 
 
-// U7bit 
+// 7bit Apple Magsafe wake up command
 #define MAGSAFE_CMD_WUPA            0x7A
 
 #define ISO14443A_CMD_REQA          0x26
