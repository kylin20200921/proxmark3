commit 638e8920882f3461ec60f1ee8405c47cfff11f2f
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Feb 18 18:23:53 2022 +0100

    added device side option to disable EAS/AFI password.  Sets to all ZEROS

diff --git a/armsrc/appmain.c b/armsrc/appmain.c
index afe10694c..5176b7367 100644
--- a/armsrc/appmain.c
+++ b/armsrc/appmain.c
@@ -1271,6 +1271,14 @@ static void PacketReceived(PacketCommandNG *packet) {
             DisablePrivacySlixLIso15693(payload->pwd);
             break;
         }
+        case CMD_HF_ISO15693_SLIX_L_DISABLE_AESAFI: {
+            struct p {
+                uint8_t pwd[4];
+            } PACKED;
+            struct p *payload = (struct p *) packet->data.asBytes;
+            DisableEAS_AFISlixLIso15693(payload->pwd);
+            break;
+        }
 
 #endif
 
diff --git a/armsrc/iso15693.c b/armsrc/iso15693.c
index c89bc6699..aa3929d73 100644
--- a/armsrc/iso15693.c
+++ b/armsrc/iso15693.c
@@ -2248,15 +2248,36 @@ static uint32_t destroy_15693_slixl(uint32_t start_time, uint32_t *eof_time, uin
 }
 
 */
+
+// Sets a PRIVACY password to all ZEROS
 void DisablePrivacySlixLIso15693(uint8_t *password) {
     LED_D_ON();
     Iso15693InitReader();
     StartCountSspClk();
     uint32_t start_time = 0, eof_time = 0;
-    // 4 == pass id.
+
+    // Password identifier Password byte
+    // 0x04  Privacy
+    // 0x08  Destroy SLIX-L
+    // 0x10  EAS/AFI
     int res = set_pass_15693_slixl(start_time, &eof_time, 0x04, password);
     reply_ng(CMD_HF_ISO15693_SLIX_L_DISABLE_PRIVACY, res, NULL, 0);
     switch_off();
 }
 
+// Sets a EAS/AFI password to all ZEROS
+void DisableEAS_AFISlixLIso15693(uint8_t *password) {
+    LED_D_ON();
+    Iso15693InitReader();
+    StartCountSspClk();
+    uint32_t start_time = 0, eof_time = 0;
+
+    // Password identifier Password byte
+    // 0x04  Privacy
+    // 0x08  Destroy SLIX-L
+    // 0x10  EAS/AFI
+    int res = set_pass_15693_slixl(start_time, &eof_time, 0x10, password);
+    reply_ng(CMD_HF_ISO15693_SLIX_L_DISABLE_AESAFI, res, NULL, 0);
+    switch_off();
+}
 
diff --git a/armsrc/iso15693.h b/armsrc/iso15693.h
index b4f634fff..29d266709 100644
--- a/armsrc/iso15693.h
+++ b/armsrc/iso15693.h
@@ -60,4 +60,5 @@ int SendDataTagEOF(uint8_t *recv, uint16_t max_recv_len, uint32_t start_time, ui
 void SetTag15693Uid(const uint8_t *uid);
 
 void DisablePrivacySlixLIso15693(uint8_t *password);
+void DisableEAS_AFISlixLIso15693(uint8_t *password);
 #endif
diff --git a/include/pm3_cmd.h b/include/pm3_cmd.h
index 94eb4d9c0..c97124cc0 100644
--- a/include/pm3_cmd.h
+++ b/include/pm3_cmd.h
@@ -519,6 +519,7 @@ typedef struct {
 #define CMD_HF_ISO15693_FINDAFI                                           0x0315
 #define CMD_HF_ISO15693_CSETUID                                           0x0316
 #define CMD_HF_ISO15693_SLIX_L_DISABLE_PRIVACY                            0x0317
+#define CMD_HF_ISO15693_SLIX_L_DISABLE_AESAFI                             0x0318
 
 #define CMD_LF_SNIFF_RAW_ADC                                              0x0360
 
