commit 2c4c676db2eea89fb986bccba28e50b035b48933
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Sep 18 21:44:55 2021 +0200

    Release v4.14434 - crimson

diff --git a/Makefile.defs b/Makefile.defs
index ffde9cec1..68e6f9f0e 100644
--- a/Makefile.defs
+++ b/Makefile.defs
@@ -75,8 +75,8 @@ ifeq ($(DEBUG),1)
   DEFCFLAGS = -g -O0 -fstrict-aliasing -pipe
   DEFLDFLAGS =
 else
-  DEFCXXFLAGS = -Wall -Werror -O3 -pipe
-  DEFCFLAGS = -Wall -Werror -O3 -fstrict-aliasing -pipe
+  DEFCXXFLAGS = -Wall -O3 -pipe
+  DEFCFLAGS = -Wall -O3 -fstrict-aliasing -pipe
   DEFLDFLAGS =
 endif
 
diff --git a/armsrc/Makefile b/armsrc/Makefile
index 219b176c1..e1c650505 100644
--- a/armsrc/Makefile
+++ b/armsrc/Makefile
@@ -166,7 +166,7 @@ showinfo:
 # version_pm3.c should be remade on every time fullimage.stage1.elf should be remade
 version_pm3.c: default_version_pm3.c $(OBJDIR)/fpga_version_info.o $(OBJDIR)/fpga_all.o $(THUMBOBJ) $(ARMOBJ)
 	$(info [-] GEN $@)
-	$(Q)$(SH) ../tools/mkversion.sh > $@ || $(PERL) ../tools/mkversion.pl > $@ || $(CP) $< $@
+	$(Q)$(CP) $< $@
 
 fpga_version_info.c: $(FPGA_BITSTREAMS) $(FPGA_COMPRESSOR)
 	$(info [-] GEN $@)
diff --git a/bootrom/Makefile b/bootrom/Makefile
index 255ebeb1c..9022a4eec 100644
--- a/bootrom/Makefile
+++ b/bootrom/Makefile
@@ -39,7 +39,7 @@ OBJS = $(OBJDIR)/bootrom.s19
 # version_pm3.c should be remade on every compilation
 version_pm3.c: default_version_pm3.c
 	$(info [=] GEN $@)
-	$(Q)$(SH) ../tools/mkversion.sh > $@ || $(PERL) ../tools/mkversion.pl > $@ || $(CP) $< $@
+	$(Q)$(CP) $< $@
 
 all: showinfo $(OBJS)
 
