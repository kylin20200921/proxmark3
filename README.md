commit 2551f9e0aabc7a0be412b55746ce6f3c45a0add1
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 1 11:29:47 2022 +0100

    simplify

diff --git a/armsrc/em4x50.c b/armsrc/em4x50.c
index 53b96ae64..8c64baed7 100644
--- a/armsrc/em4x50.c
+++ b/armsrc/em4x50.c
@@ -682,8 +682,10 @@ void em4x50_login(uint32_t *password, bool ledcontrol) {
     int status = PM3_EFAILED;
     if (ledcontrol) LED_C_ON();
     if (get_signalproperties() && find_em4x50_tag()) {
-        if (ledcontrol) LED_C_OFF();
-        if (ledcontrol) LED_D_ON();
+        if (ledcontrol) {
+            LED_C_OFF();
+            LED_D_ON();
+        }
         status = login(*password);
     }
 
@@ -700,8 +702,10 @@ void em4x50_brute(em4x50_data_t *etd, bool ledcontrol) {
     uint32_t pwd = 0x0;
     if (ledcontrol) LED_C_ON();
     if (get_signalproperties() && find_em4x50_tag()) {
-        if (ledcontrol) LED_C_OFF();
-        if (ledcontrol) LED_D_ON();
+        if (ledcontrol) {
+            LED_C_OFF();
+            LED_D_ON();
+        }
         bsuccess = brute(etd->password1, etd->password2, &pwd);
     }
 
@@ -736,8 +740,10 @@ void em4x50_chk(uint8_t *filename, bool ledcontrol) {
     if (ledcontrol) LED_C_ON();
     if (get_signalproperties() && find_em4x50_tag()) {
 
-        if (ledcontrol) LED_C_OFF();
-        if (ledcontrol) LED_D_ON();
+        if (ledcontrol) {
+            LED_C_OFF();
+            LED_D_ON();
+        }
 
         // try to login with current password
         for (int i = 0; i < pwd_count; i++) {
@@ -859,8 +865,10 @@ void em4x50_read(em4x50_data_t *etd, bool ledcontrol) {
     if (ledcontrol) LED_C_ON();
     if (get_signalproperties() && find_em4x50_tag()) {
 
-        if (ledcontrol) LED_C_OFF();
-        if (ledcontrol) LED_D_ON();
+        if (ledcontrol) {
+            LED_C_OFF();
+            LED_D_ON();
+        }
 
         bool blogin = true;
 
@@ -888,8 +896,10 @@ void em4x50_info(em4x50_data_t *etd, bool ledcontrol) {
 
     if (ledcontrol) LED_C_ON();
     if (get_signalproperties() && find_em4x50_tag()) {
-        if (ledcontrol) LED_C_OFF();
-        if (ledcontrol) LED_D_ON();
+        if (ledcontrol) {
+            LED_C_OFF();
+            LED_D_ON();
+        }
 
         bool blogin = true;
         // login with given password
@@ -917,8 +927,10 @@ void em4x50_reader(bool ledcontrol) {
 
     if (ledcontrol) LED_C_ON();
     if (get_signalproperties() && find_em4x50_tag()) {
-        if (ledcontrol) LED_C_OFF();
-        if (ledcontrol) LED_D_ON();
+        if (ledcontrol) {
+            LED_C_OFF();
+            LED_D_ON();
+        }
         standard_read(&now, words);
     }
 
@@ -1032,8 +1044,10 @@ void em4x50_write(em4x50_data_t *etd, bool ledcontrol) {
     if (ledcontrol) LED_C_ON();
     if (get_signalproperties() && find_em4x50_tag()) {
 
-        if (ledcontrol) LED_C_OFF();
-        if (ledcontrol) LED_D_ON();
+        if (ledcontrol) {
+            LED_C_OFF();
+            LED_D_ON();
+        }
 
         // if password is given try to login first
         status = PM3_SUCCESS;
@@ -1089,8 +1103,10 @@ void em4x50_writepwd(em4x50_data_t *etd, bool ledcontrol) {
     if (ledcontrol) LED_C_ON();
     if (get_signalproperties() && find_em4x50_tag()) {
 
-        if (ledcontrol) LED_C_OFF();
-        if (ledcontrol) LED_D_ON();
+        if (ledcontrol) {
+            LED_C_OFF();
+            LED_D_ON();
+        }
 
         // login and change password
         if (login(etd->password1) == PM3_SUCCESS) {
