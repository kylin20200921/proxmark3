commit aed07db1f7c0c8ae6cba7be934133925be4bbb7f
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 15 17:38:40 2022 +0100

    added the possibility to show NDEF messages with vCard that has a base64 image to be viewed

diff --git a/CHANGELOG.md b/CHANGELOG.md
index af93d6654..bcdbd17dc 100644
--- a/CHANGELOG.md
+++ b/CHANGELOG.md
@@ -3,6 +3,7 @@ All notable changes to this project will be documented in this file.
 This project uses the changelog in accordance with [keepchangelog](http://keepachangelog.com/). Please use this to write notable changes, which is not the same as git commit log...
 
 ## [unreleased][unreleased]
+ - Add `nfc decode` - now NDEF vCard messages with a PHOTO in base64 format is shown (@iceman1001)
 
 ## [Frostbit.4.14831] [2022-01-11]
  - Changed Wiegand format lookup - now case-insensitive (@iceman1001)
diff --git a/client/src/guidummy.cpp b/client/src/guidummy.cpp
index 962a9c00d..e54c1d94e 100644
--- a/client/src/guidummy.cpp
+++ b/client/src/guidummy.cpp
@@ -38,6 +38,14 @@ extern "C" void ShowPictureWindow(char *fn) {
         warned = 1;
     }
 }
+extern "C" void ShowBase64PictureWindow(char *b64) {
+    static int warned = 0;
+
+    if (!warned) {
+        printf("No GUI in this build!\n");
+        warned = 1;
+    }
+}
 extern "C" void HidePictureWindow(void) {}
 extern "C" void RepaintPictureWindow(void) {}
 
diff --git a/client/src/nfc/ndef.c b/client/src/nfc/ndef.c
index cfb05cfb0..5d80963a5 100644
--- a/client/src/nfc/ndef.c
+++ b/client/src/nfc/ndef.c
@@ -22,12 +22,13 @@
 #include <stdlib.h>
 
 #include "ui.h"
-#include "util.h" // sprint_hex...
+#include "util.h"                // sprint_hex
 #include "crypto/asn1utils.h"
 #include "crypto/libpcrypto.h"
 #include "ecp.h"
-#include "commonutil.h"  // ARRAYLEN
+#include "commonutil.h"         // ARRAYLEN
 #include "pm3_cmd.h"
+#include "proxgui.h"            // Base64 Picture Window
 
 #define STRBOOL(p) ((p) ? "1" : "0")
 