diff --git a/client/CMakeLists.txt b/client/CMakeLists.txt
index eda8e15df..1e983b205 100644
--- a/client/CMakeLists.txt
+++ b/client/CMakeLists.txt
@@ -333,7 +333,7 @@ set (TARGET_SOURCES
 
 add_custom_command(
     OUTPUT  ${CMAKE_BINARY_DIR}/version_pm3.c
-    COMMAND sh ${PM3_ROOT}/tools/mkversion.sh > ${CMAKE_BINARY_DIR}/version_pm3.c || perl ${PM3_ROOT}/tools/mkversion.pl > ${CMAKE_BINARY_DIR}/version_pm3.c || ${CMAKE_COMMAND} -E copy ${PM3_ROOT}/common/default_version_pm3.c ${CMAKE_BINARY_DIR}/version_pm3.c
+    COMMAND ${CMAKE_COMMAND} -E copy ${PM3_ROOT}/common/default_version_pm3.c ${CMAKE_BINARY_DIR}/version_pm3.c
     DEPENDS ${PM3_ROOT}/common/default_version_pm3.c
 )
 
@@ -520,7 +520,7 @@ add_executable(proxmark3
         ${ADDITIONAL_SRC}
 )
 
-target_compile_options(proxmark3 PUBLIC -Wall -Werror -O3)
+target_compile_options(proxmark3 PUBLIC -Wall -O3)
 if (EMBED_READLINE)
     if (NOT SKIPREADLINE EQUAL 1)
         add_dependencies(proxmark3 ncurses readline)
diff --git a/client/Makefile b/client/Makefile
index 5d56d2dbc..48d7760fd 100644
--- a/client/Makefile
+++ b/client/Makefile
@@ -366,7 +366,7 @@ ifeq ($(SWIG_PYTHON_FOUND),1)
     PM3CFLAGS += -DHAVE_PYTHON_SWIG
 endif
 
-CXXFLAGS ?= -Wall -Werror -O3
+CXXFLAGS ?= -Wall -O3
 PM3CXXFLAGS = $(CXXFLAGS)
 PM3CXXFLAGS += -I../include -I./include
 
@@ -822,7 +822,7 @@ src/pm3_pywrap.c: pm3.i
 # version_pm3.c should be remade on every compilation
 src/version_pm3.c: default_version_pm3.c
 	$(info [=] GEN $@)
-	$(Q)$(SH) ../tools/mkversion.sh > $@ || $(PERL) ../tools/mkversion.pl > $@ || $(CP) $< $@
+	$(Q)$(CP) $< $@
 
 # easy printing of MAKE VARIABLES
 print-%: ; @echo $* = $($*)
diff --git a/client/deps/amiibo.cmake b/client/deps/amiibo.cmake
index c946c0682..8c524c170 100644
--- a/client/deps/amiibo.cmake
+++ b/client/deps/amiibo.cmake
@@ -19,7 +19,7 @@ target_link_libraries(pm3rrg_rdv4_amiibo PRIVATE
         m
         pm3rrg_rdv4_mbedtls)
 
-target_compile_options(pm3rrg_rdv4_amiibo PRIVATE -Wall -Werror -O3)
+target_compile_options(pm3rrg_rdv4_amiibo PRIVATE -Wall -O3)
 set_property(TARGET pm3rrg_rdv4_amiibo PROPERTY POSITION_INDEPENDENT_CODE ON)
 
 target_include_directories(pm3rrg_rdv4_amiibo PRIVATE amiitool
diff --git a/client/deps/cliparser.cmake b/client/deps/cliparser.cmake
index fccae33b7..a85cc2374 100644
--- a/client/deps/cliparser.cmake
+++ b/client/deps/cliparser.cmake
@@ -9,5 +9,5 @@ target_include_directories(pm3rrg_rdv4_cliparser PRIVATE
         ../../include
         ../src)
 target_include_directories(pm3rrg_rdv4_cliparser INTERFACE cliparser)
-target_compile_options(pm3rrg_rdv4_cliparser PRIVATE -Wall -Werror -O3)
+target_compile_options(pm3rrg_rdv4_cliparser PRIVATE -Wall -O3)
 set_property(TARGET pm3rrg_rdv4_cliparser PROPERTY POSITION_INDEPENDENT_CODE ON)
diff --git a/client/deps/hardnested.cmake b/client/deps/hardnested.cmake
index dc569641c..67be099fa 100644
--- a/client/deps/hardnested.cmake
+++ b/client/deps/hardnested.cmake
@@ -2,7 +2,7 @@ add_library(pm3rrg_rdv4_hardnested_nosimd OBJECT
         hardnested/hardnested_bf_core.c
         hardnested/hardnested_bitarray_core.c)
 
-target_compile_options(pm3rrg_rdv4_hardnested_nosimd PRIVATE -Wall -Werror -O3)
+target_compile_options(pm3rrg_rdv4_hardnested_nosimd PRIVATE -Wall -O3)
 set_property(TARGET pm3rrg_rdv4_hardnested_nosimd PROPERTY POSITION_INDEPENDENT_CODE ON)
 
 target_include_directories(pm3rrg_rdv4_hardnested_nosimd PRIVATE
@@ -28,7 +28,7 @@ if ("${CMAKE_SYSTEM_PROCESSOR}" IN_LIST X86_CPUS)
             hardnested/hardnested_bf_core.c
             hardnested/hardnested_bitarray_core.c)
 
-    target_compile_options(pm3rrg_rdv4_hardnested_mmx PRIVATE -Wall -Werror -O3)
+    target_compile_options(pm3rrg_rdv4_hardnested_mmx PRIVATE -Wall -O3)
     target_compile_options(pm3rrg_rdv4_hardnested_mmx BEFORE PRIVATE
             -mmmx -mno-sse2 -mno-avx -mno-avx2 -mno-avx512f)
     set_property(TARGET pm3rrg_rdv4_hardnested_mmx PROPERTY POSITION_INDEPENDENT_CODE ON)
@@ -43,7 +43,7 @@ if ("${CMAKE_SYSTEM_PROCESSOR}" IN_LIST X86_CPUS)
             hardnested/hardnested_bf_core.c
             hardnested/hardnested_bitarray_core.c)
 
-    target_compile_options(pm3rrg_rdv4_hardnested_sse2 PRIVATE -Wall -Werror -O3)
+    target_compile_options(pm3rrg_rdv4_hardnested_sse2 PRIVATE -Wall -O3)
     target_compile_options(pm3rrg_rdv4_hardnested_sse2 BEFORE PRIVATE
             -mmmx -msse2 -mno-avx -mno-avx2 -mno-avx512f)
     set_property(TARGET pm3rrg_rdv4_hardnested_sse2 PROPERTY POSITION_INDEPENDENT_CODE ON)
@@ -58,7 +58,7 @@ if ("${CMAKE_SYSTEM_PROCESSOR}" IN_LIST X86_CPUS)
             hardnested/hardnested_bf_core.c
             hardnested/hardnested_bitarray_core.c)
 
