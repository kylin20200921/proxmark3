commit c5dd07fe8e9be4cff17e2e4c63c0ebb03b8de9ce
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Apr 9 17:32:57 2022 +0200

    make hint italic

diff --git a/client/src/ui/overlays.ui b/client/src/ui/overlays.ui
index 139c206f5..9650ac045 100644
--- a/client/src/ui/overlays.ui
+++ b/client/src/ui/overlays.ui
@@ -233,8 +233,13 @@
      <item>
       <widget class="QLabel" name="label_10">
        <property name="text">
-        <string>Press H in plot window to show keystrokes</string>
+        <string>Press `H` in plot window to show keystrokes</string>
        </property>
+       <property name="font">
+         <font>
+          <italic>true</italic>
+         </font>
+        </property>
       </widget>
      </item>
      <item>