@@ -520,10 +521,36 @@ static int ndefDecodeMime_wifi(NDEFHeader_t *ndef) {
 }
 
 static int ndefDecodeMime_vcard(NDEFHeader_t *ndef) {
+    if (ndef->PayloadLen == 0) {
+        PrintAndLogEx(INFO, "no payload");
+        return PM3_SUCCESS;
+    }
     PrintAndLogEx(INFO, _CYAN_("VCARD details"));
-    if (ndef->PayloadLen > 1) {
-        PrintAndLogEx(INFO, "");
-        PrintAndLogEx(INFO, "%.*s", (int)ndef->PayloadLen, ndef->Payload);
+    PrintAndLogEx(INFO, "");
+    PrintAndLogEx(INFO, "%.*s", (int)ndef->PayloadLen, ndef->Payload);
+
+    char *s = strstr((char*)ndef->Payload, "PHOTO");
+    if (s) {
+        s = strtok(s, ";");
+        while (s) {
+            char *tmp = s;
+            if (strncmp(tmp, "ENCODING", 8) == 0) {
+            } else if (strncmp(tmp, "TYPE", 4) == 0) {
+                
+                char *part = strtok(tmp + 4, ":");
+                while (part) {
+
+                    if (strncmp(part, "=image/", 7) == 0) {
+                    } else if (strncmp(part, "VCARD", 5) == 0) {
+                    } else  {
+                        // should be in the BASE64 data part now.
+                        ShowBase64PictureWindow(part);
+                    }
+                    part = strtok(NULL, ":");
+                }
+            }
+            s = strtok(NULL, ";");
+        }
     }
     return PM3_SUCCESS;
 }
diff --git a/client/src/proxgui.cpp b/client/src/proxgui.cpp
index ce7e4ccb6..37a11315d 100644
--- a/client/src/proxgui.cpp
+++ b/client/src/proxgui.cpp
@@ -81,6 +81,20 @@ extern "C" void ShowPictureWindow(char *fn) {
     gui->ShowPictureWindow(fn);
 }
 
+extern "C" void ShowBase64PictureWindow(char *b64) {
+    if (!gui) {
+        // Show a notice if X11/XQuartz isn't available
+#if defined(__MACH__) && defined(__APPLE__)
+        PrintAndLogEx(WARNING, "You appear to be on a MacOS device without XQuartz.\nYou may need to install XQuartz (https://www.xquartz.org/) to make the plot work.");
+#else
+        PrintAndLogEx(WARNING, "You appear to be on an environment without an X11 server or without DISPLAY environment variable set.\nPlot may not work until you resolve these issues.");
+#endif
+        return;
+    }
+
+    gui->ShowBase64PictureWindow(b64);
+}
+
 extern "C" void HidePictureWindow(void) {
     if (!gui)
         return;
diff --git a/client/src/proxgui.h b/client/src/proxgui.h
index 31d57822b..d38d41f1d 100644
--- a/client/src/proxgui.h
+++ b/client/src/proxgui.h
@@ -33,6 +33,7 @@ void RepaintGraphWindow(void);
 
 // hook up picture viewer
 void ShowPictureWindow(char *fn);
+void ShowBase64PictureWindow(char *b64);
 void HidePictureWindow(void);
 void RepaintPictureWindow(void);
 
diff --git a/client/src/proxguiqt.cpp b/client/src/proxguiqt.cpp
index 2d1c2a8be..0287caacc 100644
--- a/client/src/proxguiqt.cpp
+++ b/client/src/proxguiqt.cpp
@@ -69,6 +69,10 @@ void ProxGuiQT::ShowPictureWindow(char *fn) {
     emit ShowPictureWindowSignal(fn);
 }
 
+void ProxGuiQT::ShowBase64PictureWindow(char *b64) {
+    emit ShowBase64PictureWindowSignal(b64);
+}
+
 void ProxGuiQT::RepaintPictureWindow(void) {
     emit RepaintPictureWindowSignal();
 }
@@ -161,6 +165,55 @@ void ProxGuiQT::_ShowPictureWindow(char *fn) {
     pictureWidget->show();
 }
 
+void ProxGuiQT::_ShowBase64PictureWindow(char *b64) {
+
+    if (!plotapp)
+        return;
+
+    if (b64 == NULL)
+        return;
+
+    size_t slen = strlen(b64);
+    if (slen == 0)
+        return;
+
+   char *myb64data = (char *)calloc(slen + 1, sizeof(uint8_t));
+    if (myb64data == NULL)
+        return;
+
+    memcpy(myb64data, b64, slen);
+
+    if (!pictureWidget) {
+
+#if defined(__MACH__) && defined(__APPLE__)
+        makeFocusable();
+#endif
+
+        pictureWidget = new PictureWidget();
+    }
+
+    QPixmap pm;
+    if (pm.loadFromData(QByteArray::fromBase64(myb64data), "PNG") == false) {
+        qWarning("Failed to read base64 data: %s", myb64data);
+    }
+    free(myb64data);
+    //free(b64);
+
+    pictureController->lbl_pm->setPixmap(pm);
+    pictureController->lbl_pm->setScaledContents(false);
+    pictureController->lbl_pm->setAlignment(Qt::AlignCenter);
+
+    QString s = QString("w: %1  h: %2")
+                .arg(pm.size().width())
+                .arg(pm.size().height()
+                    );
+    pictureController->lbl_sz->setText(s);
+    pictureWidget->show();
+
+    if(!pm.isNull())
+        pm.save("img", "png");
+}
+
 void ProxGuiQT::_RepaintPictureWindow(void) {
     if (!plotapp || !pictureWidget)
         return;
@@ -214,6 +267,7 @@ void ProxGuiQT::MainLoop() {
 
     // hook up picture viewer signals
     connect(this, SIGNAL(ShowPictureWindowSignal(char *)), this, SLOT(_ShowPictureWindow(char *)));
+    connect(this, SIGNAL(ShowBase64PictureWindowSignal(char *)), this, SLOT(_ShowBase64PictureWindow(char *)));
     connect(this, SIGNAL(RepaintPictureWindowSignal()), this, SLOT(_RepaintPictureWindow()));
     connect(this, SIGNAL(HidePictureWindowSignal()), this, SLOT(_HidePictureWindow()));
 
diff --git a/client/src/proxguiqt.h b/client/src/proxguiqt.h
index 218e86820..20277f2fd 100644
--- a/client/src/proxguiqt.h
+++ b/client/src/proxguiqt.h
@@ -157,6 +157,7 @@ class ProxGuiQT : public QObject {
 
     // hook up picture viewer
     void ShowPictureWindow(char *fn);
+    void ShowBase64PictureWindow(char *b64);
     void HidePictureWindow(void);
     void RepaintPictureWindow(void);
 
@@ -170,6 +171,7 @@ class ProxGuiQT : public QObject {
 
     // hook up picture viewer
     void _ShowPictureWindow(char *fn);
+    void _ShowBase64PictureWindow(char *b64);
     void _HidePictureWindow(void);
     void _RepaintPictureWindow(void);
 
@@ -184,6 +186,7 @@ class ProxGuiQT : public QObject {
 
     // hook up picture viewer signals
     void ShowPictureWindowSignal(char *fn);
+    void ShowBase64PictureWindowSignal(char *b64);
     void HidePictureWindowSignal(void);
     void RepaintPictureWindowSignal(void);
 };