-    target_compile_options(pm3rrg_rdv4_hardnested_avx PRIVATE -Wall -Werror -O3)
+    target_compile_options(pm3rrg_rdv4_hardnested_avx PRIVATE -Wall -O3)
     target_compile_options(pm3rrg_rdv4_hardnested_avx BEFORE PRIVATE
             -mmmx -msse2 -mavx -mno-avx2 -mno-avx512f)
     set_property(TARGET pm3rrg_rdv4_hardnested_avx PROPERTY POSITION_INDEPENDENT_CODE ON)
@@ -73,7 +73,7 @@ if ("${CMAKE_SYSTEM_PROCESSOR}" IN_LIST X86_CPUS)
             hardnested/hardnested_bf_core.c
             hardnested/hardnested_bitarray_core.c)
 
-    target_compile_options(pm3rrg_rdv4_hardnested_avx2 PRIVATE -Wall -Werror -O3)
+    target_compile_options(pm3rrg_rdv4_hardnested_avx2 PRIVATE -Wall -O3)
     target_compile_options(pm3rrg_rdv4_hardnested_avx2 BEFORE PRIVATE
             -mmmx -msse2 -mavx -mavx2 -mno-avx512f)
     set_property(TARGET pm3rrg_rdv4_hardnested_avx2 PROPERTY POSITION_INDEPENDENT_CODE ON)
@@ -88,7 +88,7 @@ if ("${CMAKE_SYSTEM_PROCESSOR}" IN_LIST X86_CPUS)
             hardnested/hardnested_bf_core.c
             hardnested/hardnested_bitarray_core.c)
 
-    target_compile_options(pm3rrg_rdv4_hardnested_avx512 PRIVATE -Wall -Werror -O3)
+    target_compile_options(pm3rrg_rdv4_hardnested_avx512 PRIVATE -Wall -O3)
     target_compile_options(pm3rrg_rdv4_hardnested_avx512 BEFORE PRIVATE
             -mmmx -msse2 -mavx -mavx2 -mavx512f)
     set_property(TARGET pm3rrg_rdv4_hardnested_avx512 PROPERTY POSITION_INDEPENDENT_CODE ON)
@@ -113,7 +113,7 @@ add_library(pm3rrg_rdv4_hardnested STATIC
         hardnested/hardnested_bruteforce.c
         $<TARGET_OBJECTS:pm3rrg_rdv4_hardnested_nosimd>
         ${SIMD_TARGETS})
