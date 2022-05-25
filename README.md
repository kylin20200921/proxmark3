commit 4b0096c13520ca01548a89bfe3658749a0309eea
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jan 9 13:40:58 2022 +0100

    cppcheck complains about the dynamic_lock_Areas access. Might fix it

diff --git a/client/src/cmdhftopaz.c b/client/src/cmdhftopaz.c
index 42351ed50..b0834a4fc 100644
--- a/client/src/cmdhftopaz.c
+++ b/client/src/cmdhftopaz.c
@@ -49,8 +49,8 @@ static struct {
     uint8_t uid[7];
     uint16_t size;
     uint8_t data_blocks[TOPAZ_STATIC_MEMORY / 8][8]; // this memory is always there
-    uint8_t *dynamic_memory;                       // this memory can be there
-    dynamic_lock_area_t *dynamic_lock_areas;       // lock area descriptors
+    uint8_t *dynamic_memory;                         // this memory can be there
+    dynamic_lock_area_t *dynamic_lock_areas;         // lock area descriptors
 } topaz_tag;
 
 static void topaz_switch_on_field(void) {
@@ -201,6 +201,11 @@ static bool topaz_byte_is_locked(uint16_t byteno) {
         if (lock_area == NULL) {
             return false;
         } else {
+
+            if ((lock_area->byte_offset - TOPAZ_STATIC_MEMORY) < 0) {
+                return false;
+            }
+
             lockbits = &topaz_tag.dynamic_memory[lock_area->byte_offset - TOPAZ_STATIC_MEMORY];
             locked_bytes_per_bit = lock_area->bytes_locked_per_bit;
             byteno = byteno - lock_area->first_locked_byte;
