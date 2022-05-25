commit 644da79a5093399bca67872e9acea09694017fd0
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Oct 23 23:53:59 2021 +0200

    add legic crc16 to lua

diff --git a/client/lualibs/utils.lua b/client/lualibs/utils.lua
index 65fdbc5ad..00103d6b4 100644
--- a/client/lualibs/utils.lua
+++ b/client/lualibs/utils.lua
@@ -122,7 +122,7 @@ local Utils =
         return nil
     end,
 
-    ------------ CRC-8 Legic checksums
+    ------------ CRC-8 Legic checksum
     -- Takes a hex string and calculates a crc8
     Crc8Legic = function(s)
         if s == nil then return nil end
@@ -130,13 +130,29 @@ local Utils =
         if  type(s) == 'string' then
             local utils = require('utils')
             local asc = utils.ConvertHexToAscii(s)
-            local hash = core.crc8legic(asc)
-            return hash
+            return core.crc8legic(asc)
         end
         return nil
     end,
+    ------------ CRC-16 Legic checksum
+    -- Takes data as hex string, uid hex and calculates a crc16 legic
+    Crc16Legic = function(s, uid)
+        if s == nil then return nil end
+        if #s == 0 then return nil end
+        if uid == nil then return nil end
+        if #uid == 0 then return nil end
+
+        if  type(s) == 'string' then
+            local utils = require('utils')
+            local asc = utils.ConvertHexToAscii(s)
+            local uidstr = utils.ConvertHexToAscii(uid)
+            return core.crc16legic(asc, uidstr)
+        end
+        return nil
+    end,
+
 
-    ------------ CRC-16 ccitt checksums
+    ------------ CRC-16 ccitt checksum
     -- Takes a hex string and calculates a crc16
     Crc16 = function(s)
         if s == nil then return nil end
@@ -151,7 +167,7 @@ local Utils =
     end,
 
 
-    ------------ CRC-64 ecma checksums
+    ------------ CRC-64 ecma checksum
     -- Takes a hex string and calculates a crc64 ecma hash
     Crc64 = function(s)
         if s == nil then return nil end
@@ -164,7 +180,7 @@ local Utils =
         end
         return nil
     end,
-    ------------ CRC-64 ecma 182 checksums
+    ------------ CRC-64 ecma 182 checksum
     -- Takes a hex string and calculates a crc64 ecma182 hash
     Crc64_ecma182 = function(s)
         if s == nil then return nil end
