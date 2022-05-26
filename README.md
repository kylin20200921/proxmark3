commit a932251908e15ebb75cba58ec089aa35b9d9a297
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Feb 24 23:38:51 2022 +0100

    text

diff --git a/client/src/cmdhfcipurse.c b/client/src/cmdhfcipurse.c
index 2ad4499bb..95e6e7bad 100644
--- a/client/src/cmdhfcipurse.c
+++ b/client/src/cmdhfcipurse.c
@@ -83,9 +83,9 @@ static const APDUSpcCodeDescription_t UAPDpdateKeyAttrCodeDescriptions[] = {
 
 static const APDUSpcCodeDescription_t UAPDpdateKeyCodeDescriptions[] = {
     {0x6982, "Key is frozen or only the key itself has the rights to update" },
-    {0x6984, "key enc key is blocked or invalid" },
+    {0x6984, "Enc key is blocked or invalid" },
     {0x6985, "Deactivated file" },
-    {0x6A80, "invalid algo, key length or kvv" },
+    {0x6A80, "Invalid algo, key length or kvv" },
     {0x6A88, "Invalid key number (outside the range supported by the current DF)" }
 };
 
