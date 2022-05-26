commit 9f22131dd12dae23d827a92b8ed80bf5769721f6
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Feb 26 20:34:18 2022 +0100

    alignment

diff --git a/client/src/cmdhw.c b/client/src/cmdhw.c
index ae59ad590..28976638e 100644
--- a/client/src/cmdhw.c
+++ b/client/src/cmdhw.c
@@ -95,7 +95,7 @@ static void lookup_chipid_short(uint32_t iChipID, uint32_t mem_used) {
             sprintf(asBuff, "AT91SAM7S16 Rev A");
             break;
     }
-    PrintAndLogEx(NORMAL, "  MCU....... " _YELLOW_("%s"), asBuff);
+    PrintAndLogEx(NORMAL, "    MCU....... " _YELLOW_("%s"), asBuff);
 
     uint32_t mem_avail = 0;
     switch ((iChipID & 0xF00) >> 8) {
@@ -131,7 +131,7 @@ static void lookup_chipid_short(uint32_t iChipID, uint32_t mem_used) {
             break;
     }
 
-    PrintAndLogEx(NORMAL, "  Memory.... " _YELLOW_("%uK") " bytes ( " _YELLOW_("%2.0f%%") " used )"
+    PrintAndLogEx(NORMAL, "    Memory.... " _YELLOW_("%u") " Kb ( " _YELLOW_("%2.0f%%") " used )"
                   , mem_avail
                   , mem_avail == 0 ? 0.0f : (float)mem_used / (mem_avail * 1024) * 100
                  );
@@ -1078,7 +1078,7 @@ int CmdHW(const char *Cmd) {
 #endif
 
 void pm3_version_short(void) {
-    PrintAndLogEx(NORMAL, " [ " _CYAN_("Proxmark3 RFID instrument") " ]");
+    PrintAndLogEx(NORMAL, "  [ " _CYAN_("Proxmark3 RFID instrument") " ]");
     PrintAndLogEx(NORMAL, "");
 
     if (g_session.pm3_present) {
@@ -1103,7 +1103,7 @@ void pm3_version_short(void) {
             // client
             char temp[PM3_CMD_DATA_SIZE - 12]; // same limit as for ARM image
             format_version_information_short(temp, sizeof(temp), &g_version_information);
-            PrintAndLogEx(NORMAL, "  Client.... %s", temp);
+            PrintAndLogEx(NORMAL, "    Client.... %s", temp);
 
             bool armsrc_mismatch = false;
             char *ptr = strstr(payload->versionstr, " os: ");
@@ -1122,7 +1122,7 @@ void pm3_version_short(void) {
                 char *ptr_end = strstr(ptr, "\n");
                 if (ptr_end != NULL) {
                     uint8_t len = ptr_end - 19 - ptr;
-                    PrintAndLogEx(NORMAL, "  Bootrom... %.*s", len, ptr + 10);
+                    PrintAndLogEx(NORMAL, "    Bootrom... %.*s", len, ptr + 10);
                 }
             }
 
@@ -1132,7 +1132,7 @@ void pm3_version_short(void) {
                 char *ptr_end = strstr(ptr, "\n");
                 if (ptr_end != NULL) {
                     uint8_t len = ptr_end - 14 - ptr;
-                    PrintAndLogEx(NORMAL, "  OS........ %.*s", len, ptr + 5);
+                    PrintAndLogEx(NORMAL, "    OS........ %.*s", len, ptr + 5);
                 }
             }
 
@@ -1148,9 +1148,9 @@ void pm3_version_short(void) {
                     }
                 }
 
-                PrintAndLogEx(NORMAL, "  Target.... %s", (is_genuine_rdv4) ? _YELLOW_("RDV4") : _RED_("device / fw mismatch"));
+                PrintAndLogEx(NORMAL, "    Target.... %s", (is_genuine_rdv4) ? _YELLOW_("RDV4") : _RED_("device / fw mismatch"));
             } else {
-                PrintAndLogEx(NORMAL, "  Target.... %s", _YELLOW_("PM3 GENERIC"));
+                PrintAndLogEx(NORMAL, "    Target.... %s", _YELLOW_("PM3 GENERIC"));
             }
 
             PrintAndLogEx(NORMAL, "");
