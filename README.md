commit 7bfb314ffa8f031a5a1eb7403588e9cb0b170ae8
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Jan 27 06:17:16 2022 +0100

    should fix the actions for ubuntu and wsl

diff --git a/.github/workflows/ubuntu.yml b/.github/workflows/ubuntu.yml
index 1cf90616b..3f558bfac 100644
--- a/.github/workflows/ubuntu.yml
+++ b/.github/workflows/ubuntu.yml
@@ -26,7 +26,7 @@ jobs:
         run: sudo apt-get update
 
       - name: Install dependencies
-        run: sudo apt-get install -yqq make autoconf build-essential ca-certificates pkg-config libreadline-dev gcc-arm-none-eabi libnewlib-dev qtbase5-dev libbz2-dev libbluetooth-dev libpython3-dev python3 python3-dev libpython3-all-dev liblua5.2-dev liblua5.2-0-dbg liblua5.2-0 lua5.2 sed
+        run: sudo apt-get install -yqq make autoconf build-essential ca-certificates pkg-config libreadline-dev gcc-arm-none-eabi libnewlib-dev qtbase5-dev libbz2-dev libbluetooth-dev libpython3-dev python3 python3-dev libpython3-all-dev liblua5.2-dev liblua5.2-0-dbg liblua5.2-0 lua5.2 sed libssl-dev
 
       - name: Install Python dependencies
         run: |
@@ -58,7 +58,7 @@ jobs:
         run: sudo apt-get update
 
       - name: Install dependencies
-        run: sudo apt-get install -yqq make autoconf build-essential ca-certificates pkg-config libreadline-dev gcc-arm-none-eabi libnewlib-dev qtbase5-dev libbz2-dev libbluetooth-dev libpython3-dev python3 python3-dev libpython3-all-dev liblua5.2-dev liblua5.2-0-dbg liblua5.2-0 lua5.2 sed
+        run: sudo apt-get install -yqq make autoconf build-essential ca-certificates pkg-config libreadline-dev gcc-arm-none-eabi libnewlib-dev qtbase5-dev libbz2-dev libbluetooth-dev libpython3-dev python3 python3-dev libpython3-all-dev liblua5.2-dev liblua5.2-0-dbg liblua5.2-0 lua5.2 sed libssl-dev
 
       - name: Install Python dependencies
         run: |
@@ -91,7 +91,7 @@ jobs:
         run: sudo apt-get update
 
       - name: Install dependencies
-        run: sudo apt-get install -yqq make autoconf build-essential ca-certificates pkg-config libreadline-dev gcc-arm-none-eabi libnewlib-dev qtbase5-dev libbz2-dev libbluetooth-dev libpython3-dev python3 python3-dev libpython3-all-dev liblua5.2-dev liblua5.2-0-dbg liblua5.2-0 lua5.2 sed
+        run: sudo apt-get install -yqq make autoconf build-essential ca-certificates pkg-config libreadline-dev gcc-arm-none-eabi libnewlib-dev qtbase5-dev libbz2-dev libbluetooth-dev libpython3-dev python3 python3-dev libpython3-all-dev liblua5.2-dev liblua5.2-0-dbg liblua5.2-0 lua5.2 sed libssl-dev
 
       - name: Install Python dependencies
         run: |
diff --git a/.github/workflows/windows.yml b/.github/workflows/windows.yml
index bb97b8523..5da0035d5 100644
--- a/.github/workflows/windows.yml
+++ b/.github/workflows/windows.yml
@@ -117,6 +117,7 @@ jobs:
             python3-pip
             python3-dev
             libpython3-all-dev
+            libssl-dev
 
       - name: Install Python dependencies
         run: |
