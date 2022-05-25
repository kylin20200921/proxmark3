commit 3ec98d936cc9f34e27d44ca1514954e5d35008ab
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jun 20 21:39:52 2021 +0200

    textual

diff --git a/client/src/cmdlf.c b/client/src/cmdlf.c
index ef7e916b7..25b304c9e 100644
--- a/client/src/cmdlf.c
+++ b/client/src/cmdlf.c
@@ -686,9 +686,12 @@ int lf_sniff(bool verbose, uint32_t samples) {
 int CmdLFSniff(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "lf sniff",
-                  "Sniff low frequency signal.\n"
+                  "Sniff low frequency signal. You need to configure the LF part on the Proxmark3 device manually.\n"
+                  "Usually a trigger and skip samples is a good thing to set before doing a low frequency sniff.\n"
+                  "\n"
                   " - use " _YELLOW_("`lf config`") _CYAN_(" to set parameters.\n")
-                  _CYAN_(" - use ") _YELLOW_("`data plot`") _CYAN_(" to look at it"),
+                  _CYAN_(" - use ") _YELLOW_("`data plot`") _CYAN_(" to look at sniff signal.\n")
+                  _CYAN_(" - use ") _YELLOW_("`lf search -1`") _CYAN_(" to see if signal can be automatic decoded\n"),
                   "lf sniff -v\n"
                   "lf sniff -s 3000 -@    --> oscilloscope style \n"
                  );
