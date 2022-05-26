commit c7cb449eb619dcfb6da2cb03e21c7d63d7106cdc
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Feb 27 12:34:34 2022 +0100

    coreutils dependency

diff --git a/.github/workflows/macos.yml b/.github/workflows/macos.yml
index 6a9dd07b0..1c0d23011 100644
--- a/.github/workflows/macos.yml
+++ b/.github/workflows/macos.yml
@@ -35,7 +35,7 @@ jobs:
         run: brew tap RfidResearchGroup/proxmark3
 
       - name: Install dependencies
-        run: brew install readline qt5 RfidResearchGroup/proxmark3/arm-none-eabi-gcc openssl
+        run: brew install readline coreutils qt5 RfidResearchGroup/proxmark3/arm-none-eabi-gcc openssl
 
       - name: Fix linkings for qt5 and openssl
         working-directory: /usr/local/include
