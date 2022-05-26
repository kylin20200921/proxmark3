commit d4f5e729a1be8be6e7cd7a05524036f9ba058540
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Mar 31 23:20:26 2022 +0200

    make hf 14a apdufind less verbose in default output

diff --git a/client/src/cmdhf14a.c b/client/src/cmdhf14a.c
index 9b8458adc..44fbc4e82 100644
--- a/client/src/cmdhf14a.c
+++ b/client/src/cmdhf14a.c
@@ -2395,11 +2395,13 @@ static int CmdHf14AFindapdu(const char *Cmd) {
     param_gethex_to_eol("00a404000aa000000440000101000100", 0, aSELECT_AID, sizeof(aSELECT_AID), &aSELECT_AID_n);
     int res = ExchangeAPDU14a(aSELECT_AID, aSELECT_AID_n, true, false, response, sizeof(response), &response_n);
     if (res != PM3_SUCCESS) {
-        PrintAndLogEx(FAILED, "Tag did not respond to a test APDU (select file command). Aborting");
+        PrintAndLogEx(FAILED, "Tag did not respond to a test APDU (select file command). Aborting...");
         return res;
     }
-    PrintAndLogEx(SUCCESS, "Got response. Starting the APDU finder [ CLA " _GREEN_("%02X") " INS " _GREEN_("%02X") " P1 " _GREEN_("%02X") " P2 " _GREEN_("%02X") " ]", cla, ins, p1, p2);
+
     PrintAndLogEx(INFO, "Press " _GREEN_("<Enter>") " to exit");
+    PrintAndLogEx(NORMAL, "");
+    PrintAndLogEx(SUCCESS, "Starting the APDU finder [ CLA " _GREEN_("%02X") " INS " _GREEN_("%02X") " P1 " _GREEN_("%02X") " P2 " _GREEN_("%02X") " ]", cla, ins, p1, p2);
 
     bool inc_p1 = true;
     bool skip_ins = false;
@@ -2455,11 +2457,20 @@ retry_ins:
                         if (sw == 0x9000) {
                             log_level = SUCCESS;
                         }
-                        PrintAndLogEx(log_level, "Got response for APDU \"%s\": %04X (%s)", sprint_hex_inrow(command, command_n + i),
-                                      sw, GetAPDUCodeDescription(sw >> 8, sw & 0xff));
-                        if (response_n > 2) {
-                            PrintAndLogEx(SUCCESS, "Response data is: %s | %s", sprint_hex_inrow(response, response_n - 2),
-                                          sprint_ascii(response, response_n - 2));
+
+                        if (verbose == true || sw != 0x6e00) {
+                            PrintAndLogEx(log_level, "Got response for APDU \"%s\": %04X (%s)", 
+                                    sprint_hex_inrow(command, command_n + i),
+                                    sw,
+                                    GetAPDUCodeDescription(sw >> 8, sw & 0xff)
+                                );
+
+                            if (response_n > 2) {
+                                PrintAndLogEx(SUCCESS, "Response data is: %s | %s",
+                                    sprint_hex_inrow(response, response_n - 2),
+                                    sprint_ascii(response, response_n - 2)
+                                );
+                            }
                         }
                     }
                 }
