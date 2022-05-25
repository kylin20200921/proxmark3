commit 3af0f4a0fde120228ce9c43fcfb2e2f862f708a5
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 22 18:37:00 2022 +0100

    no need to save the image file each time its shown

diff --git a/client/src/proxguiqt.cpp b/client/src/proxguiqt.cpp
index 0287caacc..0639fd042 100644
--- a/client/src/proxguiqt.cpp
+++ b/client/src/proxguiqt.cpp
@@ -210,8 +210,6 @@ void ProxGuiQT::_ShowBase64PictureWindow(char *b64) {
     pictureController->lbl_sz->setText(s);
     pictureWidget->show();
 
-    if(!pm.isNull())
-        pm.save("img", "png");
 }
 
 void ProxGuiQT::_RepaintPictureWindow(void) {
