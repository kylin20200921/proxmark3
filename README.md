commit 0942ce7a684d3a2e2c450047da9ab73cc9f84bc6
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jan 9 13:34:54 2022 +0100

    refactory parity files a bit

diff --git a/armsrc/Makefile b/armsrc/Makefile
index a797cddb9..968dc445d 100644
--- a/armsrc/Makefile
+++ b/armsrc/Makefile
@@ -155,7 +155,6 @@ THUMBSRC = start.c \
 
 # These are to be compiled in ARM mode
 ARMSRC = fpgaloader.c \
-    parity.c \
     usb_cdc.c \
     cmd.c
 
diff --git a/client/CMakeLists.txt b/client/CMakeLists.txt
index 454bbdd7d..26d5c16bd 100644
--- a/client/CMakeLists.txt
+++ b/client/CMakeLists.txt
@@ -189,7 +189,6 @@ add_subdirectory(${PM3_ROOT}/client/deps deps)
 set (TARGET_SOURCES
         ${PM3_ROOT}/common/commonutil.c
         ${PM3_ROOT}/common/util_posix.c
-        ${PM3_ROOT}/common/parity.c
         ${PM3_ROOT}/common/bucketsort.c
         ${PM3_ROOT}/common/crapto1/crapto1.c
         ${PM3_ROOT}/common/crapto1/crypto1.c
diff --git a/client/Makefile b/client/Makefile
index 08b935919..0b74919c2 100644
--- a/client/Makefile
+++ b/client/Makefile
@@ -660,7 +660,6 @@ SRCS += bucketsort.c \
 		iso15693tools.c \
 		legic_prng.c \
 		lfdemod.c \
-		parity.c \
 		util_posix.c
 
 # swig
diff --git a/client/android/CMakeLists.txt b/client/android/CMakeLists.txt
index e8c9f9e3c..3148436ac 100644
--- a/client/android/CMakeLists.txt
+++ b/client/android/CMakeLists.txt
@@ -57,7 +57,6 @@ endif ()
 add_library(pm3rrg_rdv4 SHARED
         ${PM3_ROOT}/common/commonutil.c
         ${PM3_ROOT}/common/util_posix.c
-        ${PM3_ROOT}/common/parity.c
         ${PM3_ROOT}/common/bucketsort.c
         ${PM3_ROOT}/common/crapto1/crapto1.c
         ${PM3_ROOT}/common/crapto1/crypto1.c
diff --git a/client/experimental_lib/CMakeLists.txt b/client/experimental_lib/CMakeLists.txt
index b39ede6fe..8e0e68621 100644
--- a/client/experimental_lib/CMakeLists.txt
+++ b/client/experimental_lib/CMakeLists.txt
@@ -190,7 +190,6 @@ add_subdirectory(${PM3_ROOT}/client/deps deps)
 set (TARGET_SOURCES
         ${PM3_ROOT}/common/commonutil.c
         ${PM3_ROOT}/common/util_posix.c
-        ${PM3_ROOT}/common/parity.c
         ${PM3_ROOT}/common/bucketsort.c
         ${PM3_ROOT}/common/crapto1/crapto1.c
         ${PM3_ROOT}/common/crapto1/crypto1.c
diff --git a/client/src/cmdhffelica.c b/client/src/cmdhffelica.c
index 0f67fc8f6..6b2d3e92e 100644
--- a/client/src/cmdhffelica.c
+++ b/client/src/cmdhffelica.c
@@ -177,12 +177,6 @@ static int print_authentication2(void) {
 
 static const char *felica_model_name(uint8_t rom_type, uint8_t ic_type) {
     // source: mainly https://www.sony.net/Products/felica/business/tech-support/list.html
-
-    if (ic_type >= 0x14 && ic_type <= 0x1F) {
-        return "FeliCa Mobile IC Chip V3.0";
-    }
-
-
     switch (ic_type) {
         // FeliCa Standard Products:
         case 0x46:
@@ -239,7 +233,7 @@ static const char *felica_model_name(uint8_t rom_type, uint8_t ic_type) {
         case 0x1D:
         case 0x1E:
         case 0x1F:
-            return "Mobile FeliCa IC Chip V3.0";
+            return "FeliCa Mobile IC Chip V3.0";
         case 0x10:
         case 0x11:
         case 0x12:
diff --git a/common/parity.c b/common/parity.c
deleted file mode 100644
index cae62914b..000000000
--- a/common/parity.c
+++ /dev/null
@@ -1,57 +0,0 @@
-//-----------------------------------------------------------------------------
-// Copyright (C) Proxmark3 contributors. See AUTHORS.md for details.
-//
-// This program is free software: you can redistribute it and/or modify
-// it under the terms of the GNU General Public License as published by
-// the Free Software Foundation, either version 3 of the License, or
-// (at your option) any later version.
-//
-// This program is distributed in the hope that it will be useful,
-// but WITHOUT ANY WARRANTY; without even the implied warranty of
-// MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
-// GNU General Public License for more details.
-//
-// See LICENSE.txt for the text of the license.
-//-----------------------------------------------------------------------------
-// parity functions (all defined in parity.h)
-//-----------------------------------------------------------------------------
-#include "parity.h"
-
-const uint8_t g_OddByteParity[256] = {
-    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1,
-    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0,
-    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0,
-    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1,
-    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0,
-    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1,
-    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1,
-    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0,
-    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0,
-    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1,
-    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1,
-    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0,
-    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1,
-    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0,
-    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0,
-    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1
-};
-/*
-const uint8_t EvenByteParity[256] = {
-    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0,
-    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1,
-    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1,
-    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0,
-    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1,
-    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0,
-    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0,
-    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1,
-    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1,
-    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0,
-    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0,
-    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1,
-    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0,
-    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1,
-    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1,
-    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0
-};
-*/
diff --git a/common/parity.h b/common/parity.h
index 82ca85c9f..3694c515a 100644
--- a/common/parity.h
+++ b/common/parity.h
@@ -23,20 +23,42 @@
 
 #include "common.h"
 
-extern const uint8_t g_OddByteParity[256];
+static const uint8_t g_odd_byte_parity[256] = {
+    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1,
+    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0,
+    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0,
+    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1,
+    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0,
+    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1,
+    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1,
+    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0,
+    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0,
+    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1,
+    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1,
+    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0,
+    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1,
+    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0,
+    0, 1, 1, 0, 1, 0, 0, 1, 1, 0, 0, 1, 0, 1, 1, 0,
+    1, 0, 0, 1, 0, 1, 1, 0, 0, 1, 1, 0, 1, 0, 0, 1
+};
+
+//extern const uint8_t OddByteParity[256];
+
+#define ODD_PARITY8(x)   { g_odd_byte_parity[x] }
+#define EVEN_PARITY8(x)  { !g_odd_byte_parity[x] }
 
 static inline uint8_t oddparity8(const uint8_t x) {
-    return g_OddByteParity[x];
+    return g_odd_byte_parity[x];
 }
 
 static inline uint8_t evenparity8(const uint8_t x) {
-    return !g_OddByteParity[x];
+    return !g_odd_byte_parity[x];
 }
 
 static inline uint8_t evenparity16(uint16_t x) {
 #if !defined __GNUC__
     x ^= x >> 8;
-    return evenparity8(x);
+    return EVEN_PARITY8(x) ;
 #else
     return (__builtin_parity(x) & 0xFF);
 #endif
@@ -45,7 +67,7 @@ static inline uint8_t evenparity16(uint16_t x) {
 static inline uint8_t oddparity16(uint16_t x) {
 #if !defined __GNUC__
     x ^= x >> 8;
-    return oddparity8(x);
+    return ODD_PARITY8(x);
 #else
     return !__builtin_parity(x);
 #endif
@@ -55,7 +77,7 @@ static inline uint8_t evenparity32(uint32_t x) {
 #if !defined __GNUC__
     x ^= x >> 16;
     x ^= x >> 8;
-    return evenparity8(x);
+    return EVEN_PARITY8(x);
 #else
     return (__builtin_parity(x) & 0xFF);
 #endif
@@ -65,7 +87,7 @@ static inline uint8_t oddparity32(uint32_t x) {
 #if !defined __GNUC__
     x ^= x >> 16;
     x ^= x >> 8;
-    return oddparity8(x);
+    return ODD_PARITY8(x);
 #else
     return !__builtin_parity(x);
 #endif