-target_compile_options(pm3rrg_rdv4_hardnested PRIVATE -Wall -Werror -O3)
+target_compile_options(pm3rrg_rdv4_hardnested PRIVATE -Wall -O3)
 set_property(TARGET pm3rrg_rdv4_hardnested PROPERTY POSITION_INDEPENDENT_CODE ON)
 target_include_directories(pm3rrg_rdv4_hardnested PRIVATE
         ../../common
diff --git a/client/deps/jansson.cmake b/client/deps/jansson.cmake
index c91a47047..42c701d5e 100644
--- a/client/deps/jansson.cmake
+++ b/client/deps/jansson.cmake
@@ -14,5 +14,5 @@ add_library(pm3rrg_rdv4_jansson STATIC
 
 target_compile_definitions(pm3rrg_rdv4_jansson PRIVATE HAVE_STDINT_H)
 target_include_directories(pm3rrg_rdv4_jansson INTERFACE jansson)
-target_compile_options(pm3rrg_rdv4_jansson PRIVATE -Wall -Werror -Wno-unused-function -O3)
+target_compile_options(pm3rrg_rdv4_jansson PRIVATE -Wall -Wno-unused-function -O3)
 set_property(TARGET pm3rrg_rdv4_jansson PROPERTY POSITION_INDEPENDENT_CODE ON)
diff --git a/client/deps/lua.cmake b/client/deps/lua.cmake
index 12870342e..5cf33d724 100644
--- a/client/deps/lua.cmake
+++ b/client/deps/lua.cmake
@@ -52,5 +52,5 @@ if (NOT MINGW)
 endif (NOT MINGW)
 
 target_include_directories(pm3rrg_rdv4_lua INTERFACE liblua)
-target_compile_options(pm3rrg_rdv4_lua PRIVATE -Wall -Werror -O3)
+target_compile_options(pm3rrg_rdv4_lua PRIVATE -Wall -O3)
 set_property(TARGET pm3rrg_rdv4_lua PROPERTY POSITION_INDEPENDENT_CODE ON)
diff --git a/client/deps/mbedtls.cmake b/client/deps/mbedtls.cmake
index 40929e1ea..c726ddeba 100644
--- a/client/deps/mbedtls.cmake
+++ b/client/deps/mbedtls.cmake
@@ -44,5 +44,5 @@ add_library(pm3rrg_rdv4_mbedtls STATIC
 
 target_include_directories(pm3rrg_rdv4_mbedtls PRIVATE ../../common)
 target_include_directories(pm3rrg_rdv4_mbedtls INTERFACE ../../common/mbedtls)
-target_compile_options(pm3rrg_rdv4_mbedtls PRIVATE -Wall -Werror -O3)
+target_compile_options(pm3rrg_rdv4_mbedtls PRIVATE -Wall -O3)
 set_property(TARGET pm3rrg_rdv4_mbedtls PROPERTY POSITION_INDEPENDENT_CODE ON)
diff --git a/client/deps/reveng.cmake b/client/deps/reveng.cmake
index d7e3cfd8a..1040730f1 100644
--- a/client/deps/reveng.cmake
+++ b/client/deps/reveng.cmake
@@ -13,5 +13,5 @@ target_include_directories(pm3rrg_rdv4_reveng PRIVATE
         ../src
         ../../include)
 target_include_directories(pm3rrg_rdv4_reveng INTERFACE reveng)
-target_compile_options(pm3rrg_rdv4_reveng PRIVATE -Wall -Werror -O3)
+target_compile_options(pm3rrg_rdv4_reveng PRIVATE -Wall -O3)
 set_property(TARGET pm3rrg_rdv4_reveng PROPERTY POSITION_INDEPENDENT_CODE ON)
diff --git a/client/deps/tinycbor.cmake b/client/deps/tinycbor.cmake
index 5a6abda25..c74618149 100644
--- a/client/deps/tinycbor.cmake
+++ b/client/deps/tinycbor.cmake
@@ -11,5 +11,5 @@ add_library(pm3rrg_rdv4_tinycbor STATIC
 
 target_include_directories(pm3rrg_rdv4_tinycbor INTERFACE tinycbor)
 # Strange errors on Mingw when compiling with -O3
-target_compile_options(pm3rrg_rdv4_tinycbor PRIVATE -Wall -Werror -O2)
+target_compile_options(pm3rrg_rdv4_tinycbor PRIVATE -Wall -O2)
 set_property(TARGET pm3rrg_rdv4_tinycbor PROPERTY POSITION_INDEPENDENT_CODE ON)
diff --git a/client/deps/whereami.cmake b/client/deps/whereami.cmake
index d2d6a5b2a..721873066 100644
--- a/client/deps/whereami.cmake
+++ b/client/deps/whereami.cmake
@@ -2,5 +2,5 @@ add_library(pm3rrg_rdv4_whereami STATIC whereami/whereami.c)
 
 target_compile_definitions(pm3rrg_rdv4_whereami PRIVATE WAI_PM3_TUNED)
 target_include_directories(pm3rrg_rdv4_whereami INTERFACE whereami)
-target_compile_options(pm3rrg_rdv4_whereami PRIVATE -Wall -Werror -O3)
+target_compile_options(pm3rrg_rdv4_whereami PRIVATE -Wall -O3)
 set_property(TARGET pm3rrg_rdv4_whereami PROPERTY POSITION_INDEPENDENT_CODE ON)
diff --git a/client/src/proxmark3.c b/client/src/proxmark3.c
index 93446104a..5de8f190c 100644
--- a/client/src/proxmark3.c
+++ b/client/src/proxmark3.c
@@ -38,7 +38,7 @@
 #ifndef LIBPM3
 #define BANNERMSG1 ""
 #define BANNERMSG2 "   [ :snowflake: Iceman :snowflake: ]"
-#define BANNERMSG3 ""
+#define BANNERMSG3 "Release v4.14434 - crimson"
 
 typedef enum LogoMode { UTF8, ANSI, ASCII } LogoMode;
 
diff --git a/common/default_version_pm3.c b/common/default_version_pm3.c
index 1f3f32415..ea42c2bbf 100644
--- a/common/default_version_pm3.c
+++ b/common/default_version_pm3.c
@@ -1,5 +1,5 @@
 #include "common.h"
-/* This is the default version_pm3.c file that Makefile.common falls back to if neither sh nor perl are available */
+/* Generated file, do not edit */
 #ifndef ON_DEVICE
 #define SECTVERSINFO
 #else
@@ -8,8 +8,9 @@
 
 const struct version_information_t SECTVERSINFO g_version_information = {
     VERSION_INFORMATION_MAGIC,
-    1, /* version 1 */
-    0, /* version information not present */
-    2, /* cleanliness couldn't be determined */
-    /* Remaining fields: zero */
+    1,
+    1,
+    1,
+    "RRG/Iceman/master/v4.14434",
+    "2021-09-18 21:44:55",
 };
diff --git a/common_arm/Makefile.common b/common_arm/Makefile.common
index f5fad1401..fc86c5750 100644
--- a/common_arm/Makefile.common
+++ b/common_arm/Makefile.common
@@ -40,7 +40,7 @@ VPATH = . ../common_arm ../common ../common/crapto1 ../common/mbedtls ../common/
 INCLUDES = ../include/proxmark3_arm.h ../include/at91sam7s512.h ../include/config_gpio.h ../include/pm3_cmd.h
 
 ARMCFLAGS = -mthumb-interwork -fno-builtin
-DEFCFLAGS = -Wall -Werror -Os -pedantic -fstrict-aliasing -pipe
+DEFCFLAGS = -Wall -Os -pedantic -fstrict-aliasing -pipe
 
 # Some more warnings we want as errors:
 DEFCFLAGS += -Wbad-function-cast -Wchar-subscripts -Wundef -Wunused -Wuninitialized -Wpointer-arith -Wformat -Wformat-security -Winit-self -Wmissing-include-dirs -Wnested-externs -Wempty-body -Wignored-qualifiers -Wmissing-field-initializers -Wtype-limits
