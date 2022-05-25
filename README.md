commit 7b61c35948ee92c5a74cdd8d758e7ccd89623874
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Jan 3 22:12:30 2022 +0100

    textual change to trace list -h

diff --git a/client/src/cmdtrace.c b/client/src/cmdtrace.c
index 75b3eff50..068588d99 100644
--- a/client/src/cmdtrace.c
+++ b/client/src/cmdtrace.c
@@ -695,22 +695,22 @@ int CmdTraceList(const char *Cmd) {
                   "Annotate trace buffer with selected protocol data\n"
                   "You can load a trace from file (see `trace load -h`) or it be downloaded from device by default\n",
                   "trace list -t raw      -> just show raw data without annotations\n"
-                  "trace list -t 14a      -> interpret as " _YELLOW_("ISO14443-A") " communications\n"
-                  "trace list -t thinfilm -> interpret as " _YELLOW_("Thinfilm") " communications\n"
-                  "trace list -t topaz    -> interpret as " _YELLOW_("Topaz") " communications\n"
-                  "trace list -t mf       -> interpret as " _YELLOW_("MIFARE Classic") " communications and decrypt crypto1 stream\n"
-                  "trace list -t des      -> interpret as " _YELLOW_("MIFARE DESFire") " communications\n"
-                  "trace list -t 14b      -> interpret as " _YELLOW_("ISO14443-B") " communications\n"
-                  "trace list -t 7816     -> interpret as " _YELLOW_("ISO7816-4") " communications\n"
-                  "trace list -t 15       -> interpret as " _YELLOW_("ISO15693") " communications\n"
-                  "trace list -t iclass   -> interpret as " _YELLOW_("iCLASS") " communications\n"
-                  "trace list -t legic    -> interpret as " _YELLOW_("LEGIC") " communications\n"
-                  "trace list -t felica   -> interpret as " _YELLOW_("ISO18092 / FeliCa") " communications\n"
-                  "trace list -t hitag1   -> interpret as " _YELLOW_("Hitag1") " communications\n"
-                  "trace list -t hitag2   -> interpret as " _YELLOW_("Hitag2") " communications\n"
-                  "trace list -t hitags   -> interpret as " _YELLOW_("HitagS") " communications\n"
-                  "trace list -t lto      -> interpret as " _YELLOW_("LTO-CM") " communications\n"
-                  "trace list -t cryptorf -> interpret as " _YELLOW_("CryptoRF") " communitcations\n"
+                  "trace list -t 14a      -> interpret as " _YELLOW_("ISO14443-A") "\n"
+                  "trace list -t thinfilm -> interpret as " _YELLOW_("Thinfilm") "\n"
+                  "trace list -t topaz    -> interpret as " _YELLOW_("Topaz") "\n"
+                  "trace list -t mf       -> interpret as " _YELLOW_("MIFARE Classic") " and decrypt crypto1 stream\n"
+                  "trace list -t des      -> interpret as " _YELLOW_("MIFARE DESFire") "\n"
+                  "trace list -t 14b      -> interpret as " _YELLOW_("ISO14443-B") "\n"
+                  "trace list -t 7816     -> interpret as " _YELLOW_("ISO7816-4") "\n"
+                  "trace list -t 15       -> interpret as " _YELLOW_("ISO15693") "\n"
+                  "trace list -t iclass   -> interpret as " _YELLOW_("iCLASS") "\n"
+                  "trace list -t legic    -> interpret as " _YELLOW_("LEGIC") "\n"
+                  "trace list -t felica   -> interpret as " _YELLOW_("ISO18092 / FeliCa") "\n"
+                  "trace list -t hitag1   -> interpret as " _YELLOW_("Hitag1") "\n"
+                  "trace list -t hitag2   -> interpret as " _YELLOW_("Hitag2") "\n"
+                  "trace list -t hitags   -> interpret as " _YELLOW_("HitagS") "\n"
+                  "trace list -t lto      -> interpret as " _YELLOW_("LTO-CM") "\n"
+                  "trace list -t cryptorf -> interpret as " _YELLOW_("CryptoRF") "\n\n"
                   "trace list -t mf --dict <mfc_default_keys>    -> use dictionary keys file\n"
                   "trace list -t 14a -f                          -> show frame delay times\n"
                   "trace list -t 14a -1                          -> use trace buffer "
@@ -726,7 +726,7 @@ int CmdTraceList(const char *Cmd) {
         arg_lit0("x", NULL, "show hexdump to convert to pcap(ng)\n"
                  "                                   or to import into Wireshark using encapsulation type \"ISO 14443\""),
         arg_str0("t", "type", NULL, "protocol to annotate the trace"),
-        arg_str0(NULL, "dict", "<file>", "use dictionary keys file"),
+        arg_str0(NULL, "dict", "<fn>", "use dictionary keys file"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, true);
