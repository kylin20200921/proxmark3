commit 652affbfb1460aac07c05b25cf9515e8ebb35235
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Feb 26 17:17:14 2022 +0100

    rework start screen to be shorter

diff --git a/client/src/cmdhw.c b/client/src/cmdhw.c
index 6e96b130f..ae59ad590 100644
--- a/client/src/cmdhw.c
+++ b/client/src/cmdhw.c
@@ -36,6 +36,109 @@
 
 static int CmdHelp(const char *Cmd);
 
+static void lookup_chipid_short(uint32_t iChipID, uint32_t mem_used) {
+    char asBuff[120];
+    memset(asBuff, 0, sizeof(asBuff));
+    switch (iChipID) {
+        case 0x270B0A40:
+            sprintf(asBuff, "AT91SAM7S512 Rev A");
+            break;
+        case 0x270B0A4F:
+            sprintf(asBuff, "AT91SAM7S512 Rev B");
+            break;
+        case 0x270D0940:
+            sprintf(asBuff, "AT91SAM7S256 Rev A");
+            break;
+        case 0x270B0941:
+            sprintf(asBuff, "AT91SAM7S256 Rev B");
+            break;
+        case 0x270B0942:
+            sprintf(asBuff, "AT91SAM7S256 Rev C");
+            break;
+        case 0x270B0943:
+            sprintf(asBuff, "AT91SAM7S256 Rev D");
+            break;
+        case 0x270C0740:
+            sprintf(asBuff, "AT91SAM7S128 Rev A");
+            break;
+        case 0x270A0741:
+            sprintf(asBuff, "AT91SAM7S128 Rev B");
+            break;
+        case 0x270A0742:
+            sprintf(asBuff, "AT91SAM7S128 Rev C");
+            break;
+        case 0x270A0743:
+            sprintf(asBuff, "AT91SAM7S128 Rev D");
+            break;
+        case 0x27090540:
+            sprintf(asBuff, "AT91SAM7S64 Rev A");
+            break;
+        case 0x27090543:
+            sprintf(asBuff, "AT91SAM7S64 Rev B");
+            break;
+        case 0x27090544:
+            sprintf(asBuff, "AT91SAM7S64 Rev C");
+            break;
+        case 0x27080342:
+            sprintf(asBuff, "AT91SAM7S321 Rev A");
+            break;
+        case 0x27080340:
+            sprintf(asBuff, "AT91SAM7S32 Rev A");
+            break;
+        case 0x27080341:
+            sprintf(asBuff, "AT91SAM7S32 Rev B");
+            break;
+        case 0x27050241:
+            sprintf(asBuff, "AT9SAM7S161 Rev A");
+            break;
+        case 0x27050240:
+            sprintf(asBuff, "AT91SAM7S16 Rev A");
+            break;
+    }
+    PrintAndLogEx(NORMAL, "  MCU....... " _YELLOW_("%s"), asBuff);
+
+    uint32_t mem_avail = 0;
+    switch ((iChipID & 0xF00) >> 8) {
+        case 0:
+            mem_avail = 0;
+            break;
+        case 1:
+            mem_avail = 8;
+            break;
+        case 2:
+            mem_avail = 16;
+            break;
+        case 3:
+            mem_avail = 32;
+            break;
+        case 5:
+            mem_avail = 64;
+            break;
+        case 7:
+            mem_avail = 128;
+            break;
+        case 9:
+            mem_avail = 256;
+            break;
+        case 10:
+            mem_avail = 512;
+            break;
+        case 12:
+            mem_avail = 1024;
+            break;
+        case 14:
+            mem_avail = 2048;
+            break;
+    }
+
+    PrintAndLogEx(NORMAL, "  Memory.... " _YELLOW_("%uK") " bytes ( " _YELLOW_("%2.0f%%") " used )"
+                  , mem_avail
+                  , mem_avail == 0 ? 0.0f : (float)mem_used / (mem_avail * 1024) * 100
+                 );
+
+    PrintAndLogEx(NORMAL, "");
+}
+
 static void lookupChipID(uint32_t iChipID, uint32_t mem_used) {
     char asBuff[120];
     memset(asBuff, 0, sizeof(asBuff));
@@ -921,7 +1024,6 @@ int CmdHW(const char *Cmd) {
     return CmdsParse(CommandTable, Cmd);
 }
 
-void pm3_version(bool verbose, bool oneliner) {
 
 #if defined(__MINGW64__)
 # define PM3CLIENTCOMPILER "MinGW-w64 "
@@ -975,6 +1077,96 @@ void pm3_version(bool verbose, bool oneliner) {
 # define PM3HOSTARCH "unknown"
 #endif
 
+void pm3_version_short(void) {
+    PrintAndLogEx(NORMAL, " [ " _CYAN_("Proxmark3 RFID instrument") " ]");
+    PrintAndLogEx(NORMAL, "");
+
+    if (g_session.pm3_present) {
+
+        PacketResponseNG resp;
+        clearCommandBuffer();
+        SendCommandNG(CMD_VERSION, NULL, 0);
+
+        if (WaitForResponseTimeout(CMD_VERSION, &resp, 1000)) {
+
+            struct p {
+                uint32_t id;
+                uint32_t section_size;
+                uint32_t versionstr_len;
+                char versionstr[PM3_CMD_DATA_SIZE - 12];
+            } PACKED;
+
+            struct p *payload = (struct p *)&resp.data.asBytes;
+
+            lookup_chipid_short(payload->id, payload->section_size);
+
+            // client
+            char temp[PM3_CMD_DATA_SIZE - 12]; // same limit as for ARM image
+            format_version_information_short(temp, sizeof(temp), &g_version_information);
+            PrintAndLogEx(NORMAL, "  Client.... %s", temp);
+
+            bool armsrc_mismatch = false;
+            char *ptr = strstr(payload->versionstr, " os: ");
+            if (ptr != NULL) {
+                ptr = strstr(ptr, "\n");
+                if ((ptr != NULL) && (strlen(g_version_information.armsrc) == 9)) {
+                    if (strncmp(ptr - 9, g_version_information.armsrc, 9) != 0) {
+                        armsrc_mismatch = true;
+                    }
+                }
+            }
+
+            // bootrom
+            ptr = strstr(payload->versionstr, " bootrom: ");
+            if (ptr != NULL) {
+                char *ptr_end = strstr(ptr, "\n");
+                if (ptr_end != NULL) {
+                    uint8_t len = ptr_end - 19 - ptr;
+                    PrintAndLogEx(NORMAL, "  Bootrom... %.*s", len, ptr + 10);
+                }
+            }
+
+            // os:
+            ptr = strstr(payload->versionstr, " os: ");
+            if (ptr != NULL) {
+                char *ptr_end = strstr(ptr, "\n");
+                if (ptr_end != NULL) {
+                    uint8_t len = ptr_end - 14 - ptr;
+                    PrintAndLogEx(NORMAL, "  OS........ %.*s", len, ptr + 5);
+                }
+            }
+
+
+            if (IfPm3Rdv4Fw()) {
+
+                bool is_genuine_rdv4 = false;
+                // validate signature data
+                rdv40_validation_t mem;
+                if (rdv4_get_signature(&mem) == PM3_SUCCESS) {
+                    if (rdv4_validate(&mem) == PM3_SUCCESS) {
+                        is_genuine_rdv4 = true;
+                    }
+                }
+
+                PrintAndLogEx(NORMAL, "  Target.... %s", (is_genuine_rdv4) ? _YELLOW_("RDV4") : _RED_("device / fw mismatch"));
+            } else {
+                PrintAndLogEx(NORMAL, "  Target.... %s", _YELLOW_("PM3 GENERIC"));
+            }
+
+            PrintAndLogEx(NORMAL, "");
+
+            if (armsrc_mismatch) {
+                PrintAndLogEx(NORMAL, "");
+                PrintAndLogEx(WARNING, "  --> " _RED_("ARM firmware does not match the source at the time the client was compiled"));
+                PrintAndLogEx(WARNING,  " --> Make sure to flash a correct and up-to-date version");
+            }
+        }
+    }
+    PrintAndLogEx(NORMAL, "");
+}
+
+void pm3_version(bool verbose, bool oneliner) {
+
     char temp[PM3_CMD_DATA_SIZE - 12]; // same limit as for ARM image
 
     if (oneliner) {
diff --git a/client/src/cmdhw.h b/client/src/cmdhw.h
index bda323dfc..4756a7cb0 100644
--- a/client/src/cmdhw.h
+++ b/client/src/cmdhw.h
@@ -26,5 +26,6 @@ int CmdHW(const char *Cmd);
 
 int handle_tearoff(tearoff_params_t *params, bool verbose);
 void pm3_version(bool verbose, bool oneliner);
+void pm3_version_short(void);
 
 #endif
diff --git a/client/src/proxmark3.c b/client/src/proxmark3.c
index 26062f9d7..e15c9ff9f 100644
--- a/client/src/proxmark3.c
+++ b/client/src/proxmark3.c
@@ -41,7 +41,7 @@ static int mainret = PM3_ESOFT;
 
 #ifndef LIBPM3
 #define BANNERMSG1 ""
-#define BANNERMSG2 "   [ Iceman :snowflake: ]"
+#define BANNERMSG2 "   [ :snowflake: ]"
 #define BANNERMSG3 ""
 
 typedef enum LogoMode { UTF8, ANSI, ASCII } LogoMode;
@@ -231,7 +231,7 @@ main_loop(char *script_cmds_file, char *script_cmd, bool stayInCommandLoop) {
     if (execCommand || script_cmds_file || stdinOnPipe)
         pm3_version(false, false);
     else
-        pm3_version(true, false);
+        pm3_version_short();
 
     if (script_cmds_file) {
 
diff --git a/common/commonutil.c b/common/commonutil.c
index 310578233..2101414ab 100644
--- a/common/commonutil.c
+++ b/common/commonutil.c
@@ -53,6 +53,27 @@ void FormatVersionInformation(char *dst, int len, const char *prefix, void *vers
     strncat(dst, v->armsrc, len - strlen(dst) - 1);
 }
 
+void format_version_information_short(char *dst, int len, void *version_info) {
+    struct version_information_t *v = (struct version_information_t *)version_info;
+    dst[0] = 0;
+    if (v->magic != VERSION_INFORMATION_MAGIC) {
+        strncat(dst, "Missing/Invalid version information", len - strlen(dst) - 1);
+        return;
+    }
+    if (v->versionversion != 1) {
+        strncat(dst, "Version information not understood", len - strlen(dst) - 1);
+        return;
+    }
+    if (!v->present) {
+        strncat(dst, "Version information not available", len - strlen(dst) - 1);
+        return;
+    }
+
+    strncat(dst, v->gitversion, len - strlen(dst) - 1);
+    strncat(dst, " ", len - strlen(dst) - 1);
+    strncat(dst, v->buildtime, len - strlen(dst) - 1);
+}
+
 /*
  ref  http://www.csm.ornl.gov/~dunigan/crc.html
  Returns the value v with the bottom b [0,32] bits reflected.
diff --git a/common/commonutil.h b/common/commonutil.h
index 01abdd0cb..0d8932a51 100644
--- a/common/commonutil.h
+++ b/common/commonutil.h
@@ -51,6 +51,7 @@
 
 extern struct version_information_t g_version_information;
 void FormatVersionInformation(char *dst, int len, const char *prefix, void *version_info);
+void format_version_information_short(char *dst, int len, void *version_info);
 
 uint32_t reflect(uint32_t v, int b); // used in crc.c ...
 uint8_t reflect8(uint8_t b);         // dedicated 8bit reversal
