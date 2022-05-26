commit 507d5a43ba5b0d302b7052d3aebbe331b617b0f4
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Mar 19 20:23:11 2022 +0100

    new param to view command to print all keys nicely and also print all found value blocks,  if any

diff --git a/client/src/cmdhfmf.c b/client/src/cmdhfmf.c
index aebdd7ef5..001031e19 100644
--- a/client/src/cmdhfmf.c
+++ b/client/src/cmdhfmf.c
@@ -219,6 +219,23 @@ static char GetFormatFromSector(uint8_t sectors) {
     }
 }
 
+static bool mfc_value(const uint8_t *d, uint32_t *val) {
+    // values
+    uint32_t a = MemLeToUint4byte(d);
+    uint32_t a_inv = MemLeToUint4byte(d + 4);
+    uint32_t b = MemLeToUint4byte(d + 8);
+
+    int val_checks = (
+                         (a == b) && (a == ~a_inv) &&
+                         (d[12] == (~d[13] & 0xFF)) &&
+                         (d[14] == (~d[15] & 0xFF))
+                     );
+
+    if (val) {
+        *val = a;
+    }
+    return (val_checks);
+}
 static void mf_print_block(uint8_t blockno, uint8_t *d) {
     if (blockno == 0) {
         PrintAndLogEx(INFO, "%3d | " _RED_("%s"), blockno, sprint_hex_ascii(d, MFBLOCK_SIZE));
@@ -241,6 +258,64 @@ static void mf_print_blocks(uint16_t n, uint8_t *d) {
     PrintAndLogEx(NORMAL, "");
 }
 
+static int mf_print_keys(uint16_t n, uint8_t *d) {
+
+    uint8_t sectors = 0;
+    switch(n) {
+        case MIFARE_MINI_MAXBLOCK:
+            sectors = MIFARE_MINI_MAXSECTOR;
+            break;
+        case MIFARE_2K_MAXBLOCK:
+            sectors = MIFARE_2K_MAXSECTOR;
+            break;
+        case MIFARE_4K_MAXBLOCK:
+            sectors = MIFARE_4K_MAXSECTOR;
+            break;        
+        case MIFARE_1K_MAXBLOCK:
+        default:
+            sectors = MIFARE_1K_MAXSECTOR;
+            break;
+    }
+
+    sector_t *e_sector = calloc(sectors, sizeof(sector_t));
+    if (e_sector == NULL)  {
+        return PM3_EMALLOC;
+    }
+
+    for (uint16_t i = 0; i < n; i++) {
+        if (mfIsSectorTrailer(i)) {
+            e_sector[mfSectorNum(i)].foundKey[0] = 1;
+            e_sector[mfSectorNum(i)].Key[0] = bytes_to_num(d + (i * MFBLOCK_SIZE), 6);
+            e_sector[mfSectorNum(i)].foundKey[1] = 1;
+            e_sector[mfSectorNum(i)].Key[1] = bytes_to_num(d + (i * MFBLOCK_SIZE) + 10, 6);
+        }
+    }
+    printKeyTable(sectors, e_sector);
+    free(e_sector);
+    return PM3_SUCCESS;    
+}
+
+static void mf_print_values(uint16_t n, uint8_t *d) {
+
+    PrintAndLogEx(NORMAL, "");
+    PrintAndLogEx(INFO, "Looking for value blocks...");
+    PrintAndLogEx(NORMAL, "");
+    uint8_t cnt = 0;
+    uint32_t value = 0;
+    for (uint16_t i = 0; i < n; i++) {
+    
+        if (mfc_value(d + (i * MFBLOCK_SIZE), &value))  {
+            PrintAndLogEx(INFO, "%03d | " _YELLOW_("%" PRIu32) " " _YELLOW_("0x%" PRIX32), i, value, value);
+            ++cnt;
+        }
+    }
+
+    if (cnt) {
+        PrintAndLogEx(INFO, "Found %u value blocks in file", cnt);        
+        PrintAndLogEx(NORMAL, "");
+    }
+}
+
 static void mf_print_sector_hdr(uint8_t sector) {
     PrintAndLogEx(NORMAL, "");
     PrintAndLogEx(INFO, "  # | sector " _GREEN_("%02d") " / " _GREEN_("0x%02X") "                                | ascii", sector, sector);
@@ -3544,22 +3619,22 @@ void printKeyTableEx(uint8_t sectorscnt, sector_t *e_sector, uint8_t start_secto
     char strA[12 + 1] = {0};
     char strB[12 + 1] = {0};
     PrintAndLogEx(NORMAL, "");
-    PrintAndLogEx(SUCCESS, "|-----|-----|----------------|---|----------------|---|");
-    PrintAndLogEx(SUCCESS, "| Sec | Blk | key A          |res| key B          |res|");
-    PrintAndLogEx(SUCCESS, "|-----|-----|----------------|---|----------------|---|");
+    PrintAndLogEx(SUCCESS, "-----+-----+--------------+---+--------------+----");
+    PrintAndLogEx(SUCCESS, " Sec | Blk | key A        |res| key B        |res");
+    PrintAndLogEx(SUCCESS, "-----+-----+--------------+---+--------------+----");
     for (uint8_t i = 0; i < sectorscnt; i++) {
 
         snprintf(strA, sizeof(strA), "------------");
         snprintf(strB, sizeof(strB), "------------");
 
         if (e_sector[i].foundKey[0])
-            snprintf(strA, sizeof(strA), "%012" PRIx64, e_sector[i].Key[0]);
+            snprintf(strA, sizeof(strA), "%012" PRIX64, e_sector[i].Key[0]);
 
         if (e_sector[i].foundKey[1])
-            snprintf(strB, sizeof(strB), "%012" PRIx64, e_sector[i].Key[1]);
+            snprintf(strB, sizeof(strB), "%012" PRIX64, e_sector[i].Key[1]);
 
         if (e_sector[i].foundKey[0] > 1) {
-            PrintAndLogEx(SUCCESS, "| "_YELLOW_("%03d")" | "_YELLOW_("%03d")" | " _GREEN_("%s")"   | " _YELLOW_("%c")" | " _GREEN_("%s")"   | " _YELLOW_("%c")" |"
+            PrintAndLogEx(SUCCESS, " "_YELLOW_("%03d")" | %03d | " _GREEN_("%s")" | " _YELLOW_("%c")" | " _GREEN_("%s")" | " _YELLOW_("%c") 
                           , i
                           , mfSectorTrailerOfSector(i)
                           , strA, e_sector[i].foundKey[0]
@@ -3572,7 +3647,7 @@ void printKeyTableEx(uint8_t sectorscnt, sector_t *e_sector, uint8_t start_secto
             if (start_sector == 0)
                 s = i;
 
-            PrintAndLogEx(SUCCESS, "| "_YELLOW_("%03d")" | "_YELLOW_("%03d")" | " _GREEN_("%s")"   | " _YELLOW_("%d")" | " _GREEN_("%s")"   | " _YELLOW_("%d")" |"
+            PrintAndLogEx(SUCCESS, " "_YELLOW_("%03d")" | %03d | " _GREEN_("%s")" | " _YELLOW_("%d")" | " _GREEN_("%s")" | " _YELLOW_("%d") 
                           , s
                           , mfSectorTrailerOfSector(s)
                           , strA, e_sector[i].foundKey[0]
@@ -3580,8 +3655,7 @@ void printKeyTableEx(uint8_t sectorscnt, sector_t *e_sector, uint8_t start_secto
                          );
         }
     }
-    PrintAndLogEx(SUCCESS, "|-----|-----|----------------|---|----------------|---|");
-
+    PrintAndLogEx(SUCCESS, "-----+-----+--------------+---+--------------+----");
     if (e_sector[0].foundKey[0] > 1) {
         PrintAndLogEx(INFO, "( "
                       _YELLOW_("D") ":Dictionary / "
@@ -6083,6 +6157,12 @@ static int CmdHF14AMfView(const char *Cmd) {
     }
 
     mf_print_blocks(block_cnt, dump);
+
+    if (verbose) {
+        mf_print_keys(block_cnt, dump);
+        mf_print_values(block_cnt, dump);
+    }
+
     free(dump);
     return PM3_SUCCESS;
 }
@@ -6201,24 +6281,6 @@ static int CmdHF14AGen4View(const char *Cmd) {
     return PM3_SUCCESS;
 }
 
-static bool mfc_value(const uint8_t *d, uint32_t *val) {
-    // values
-    uint32_t a = MemLeToUint4byte(d);
-    uint32_t a_inv = MemLeToUint4byte(d + 4);
-    uint32_t b = MemLeToUint4byte(d + 8);
-
-    int val_checks = (
-                         (a == b) && (a == ~a_inv) &&
-                         (d[12] == (~d[13] & 0xFF)) &&
-                         (d[14] == (~d[15] & 0xFF))
-                     );
-
-    if (val) {
-        *val = a;
-    }
-    return (val_checks);
-}
-
 static int CmdHF14AMfValue(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "hf mf value",
@@ -6237,7 +6299,6 @@ static int CmdHF14AMfValue(const char *Cmd) {
     CLIGetHexWithReturn(ctx, 1, data, &dlen);
     CLIParserFree(ctx);
 
-
     uint32_t value = 0;
 
     if (mfc_value(data, &value))  {