diff --git a/client/src/scripting.c b/client/src/scripting.c
index 95b56e0c8..4b3ccd2f9 100644
--- a/client/src/scripting.c
+++ b/client/src/scripting.c
@@ -683,13 +683,29 @@ static int l_aes128encrypt_ecb(lua_State *L) {
 
 static int l_crc8legic(lua_State *L) {
     size_t size;
-    const char *p_str = luaL_checklstring(L, 1, &size);
+    const char *p_hexstr = luaL_checklstring(L, 1, &size);
+    uint16_t retval = CRC8Legic((uint8_t *)p_hexstr, size);
+    lua_pushunsigned(L, retval);
+    return 1;
+}
 
-    uint16_t retval = CRC8Legic((uint8_t *) p_str, size);
+static int l_crc16legic(lua_State *L) {
+    size_t hexsize, uidsize;
+
+    // data as hex string
+    const char *p_hexstr = luaL_checklstring(L, 1, &hexsize);
+
+    // calc uid crc based on uid hex
+    const char *p_uid = luaL_checklstring(L, 2, &uidsize);
+    uint16_t uidcrc = CRC8Legic((uint8_t *)p_uid, uidsize);
+
+    init_table(CRC_LEGIC_16);
+    uint16_t retval = crc16_legic((uint8_t *)p_hexstr, hexsize, uidcrc);
     lua_pushunsigned(L, retval);
     return 1;
 }
 
+
 static int l_crc16(lua_State *L) {
     size_t size;
     const char *p_str = luaL_checklstring(L, 1, &size);
@@ -1359,6 +1375,7 @@ int set_pm3_libraries(lua_State *L) {
         {"aes128_encrypt",              l_aes128encrypt_cbc},
         {"aes128_encrypt_ecb",          l_aes128encrypt_ecb},
         {"crc8legic",                   l_crc8legic},
+        {"crc16legic",                  l_crc16legic},
         {"crc16",                       l_crc16},
         {"crc64",                       l_crc64},
         {"crc64_ecma182",               l_crc64_ecma182},
diff --git a/common/crc16.c b/common/crc16.c
index d1573b7d0..e61cf1d58 100644
--- a/common/crc16.c
+++ b/common/crc16.c
@@ -41,6 +41,9 @@ void init_table(CrcType_t crctype) {
         case CRC_LEGIC:
             generate_table(CRC16_POLY_LEGIC, true);
             break;
+        case CRC_LEGIC_16:
+            generate_table(CRC16_POLY_LEGIC_16, true);
+            break;
         case CRC_CCITT:
             generate_table(CRC16_POLY_CCITT, false);
             break;
@@ -194,6 +197,7 @@ void compute_crc(CrcType_t ct, const uint8_t *d, size_t n, uint8_t *first, uint8
             crc = crc16_fdxb(d, n);
             break;
         case CRC_LEGIC:
+        case CRC_LEGIC_16:
             // TODO
             return;
         case CRC_NONE:
@@ -227,6 +231,7 @@ uint16_t Crc16ex(CrcType_t ct, const uint8_t *d, size_t n) {
         case CRC_11784:
             return crc16_fdxb(d, n);
         case CRC_LEGIC:
+        case CRC_LEGIC_16:
             // TODO
             return 0;
         case CRC_NONE:
@@ -272,6 +277,7 @@ bool check_crc(CrcType_t ct, const uint8_t *d, size_t n) {
         case CRC_11784:
             return (crc16_fdxb(d, n) == 0);
         case CRC_LEGIC:
+        case CRC_LEGIC_16:
             // TODO
             return false;
         case CRC_NONE:
@@ -330,7 +336,7 @@ uint16_t crc16_iclass(uint8_t const *d, size_t n) {
 // This CRC-16 is used in Legic Advant systems.
 // poly=0xB400,  init=depends  refin=true  refout=true  xorout=0x0000  check=  name="CRC-16/LEGIC"
 uint16_t crc16_legic(uint8_t const *d, size_t n, uint8_t uidcrc) {
-    uint16_t initial = uidcrc << 8 | uidcrc;
-    return crc16_fast(d, n, initial, true, true);
+    uint16_t initial = (uidcrc << 8 | uidcrc);
+    return crc16_fast(d, n, initial, true, false);
 }
 
diff --git a/common/crc16.h b/common/crc16.h
index fba4ea72f..abf403629 100644
--- a/common/crc16.h
+++ b/common/crc16.h
@@ -10,10 +10,11 @@
 
 #include "common.h"
 
-#define CRC16_POLY_CCITT   0x1021
-#define CRC16_POLY_KERMIT  0x8408
-#define CRC16_POLY_LEGIC   0xc6c6 //0x6363
-#define CRC16_POLY_DNP     0x3d65
+#define CRC16_POLY_CCITT     0x1021
+#define CRC16_POLY_KERMIT    0x8408
+#define CRC16_POLY_LEGIC     0xc6c6 //0x6363
+#define CRC16_POLY_LEGIC_16  0x002d
+#define CRC16_POLY_DNP       0x3d65
 
 #define X25_CRC_CHECK     ((uint16_t)(~0xF0B8 & 0xFFFF)) // use this for checking of a correct crc
 
@@ -26,6 +27,7 @@ typedef enum {
     CRC_ICLASS,
     CRC_FELICA,
     CRC_LEGIC,
+    CRC_LEGIC_16,
     CRC_CCITT,
     CRC_KERMIT,
     CRC_XMODEM,
