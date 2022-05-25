commit 869ae27f35bd7404725b39a7cb27df5f0dabdb4d
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Jan 6 22:11:57 2022 +0100

    cppcheck

diff --git a/common/commonutil.c b/common/commonutil.c
index e460014d7..1eee0ff23 100644
--- a/common/commonutil.c
+++ b/common/commonutil.c
@@ -129,27 +129,27 @@ uint64_t bytes_to_num(uint8_t *src, size_t len) {
     return num;
 }
 
-uint16_t MemLeToUint2byte(uint8_t *data) {
+uint16_t MemLeToUint2byte(const uint8_t *data) {
     return (data[1] << 8) + data[0];
 }
 
-uint32_t MemLeToUint3byte(uint8_t *data) {
+uint32_t MemLeToUint3byte(const uint8_t *data) {
     return (data[2] << 16) + (data[1] << 8) + data[0];
 }
 
-uint32_t MemLeToUint4byte(uint8_t *data) {
+uint32_t MemLeToUint4byte(const uint8_t *data) {
     return (data[3] << 24) + (data[2] << 16) + (data[1] << 8) + data[0];
 }
 
-uint16_t MemBeToUint2byte(uint8_t *data) {
+uint16_t MemBeToUint2byte(const uint8_t *data) {
     return (data[0] << 8) + data[1];
 }
 
-uint32_t MemBeToUint3byte(uint8_t *data) {
+uint32_t MemBeToUint3byte(const uint8_t *data) {
     return (data[0] << 16) + (data[1] << 8) + data[2];
 }
 
-uint32_t MemBeToUint4byte(uint8_t *data) {
+uint32_t MemBeToUint4byte(const uint8_t *data) {
     return (data[0] << 24) + (data[1] << 16) + (data[2] << 8) + data[3];
 }
 
@@ -207,7 +207,7 @@ void lsl(uint8_t *data, size_t len) {
 
 
 // BSWAP24 of array[3]
-uint32_t le24toh(uint8_t data[3]) {
+uint32_t le24toh(const uint8_t data[3]) {
     return (data[2] << 16) | (data[1] << 8) | data[0];
 }
 
diff --git a/common/commonutil.h b/common/commonutil.h
index 1a859e6fb..8646de3ba 100644
--- a/common/commonutil.h
+++ b/common/commonutil.h
@@ -53,12 +53,12 @@ void num_to_bytes(uint64_t n, size_t len, uint8_t *dest);
 uint64_t bytes_to_num(uint8_t *src, size_t len);
 
 // LE and BE to/from memory
-uint16_t MemLeToUint2byte(uint8_t *data);
-uint32_t MemLeToUint3byte(uint8_t *data);
-uint32_t MemLeToUint4byte(uint8_t *data);
-uint16_t MemBeToUint2byte(uint8_t *data);
-uint32_t MemBeToUint3byte(uint8_t *data);
-uint32_t MemBeToUint4byte(uint8_t *data);
+uint16_t MemLeToUint2byte(const uint8_t *data);
+uint32_t MemLeToUint3byte(const uint8_t *data);
+uint32_t MemLeToUint4byte(const uint8_t *data);
+uint16_t MemBeToUint2byte(const uint8_t *data);
+uint32_t MemBeToUint3byte(const uint8_t *data);
+uint32_t MemBeToUint4byte(const uint8_t *data);
 void Uint2byteToMemLe(uint8_t *data, uint16_t value);
 void Uint3byteToMemLe(uint8_t *data, uint32_t value);
 void Uint4byteToMemLe(uint8_t *data, uint32_t value);
@@ -69,7 +69,7 @@ void Uint4byteToMemBe(uint8_t *data, uint32_t value);
 // rotate left byte array
 void rol(uint8_t *data, const size_t len);
 void lsl(uint8_t *data, size_t len);
-uint32_t le24toh(uint8_t data[3]);
+uint32_t le24toh(const uint8_t data[3]);
 void htole24(uint32_t val, uint8_t data[3]);
 
 // rol on a u32
