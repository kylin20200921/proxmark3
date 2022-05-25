commit f1501c96984199653189f5dfb6d86e9c160f9514
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Jan 27 06:20:57 2022 +0100

    should fix actions builds fro OSX

diff --git a/.github/workflows/macos.yml b/.github/workflows/macos.yml
index 7d973926c..950b85cc4 100644
--- a/.github/workflows/macos.yml
+++ b/.github/workflows/macos.yml
@@ -35,7 +35,7 @@ jobs:
         run: brew tap RfidResearchGroup/proxmark3
 
       - name: Install dependencies
-        run: brew install readline qt5 RfidResearchGroup/proxmark3/arm-none-eabi-gcc
+        run: brew install readline qt5 RfidResearchGroup/proxmark3/arm-none-eabi-gcc openssl
 
       - name: Install Python dependencies
         run: |
@@ -76,7 +76,7 @@ jobs:
         run: brew tap RfidResearchGroup/proxmark3
 
       - name: Install dependencies
-        run: brew install readline qt5 RfidResearchGroup/proxmark3/arm-none-eabi-gcc
+        run: brew install readline qt5 RfidResearchGroup/proxmark3/arm-none-eabi-gcc openssl
 
       - name: Install Python dependencies
         run: |
@@ -118,7 +118,7 @@ jobs:
         run: brew tap RfidResearchGroup/proxmark3
 
       - name: Install dependencies
-        run: brew install readline qt5 RfidResearchGroup/proxmark3/arm-none-eabi-gcc
+        run: brew install readline qt5 RfidResearchGroup/proxmark3/arm-none-eabi-gcc openssl
 
       - name: Install Python dependencies
         run: |
