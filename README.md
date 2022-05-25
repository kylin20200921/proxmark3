commit a85fa6dfb7444de7793154ee2f4f8ec0ac5a4d30
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Jan 1 15:46:41 2022 +0100

    mem leak fix

diff --git a/client/src/ksx6924/ksx6924core.h b/client/src/ksx6924/ksx6924core.h
index 6e9bccc74..10ffbd217 100644
--- a/client/src/ksx6924/ksx6924core.h
+++ b/client/src/ksx6924/ksx6924core.h
@@ -90,8 +90,7 @@ bool KSX6924InitializeCard(uint8_t mpda1, uint8_t mpda2, uint8_t mpda3, uint8_t
 
 // Proprietary get record command. Function unknown.
 // result must be 10 bytes long.
-bool KSX6924ProprietaryGetRecord(
-    uint8_t id, uint8_t *result, size_t resultLen);
+bool KSX6924ProprietaryGetRecord(uint8_t id, uint8_t *result, size_t result_len);
 
 #endif /* __KSX6924CORE_H__ */
 
