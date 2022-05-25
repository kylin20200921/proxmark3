commit 0a4c9f83accdf36ba9f047db7c9ea8a8fd9c6271
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Jan 3 22:08:55 2022 +0100

    fixes to hf iclass configcard mem setup,  textual output,  MIX->NG convertion

diff --git a/armsrc/appmain.c b/armsrc/appmain.c
index ef95ba3e2..1e0a214fd 100644
--- a/armsrc/appmain.c
+++ b/armsrc/appmain.c
@@ -23,6 +23,7 @@
 #include "printf.h"
 #include "legicrf.h"
 #include "BigBuf.h"
+#include "iclass_cmd.h"
 #include "iso14443a.h"
 #include "iso14443b.h"
 #include "iso15693.h"
@@ -1685,7 +1686,8 @@ static void PacketReceived(PacketCommandNG *packet) {
             break;
         }
         case CMD_HF_ICLASS_READER: {
-            ReaderIClass(packet->oldarg[0]);
+            iclass_card_select_t *payload = (iclass_card_select_t *) packet->data.asBytes;
+            ReaderIClass(payload->flags);
             break;
         }
         case CMD_HF_ICLASS_EML_MEMSET: {
diff --git a/armsrc/iclass.c b/armsrc/iclass.c
index 9fe238e6a..b7090ad9d 100644
--- a/armsrc/iclass.c
+++ b/armsrc/iclass.c
@@ -34,6 +34,7 @@
 #include "protocols.h"
 #include "ticks.h"
 #include "iso15693.h"
+#include "iclass_cmd.h"              /* iclass_card_select_t struct */
 
 static uint8_t get_pagemap(const picopass_hdr_t *hdr) {
     return (hdr->conf.fuses & (FUSE_CRYPT0 | FUSE_CRYPT1)) >> 3;
@@ -1402,13 +1403,8 @@ bool select_iclass_tag(picopass_hdr_t *hdr, bool use_credit_key, uint32_t *eof_t
 // turn off afterwards
 void ReaderIClass(uint8_t flags) {
 
-    picopass_hdr_t hdr = {0};
-//    uint8_t last_csn[8] = {0, 0, 0, 0, 0, 0, 0, 0};
-    uint8_t resp[ICLASS_BUFFER_SIZE] = {0};
-    memset(resp, 0xFF, sizeof(resp));
-
-//    bool flag_readonce = flags & FLAG_ICLASS_READER_ONLY_ONCE;    // flag to read until one tag is found successfully
-    bool use_credit_key = flags & FLAG_ICLASS_READER_CREDITKEY;     // flag to use credit key
+    // flag to use credit key
+    bool use_credit_key = ((flags & FLAG_ICLASS_READER_CREDITKEY) == FLAG_ICLASS_READER_CREDITKEY);
 
     if ((flags & FLAG_ICLASS_READER_INIT) == FLAG_ICLASS_READER_INIT) {
         Iso15693InitReader();
@@ -1418,13 +1414,14 @@ void ReaderIClass(uint8_t flags) {
         clear_trace();
     }
 
-    uint8_t result_status = 0;
+
+    uint8_t res = 0;
     uint32_t eof_time = 0;
-    bool status = select_iclass_tag_ex(&hdr, use_credit_key, &eof_time, &result_status);
-    if (status == false) {
-        reply_mix(CMD_ACK, 0xFF, 0, 0, NULL, 0);
-        switch_off();
-        return;
+    picopass_hdr_t hdr = {0};
+
+    if (select_iclass_tag_ex(&hdr, use_credit_key, &eof_time, &res) == false) {
+        reply_ng(CMD_HF_ICLASS_READER, PM3_ERFTRANS, NULL, 0);
+        goto out;
     }
 
     // Page mapping for secure mode
@@ -1443,30 +1440,14 @@ void ReaderIClass(uint8_t flags) {
     // Return to client, e 6 * 8 bytes of data.
     // with 0xFF:s in block 3 and 4.
 
-    LED_B_ON();
-    reply_mix(CMD_ACK, result_status, 0, 0, (uint8_t *)&hdr, sizeof(hdr));
-
-    //Send back to client, but don't bother if we already sent this -
-    //  only useful if looping in arm (not try_once && not abort_after_read)
-    /*
-    if (memcmp(last_csn, card_data, 8) != 0) {
-
-        reply_mix(CMD_ACK, result_status, 0, 0, card_data, sizeof(card_data));
-        if (flag_readonce) {
-            LED_B_OFF();
-            return;
-        }
-        LED_B_OFF();
-    }
-    */
+    iclass_card_select_resp_t payload = {
+        .status = res
+    };
+    memcpy(&payload.header.hdr, &hdr, sizeof(picopass_hdr_t));
 
-//    if (userCancelled) {
-//        reply_mix(CMD_ACK, 0xFF, 0, 0, card_data, 0);
-//        switch_off();
-//    } else {
-//        reply_mix(CMD_ACK, result_status, 0, 0, card_data, 0);
-//    }
+    reply_ng(CMD_HF_ICLASS_READER, PM3_SUCCESS, (uint8_t*)&payload, sizeof(iclass_card_select_resp_t));
 
+out: 
     switch_off();
 }
 
diff --git a/armsrc/iclass.h b/armsrc/iclass.h
index 958382256..c0cbe9b3b 100644
--- a/armsrc/iclass.h
+++ b/armsrc/iclass.h
@@ -13,7 +13,7 @@
 #define __ICLASS_H
 
 #include "common.h"
-#include "pm3_cmd.h"
+#include "iclass_cmd.h"
 
 void SniffIClass(uint8_t jam_search_len, uint8_t *jam_search_string);
 void ReaderIClass(uint8_t flags);
diff --git a/client/src/cmdhficlass.c b/client/src/cmdhficlass.c
index 8c68833ab..b9945bf2e 100644
--- a/client/src/cmdhficlass.c
+++ b/client/src/cmdhficlass.c
@@ -32,10 +32,11 @@
 #include "wiegand_formatutils.h"
 #include "cmdsmartcard.h"   // smart select fct
 #include "proxendian.h"
+#include "iclass_cmd.h"
 
-#define NUM_CSNS 9
-#define ICLASS_KEYS_MAX 8
-#define ICLASS_AUTH_RETRY 10
+#define NUM_CSNS               9
+#define ICLASS_KEYS_MAX        8
+#define ICLASS_AUTH_RETRY      10
 #define ICLASS_DECRYPTION_BIN  "iclass_decryptionkey.bin"
 
 static void print_picopass_info(const picopass_hdr_t *hdr);
@@ -165,7 +166,7 @@ static const char *card_types[] = {
 };
 
 static uint8_t card_app2_limit[] = {
-    0xff,
+    0x1f,
     0xff,
     0xff,
     0xff,
@@ -278,7 +279,8 @@ static int generate_config_card(const iclass_config_card_item_t *o,  uint8_t *ke
         // calc diversified key for selected card
         HFiClassCalcDivKey(cc->csn, iClass_Key_Table[0], cc->key_d, false);
     } else {
-        PrintAndLogEx(INFO, "failed to read a card, will use default config card data");
+        PrintAndLogEx(FAILED, "failed to read a card");
+        PrintAndLogEx(INFO,"falling back to default config card");
     }
 
     // generate dump file
@@ -323,35 +325,47 @@ static int generate_config_card(const iclass_config_card_item_t *o,  uint8_t *ke
                 return PM3_EMALLOC;
             }
             data = p;
-            memset(data, 0xFF,  tot_bytes);
         }
 
+        memset(data + sizeof(picopass_hdr_t), 0xFF,  tot_bytes - sizeof(picopass_hdr_t));
+
+        bool old = GetFlushAfterWrite();
+        SetFlushAfterWrite(true);
+
         // KEYROLL need to encrypt
+        PrintAndLogEx(INFO, "Setting up encryption... " NOLF);
         uint8_t ffs[8] = {0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF, 0xFF};
         if (Encrypt(ffs, ffs) == false) {
             PrintAndLogEx(WARNING, "failed to encrypt FF");
+        } else {
+            PrintAndLogEx(NORMAL,"( " _GREEN_("ok") " )");
         }
 
         // local key copy
+        PrintAndLogEx(INFO, "Encrypting local key... " NOLF);
         uint8_t lkey[8];
         memcpy(lkey, key, sizeof(lkey));
-
         uint8_t enckey1[8];
         if (Encrypt(lkey, enckey1) == false) {
             PrintAndLogEx(WARNING, "failed to encrypt key1");
+        } else {
+            PrintAndLogEx(NORMAL,"( " _GREEN_("ok") " )");
         }
 
+        PrintAndLogEx(INFO, "Copy data... " NOLF);
         memcpy(data, cc, sizeof(picopass_hdr_t));
         memcpy(data + (6 * 8), o->data, sizeof(o->data));
 
         // encrypted keyroll key 0D
         memcpy(data + (0xD * 8), enckey1, sizeof(enckey1));
         // encrypted 0xFF
-        for (uint8_t i = 0xe; i < 0x14; i++) {
+        for (uint8_t i = 0xD; i < 0x14; i++) {
             memcpy(data + (i * 8), ffs, sizeof(ffs));
         }
+        PrintAndLogEx(NORMAL,"( " _GREEN_("ok") " )");
 
         // encrypted partial keyroll key 14
+        PrintAndLogEx(INFO, "Setting encrypted partial key14... " NOLF);
         uint8_t foo[8] = {0x15};
         memcpy(foo + 1, lkey, 7);
         uint8_t enckey2[8];
@@ -359,34 +373,43 @@ static int generate_config_card(const iclass_config_card_item_t *o,  uint8_t *ke
             PrintAndLogEx(WARNING, "failed to encrypt partial 1");
         }
         memcpy(data + (0x14 * 8), enckey2, sizeof(enckey2));
+        PrintAndLogEx(NORMAL,"( " _GREEN_("ok") " )");
+
 
         // encrypted partial keyroll key 15
+        PrintAndLogEx(INFO, "Setting encrypted partial key15... " NOLF);
         memset(foo, 0xFF, sizeof(foo));
         foo[0] = lkey[7];
         if (Encrypt(foo, enckey2) == false) {
             PrintAndLogEx(WARNING, "failed to encrypt partial 2");
         }
         memcpy(data + (0x15 * 8), enckey2, sizeof(enckey2));
+        PrintAndLogEx(NORMAL,"( " _GREEN_("ok") " )");
 
         // encrypted 0xFF
+        PrintAndLogEx(INFO, "Setting 0xFF's... " NOLF);
         for (uint8_t i = 0x16; i <= app1_limit; i++) {
             memcpy(data + (i * 8), ffs, sizeof(ffs));
-        }
+        }        
+        PrintAndLogEx(NORMAL,"( " _GREEN_("ok") " )");
 
         // revert potential modified app1_limit
         cc->conf.app_limit = old_limit;
 
+        SetFlushAfterWrite(old);
     } else {
         memcpy(data, cc, sizeof(picopass_hdr_t));
         memcpy(data + (6 * 8), o->data, sizeof(o->data));
     }
 
     //Send to device
+    PrintAndLogEx(INFO, "Uploading to device... ");
     uint16_t bytes_sent = 0;
     iclass_upload_emul(data, tot_bytes, &bytes_sent);
     free(data);
 
-    PrintAndLogEx(SUCCESS, "sent %u bytes of data to device emulator memory", bytes_sent);
+    PrintAndLogEx(NORMAL, "");
+    PrintAndLogEx(SUCCESS, "sent " _YELLOW_("%u") " bytes of data to device emulator memory", bytes_sent);
     PrintAndLogEx(HINT, "Try `" _YELLOW_("hf iclass eview") "` to view dump file");
     PrintAndLogEx(HINT, "Try `" _YELLOW_("hf iclass sim -t 3") "` to start simulating config card");
     return PM3_SUCCESS;
@@ -454,15 +477,21 @@ static void fuse_config(const picopass_hdr_t *hdr) {
         PrintAndLogEx(SUCCESS, "    RA........... Read access enabled (non-secure mode)");
     else
         PrintAndLogEx(INFO, "    RA........... Read access not enabled");
+
+    if (notset(fuses, FUSE_FPROD0) && isset(fuses, FUSE_FPROD1)) {
+        PrintAndLogEx(INFO, "    PROD0/1...... Default production fuses");
+    }
 }
 
-static void getMemConfig(uint8_t mem_cfg, uint8_t chip_cfg, uint8_t *app_areas, uint8_t *kb) {
+static void getMemConfig(uint8_t mem_cfg, uint8_t chip_cfg, uint8_t *app_areas, uint8_t *kb, uint8_t *books, uint8_t *pages) {
     // How to determine chip type
 
     // mem-bit 7 = 16K
     // mem-bit 5 = Book
     // mem-bit 4 = 2K
     // chip-bit 4 = Multi App
+    *books = 1;
+    *pages = 1;
 
     uint8_t k16 = isset(mem_cfg, 0x80);
     //uint8_t k2 = isset(mem_cfg, 0x10);
@@ -477,12 +506,16 @@ static void getMemConfig(uint8_t mem_cfg, uint8_t chip_cfg, uint8_t *app_areas,
     } else if (notset(chip_cfg, 0x10) && !k16 && !book) {
         *kb = 16;
         *app_areas = 16;
+        *pages = 8;
     } else if (isset(chip_cfg, 0x10) && k16 && book) {
         *kb = 32;
         *app_areas = 3;
+        *books = 2;
     } else if (notset(chip_cfg, 0x10) && !k16 && book) {
         *kb = 32;
         *app_areas = 17;
+        *pages = 8;
+        *books = 2;
     } else {
         *kb = 32;
         *app_areas = 2;
@@ -506,8 +539,10 @@ static void mem_app_config(const picopass_hdr_t *hdr) {
     uint8_t chip = hdr->conf.chip_config;
     uint8_t kb = 2;
     uint8_t app_areas = 2;
-
-    getMemConfig(mem, chip, &app_areas, &kb);
+    uint8_t books = 1;
+    uint8_t pages = 1;
+        
+    getMemConfig(mem, chip, &app_areas, &kb, &books, &pages);
 
     uint8_t type = get_mem_config(hdr);
     uint8_t app1_limit = hdr->conf.app_limit - 5; // minus header blocks
@@ -522,9 +557,26 @@ static void mem_app_config(const picopass_hdr_t *hdr) {
         return;
     }
 
-    PrintAndLogEx(INFO, " %u KBits/%u App Areas ( " _YELLOW_("%u") " bytes )", kb, app_areas, (app2_limit + 1) * 8);
-    PrintAndLogEx(INFO, "    AA1 blocks %u { 0x06 - 0x%02X (06 - %02d) }", app1_limit, app1_limit + 5, app1_limit + 5);
-    PrintAndLogEx(INFO, "    AA2 blocks %u { 0x%02X - 0x%02X (%02d - %02d) }", app2_limit - app1_limit, app1_limit + 5 + 1, app2_limit, app1_limit + 5 + 1, app2_limit);
+    PrintAndLogEx(INFO, " %u KBits/%u App Areas ( " _YELLOW_("%u") " bytes )"
+        , kb
+        , app_areas
+        , ((app2_limit + 1) * 8) * books * pages);
+
+    PrintAndLogEx(INFO, "    %u books / %u pages"
+        , books
+        , pages
+    );
+    PrintAndLogEx(INFO, " First book / first page configuration");
+    PrintAndLogEx(INFO, "    Config | 0 - 5 ( 0x00 - 0x05 ) - 6 blocks ");
+    PrintAndLogEx(INFO, "    AA1    | 6 - %2d ( 0x06 - 0x%02X ) - %u blocks", app1_limit + 5, app1_limit + 5, app1_limit);
+    if (app1_limit + 5 < app2_limit ) {
+        PrintAndLogEx(INFO, "    AA2    | %2d - %2d ( 0x%02X - 0x%02X ) - %u blocks", app1_limit + 5 + 1, app2_limit, app1_limit + 5 + 1, app2_limit, app2_limit - app1_limit);
+    }
+/*
+[=]  32 KBits/3 App Areas ( 2048 bytes )
+[=]     AA1 blocks 250 { 0x06 - 0xFF (06 - 255) }
+[=]     AA2 blocks 5 { 0x100 - 0xFF (256 - 255) }
+*/
 
     PrintAndLogEx(INFO, "------------------------- " _CYAN_("KeyAccess") " ------------------------");
     PrintAndLogEx(INFO, " * Kd, Debit key, AA1    Kc, Credit key, AA2 *");
@@ -557,8 +609,19 @@ void print_picopass_header(const picopass_hdr_t *hdr) {
     PrintAndLogEx(SUCCESS, "    CSN: " _GREEN_("%s") " uid", sprint_hex(hdr->csn, sizeof(hdr->csn)));
     PrintAndLogEx(SUCCESS, " Config: %s Card configuration", sprint_hex((uint8_t *)&hdr->conf, sizeof(hdr->conf)));
     PrintAndLogEx(SUCCESS, "E-purse: %s Card challenge, CC", sprint_hex(hdr->epurse, sizeof(hdr->epurse)));
-    PrintAndLogEx(SUCCESS, "     Kd: %s Debit key, hidden", sprint_hex(hdr->key_d, sizeof(hdr->key_d)));
-    PrintAndLogEx(SUCCESS, "     Kc: %s Credit key, hidden", sprint_hex(hdr->key_c, sizeof(hdr->key_c)));
+
+    if (memcmp(hdr->key_d, zeros, sizeof(zeros)) && memcmp(hdr->key_d, empty, sizeof(empty))) {
+        PrintAndLogEx(SUCCESS, "     Kd: " _YELLOW_("%s") " debit key", sprint_hex(hdr->key_d, sizeof(hdr->key_d)));
+    } else {
+        PrintAndLogEx(SUCCESS, "     Kd: %s debit key ( hidden )", sprint_hex(hdr->key_d, sizeof(hdr->key_d)));
+    }
+
+    if (memcmp(hdr->key_c, zeros, sizeof(zeros)) && memcmp(hdr->key_c, empty, sizeof(empty))) {
+        PrintAndLogEx(SUCCESS, "     Kc: " _YELLOW_("%s") " credit key", sprint_hex(hdr->key_c, sizeof(hdr->key_c)));
+    } else {
+        PrintAndLogEx(SUCCESS, "     Kc: %s credit key ( hidden )", sprint_hex(hdr->key_c, sizeof(hdr->key_c)));
+    }
+
     PrintAndLogEx(SUCCESS, "    AIA: %s Application Issuer area", sprint_hex(hdr->app_issuer_area, sizeof(hdr->app_issuer_area)));
 }
 
@@ -834,25 +897,28 @@ static int CmdHFiClassInfo(const char *Cmd) {
 
 int read_iclass_csn(bool loop, bool verbose) {
 
-    uint32_t flags = (FLAG_ICLASS_READER_INIT | FLAG_ICLASS_READER_CLEARTRACE);
+    iclass_card_select_t payload = {
+        .flags =  (FLAG_ICLASS_READER_INIT | FLAG_ICLASS_READER_CLEARTRACE)
+    };
+
     int res = PM3_SUCCESS;
 
     do {
         clearCommandBuffer();
-        SendCommandMIX(CMD_HF_ICLASS_READER, flags, 0, 0, NULL, 0);
         PacketResponseNG resp;
-        if (WaitForResponseTimeout(CMD_ACK, &resp, 2000)) {
+        SendCommandNG(CMD_HF_ICLASS_READER, (uint8_t*)&payload, sizeof(iclass_card_select_t));
 
-            uint8_t status = resp.oldarg[0] & 0xff;
+        if (WaitForResponseTimeout(CMD_HF_ICLASS_READER, &resp, 2000)) {
 
+            iclass_card_select_resp_t *r = (iclass_card_select_resp_t*)resp.data.asBytes;
             if (loop) {
-                if (status == 0xFF) {
+                if (resp.status == PM3_ERFTRANS) {
                     continue;
                 }
             } else {
 
-                if (status == 0 || status == 0xFF) {
-                    if (verbose) PrintAndLogEx(WARNING, "iCLASS / ISO15693 card select failed");
+                if (r->status == FLAG_ICLASS_NULL || resp.status == PM3_ERFTRANS) {
+                    if (verbose) PrintAndLogEx(WARNING, "iCLASS / Picopass card select failed ( %d )", r->status);
                     res = PM3_EOPABORTED;
                     break;
                 }
@@ -860,13 +926,17 @@ int read_iclass_csn(bool loop, bool verbose) {
 
             picopass_hdr_t *card = calloc(1, sizeof(picopass_hdr_t));
             if (card) {
-                memcpy(card, (picopass_hdr_t *)resp.data.asBytes, sizeof(picopass_hdr_t));
-                PrintAndLogEx(NORMAL, "");
+                memcpy(card, &r->header.hdr, sizeof(picopass_hdr_t));
+                if (loop == false) {
+                    PrintAndLogEx(NORMAL, "");
+                }
                 PrintAndLogEx(SUCCESS, "iCLASS / Picopass CSN: " _GREEN_("%s"), sprint_hex(card->csn, sizeof(card->csn)));
                 iclass_set_last_known_card(card);
                 free(card);
+                res = PM3_SUCCESS;
             } else {
                 PrintAndLogEx(FAILED, "failed to allocate memory");
+                res = PM3_EMALLOC;
             }
         }
     } while (loop && kbd_enter_pressed() == false);
@@ -1240,7 +1310,9 @@ static int CmdHFiClassDecrypt(const char *Cmd) {
         } else {
             mbedtls_des3_crypt_ecb(&ctx, enc_data, dec_data);
         }
-        PrintAndLogEx(SUCCESS, "Data: %s", sprint_hex(dec_data, sizeof(dec_data)));
+
+        PrintAndLogEx(SUCCESS, "encrypted... %s", sprint_hex_inrow(enc_data, sizeof(enc_data)));
+        PrintAndLogEx(SUCCESS, "plain....... " _YELLOW_("%s"), sprint_hex_inrow(dec_data, sizeof(dec_data)));
 
         if (use_sc && use_decode6)
             DecodeBlock6(dec_data);
@@ -1256,7 +1328,9 @@ static int CmdHFiClassDecrypt(const char *Cmd) {
         uint8_t applimit = hdr->conf.app_limit;
         uint8_t kb = 2;
         uint8_t app_areas = 2;
-        getMemConfig(mem, chip, &app_areas, &kb);
+        uint8_t books = 1;
+        uint8_t pages = 1;
+        getMemConfig(mem, chip, &app_areas, &kb, &books, &pages);
 
         BLOCK79ENCRYPTION aa1_encryption = (decrypted[(6 * 8) + 7] & 0x03);
 
@@ -1455,39 +1529,45 @@ static int CmdHFiClassEncryptBlk(const char *Cmd) {
         }
     }
 
+
+    PrintAndLogEx(SUCCESS, "plain....... %s", sprint_hex_inrow(blk_data, sizeof(blk_data)));
+
     if (use_sc) {
         Encrypt(blk_data, blk_data);
     } else {
         iclass_encrypt_block_data(blk_data, key);
     }
-    PrintAndLogEx(SUCCESS, "encrypted block %s", sprint_hex(blk_data, 8));
+    
+    PrintAndLogEx(SUCCESS, "encrypted... " _YELLOW_("%s"), sprint_hex_inrow(blk_data, sizeof(blk_data)));
     return PM3_SUCCESS;
 }
 
 static bool select_only(uint8_t *CSN, uint8_t *CCNR, bool verbose) {
 
-    uint8_t flags = (FLAG_ICLASS_READER_INIT | FLAG_ICLASS_READER_CLEARTRACE);
+    iclass_card_select_t payload = {
+        .flags =  (FLAG_ICLASS_READER_INIT | FLAG_ICLASS_READER_CLEARTRACE)
+    };
 
     clearCommandBuffer();
     PacketResponseNG resp;
-    SendCommandMIX(CMD_HF_ICLASS_READER, flags, 0, 0, NULL, 0);
-    if (WaitForResponseTimeout(CMD_ACK, &resp, 2000) == false) {
+    SendCommandNG(CMD_HF_ICLASS_READER, (uint8_t*)&payload, sizeof(iclass_card_select_t));
+
+    if (WaitForResponseTimeout(CMD_HF_ICLASS_READER, &resp, 2000) == false) {
         PrintAndLogEx(WARNING, "command execute timeout");
         return false;
     }
 
-    uint8_t isok = resp.oldarg[0] & 0xff;
+    iclass_card_select_resp_t *r = (iclass_card_select_resp_t*)resp.data.asBytes;
+    picopass_hdr_t *hdr = &r->header.hdr;
 
     // no tag found or button pressed
-    if ((isok == 0) || isok == 0xFF) {
+    if (r->status == FLAG_ICLASS_NULL || resp.status == PM3_ERFTRANS) {
         if (verbose) {
-            PrintAndLogEx(FAILED, "failed tag-select, aborting...  (%d)", isok);
+            PrintAndLogEx(FAILED, "failed tag-select, aborting...  (%d)", r->status);
         }
         return false;
     }
 
-    picopass_hdr_t *hdr = (picopass_hdr_t *)resp.data.asBytes;
-
     if (CSN != NULL)
         memcpy(CSN, hdr->csn, 8);
 
@@ -1614,34 +1694,41 @@ static int CmdHFiClassDump(const char *Cmd) {
 
     uint8_t app_limit1 = 0, app_limit2 = 0;
 
-    uint32_t flags = (FLAG_ICLASS_READER_INIT | FLAG_ICLASS_READER_CLEARTRACE);
-
     //get CSN and config
-    PacketResponseNG resp;
     uint8_t tag_data[0x100 * 8];
     memset(tag_data, 0xFF, sizeof(tag_data));
 
+
+    iclass_card_select_t payload_rdr = {
+        .flags =  (FLAG_ICLASS_READER_INIT | FLAG_ICLASS_READER_CLEARTRACE)
+    };
     clearCommandBuffer();
-    SendCommandMIX(CMD_HF_ICLASS_READER, flags, 0, 0, NULL, 0);
-    if (!WaitForResponseTimeout(CMD_ACK, &resp, 2000)) {
+    PacketResponseNG resp;
+    SendCommandNG(CMD_HF_ICLASS_READER, (uint8_t*)&payload_rdr, sizeof(iclass_card_select_t));
+
+    if (WaitForResponseTimeout(CMD_HF_ICLASS_READER, &resp, 2000) == false) {
         PrintAndLogEx(WARNING, "command execute timeout");
         DropField();
         return PM3_ESOFT;
     }
     DropField();
 
-    uint8_t readStatus = resp.oldarg[0] & 0xff;
-    picopass_hdr_t *hdr = (picopass_hdr_t *)resp.data.asBytes;
-
-    if (readStatus == 0) {
+    if (resp.status == PM3_ERFTRANS) {
         PrintAndLogEx(FAILED, "no tag found");
         DropField();
         return PM3_ESOFT;
     }
 
+    iclass_card_select_resp_t *r = (iclass_card_select_resp_t*)resp.data.asBytes;
+    if (r->status == FLAG_ICLASS_NULL) {
+        PrintAndLogEx(FAILED, "failed to read block 0,1,2");
+        return PM3_ESOFT;
+    }
+
+    picopass_hdr_t *hdr = &r->header.hdr;
     uint8_t pagemap = get_pagemap(hdr);
 
-    if (readStatus & (FLAG_ICLASS_CSN | FLAG_ICLASS_CONF | FLAG_ICLASS_CC)) {
+    if (r->status & (FLAG_ICLASS_CSN | FLAG_ICLASS_CONF | FLAG_ICLASS_CC)) {
 
         memcpy(tag_data, hdr, 24);
 
@@ -1661,11 +1748,6 @@ static int CmdHFiClassDump(const char *Cmd) {
             app_limit1 = hdr->conf.app_limit;
             app_limit2 = card_app2_limit[type];
         }
-
-    } else {
-        PrintAndLogEx(FAILED, "failed to read block 0,1,2");
-        DropField();
-        return PM3_ESOFT;
     }
 
     if (pagemap == PICOPASS_NON_SECURE_PAGEMODE) {
@@ -2470,11 +2552,10 @@ void printIclassDumpContents(uint8_t *iclass_dump, uint8_t startblock, uint8_t e
     );
     */
     uint8_t pagemap = get_pagemap(hdr);
-
-
-
     int i = startblock;
 
+    PrintAndLogEx(NORMAL, "");
+    PrintAndLogEx(INFO, "-------------------------- " _CYAN_("Tag memory") " ---------------------------");
     PrintAndLogEx(NORMAL, "");
     PrintAndLogEx(INFO, " block#  | data                    | ascii    |lck| info");
     PrintAndLogEx(INFO, "---------+-------------------------+----------+---+--------------");
@@ -2484,7 +2565,7 @@ void printIclassDumpContents(uint8_t *iclass_dump, uint8_t startblock, uint8_t e
                  );
 
     if (i != 1)
-        PrintAndLogEx(INFO, "....");
+        PrintAndLogEx(INFO, "  ......");
 
     while (i <= endblock) {
         uint8_t *blk = iclass_dump + (i * 8);
@@ -3956,88 +4037,101 @@ int CmdHFiClass(const char *Cmd) {
 
 int info_iclass(void) {
 
-    uint32_t flags = (FLAG_ICLASS_READER_INIT | FLAG_ICLASS_READER_CLEARTRACE);
-
+    iclass_card_select_t payload = {
+        .flags =  (FLAG_ICLASS_READER_INIT | FLAG_ICLASS_READER_CLEARTRACE)
+    };
     clearCommandBuffer();
-    SendCommandMIX(CMD_HF_ICLASS_READER, flags, 0, 0, NULL, 0);
     PacketResponseNG resp;
+    SendCommandNG(CMD_HF_ICLASS_READER, (uint8_t*)&payload, sizeof(iclass_card_select_t));
 
-    if (WaitForResponseTimeout(CMD_ACK, &resp, 2000)) {
+    if (WaitForResponseTimeout(CMD_HF_ICLASS_READER, &resp, 2000) == false) {
+        DropField();
+        return PM3_ETIMEOUT;
+    }
+    DropField();
 
-        uint8_t readStatus = resp.oldarg[0] & 0xff;
+    iclass_card_select_resp_t *r = (iclass_card_select_resp_t*)resp.data.asBytes;
 
-        // no tag found or button pressed
-        if (readStatus == 0 || readStatus == 0xFF) {
-            DropField();
-            return PM3_EOPABORTED;
-        }
+    // no tag found or button pressed
+    if (r->status == FLAG_ICLASS_NULL || resp.status == PM3_ERFTRANS) {
+        return PM3_EOPABORTED;
+    }
 
-        picopass_hdr_t *hdr = (picopass_hdr_t *)resp.data.asBytes;
-        picopass_ns_hdr_t *ns_hdr = (picopass_ns_hdr_t *)resp.data.asBytes;
+    picopass_hdr_t *hdr = &r->header.hdr;
+    picopass_ns_hdr_t *ns_hdr = &r->header.ns_hdr;
 
-        PrintAndLogEx(NORMAL, "");
-        PrintAndLogEx(INFO, "--------------------- " _CYAN_("Tag Information") " ----------------------");
+    PrintAndLogEx(NORMAL, "");
+    PrintAndLogEx(INFO, "--------------------- " _CYAN_("Tag Information") " ----------------------");
 
-        if (readStatus & FLAG_ICLASS_CSN) {
-            PrintAndLogEx(SUCCESS, "    CSN: " _GREEN_("%s") " uid", sprint_hex(hdr->csn, sizeof(hdr->csn)));
-        }
+    if ((r->status & FLAG_ICLASS_CSN) == FLAG_ICLASS_CSN) {
+        PrintAndLogEx(SUCCESS, "    CSN: " _GREEN_("%s") " uid", sprint_hex(hdr->csn, sizeof(hdr->csn)));
+    }
 
-        if (readStatus & FLAG_ICLASS_CONF) {
-            PrintAndLogEx(SUCCESS, " Config: %s card configuration", sprint_hex((uint8_t *)&hdr->conf, sizeof(hdr->conf)));
-        }
+    if ((r->status & FLAG_ICLASS_CONF) == FLAG_ICLASS_CONF) {
+        PrintAndLogEx(SUCCESS, " Config: %s card configuration", sprint_hex((uint8_t *)&hdr->conf, sizeof(hdr->conf)));
+    }
 
-        // page mapping.  If fuse0|1 == 0x01, card is in non-secure mode, with CSN, CONF, AIA as top 3 blocks.
-        // page9 in http://www.proxmark.org/files/Documents/13.56%20MHz%20-%20iClass/DS%20Picopass%202KS%20V1-0.pdf
-        uint8_t pagemap = get_pagemap(hdr);
-        if (pagemap == PICOPASS_NON_SECURE_PAGEMODE) {
-            PrintAndLogEx(SUCCESS, "    AIA: %s application issuer area", sprint_hex(ns_hdr->app_issuer_area, sizeof(ns_hdr->app_issuer_area)));
-        } else {
+    // page mapping.  If fuse0|1 == 0x01, card is in non-secure mode, with CSN, CONF, AIA as top 3 blocks.
+    // page9 in http://www.proxmark.org/files/Documents/13.56%20MHz%20-%20iClass/DS%20Picopass%202KS%20V1-0.pdf
+    uint8_t pagemap = get_pagemap(hdr);
+    if (pagemap == PICOPASS_NON_SECURE_PAGEMODE) {
+        PrintAndLogEx(SUCCESS, "    AIA: %s application issuer area", sprint_hex(ns_hdr->app_issuer_area, sizeof(ns_hdr->app_issuer_area)));
+    } else {
 
-            if (readStatus & FLAG_ICLASS_CC) {
-                PrintAndLogEx(SUCCESS, "E-purse: %s Card challenge, CC", sprint_hex(hdr->epurse, sizeof(hdr->epurse)));
-            }
+        if ((r->status & FLAG_ICLASS_CC) == FLAG_ICLASS_CC) {
+            PrintAndLogEx(SUCCESS, "E-purse: %s Card challenge, CC", sprint_hex(hdr->epurse, sizeof(hdr->epurse)));
+        }
 
-            PrintAndLogEx(SUCCESS, "     Kd: %s debit key, hidden", sprint_hex(hdr->key_d, sizeof(hdr->key_d)));
-            PrintAndLogEx(SUCCESS, "     Kc: %s credit key, hidden", sprint_hex(hdr->key_c, sizeof(hdr->key_c)));
+        if (memcmp(hdr->key_d, zeros, sizeof(zeros))) {
+            PrintAndLogEx(SUCCESS, "     Kd: " _YELLOW_("%s") " debit key", sprint_hex(hdr->key_d, sizeof(hdr->key_d)));
+        } else {
+            PrintAndLogEx(SUCCESS, "     Kd: %s debit key ( hidden )", sprint_hex(hdr->key_d, sizeof(hdr->key_d)));
+        }
 
-            if (readStatus & FLAG_ICLASS_AIA) {
-                PrintAndLogEx(SUCCESS, "    AIA: %s application issuer area", sprint_hex(hdr->app_issuer_area, sizeof(hdr->app_issuer_area)));
-            }
+        if (memcmp(hdr->key_c, zeros, sizeof(zeros))) {
+            PrintAndLogEx(SUCCESS, "     Kc: " _YELLOW_("%s") " credit key", sprint_hex(hdr->key_c, sizeof(hdr->key_c)));
+        } else {
+            PrintAndLogEx(SUCCESS, "     Kc: %s credit key ( hidden )", sprint_hex(hdr->key_c, sizeof(hdr->key_c)));
         }
+        
 
-        if (readStatus & FLAG_ICLASS_CONF) {
-            print_picopass_info(hdr);
+        if ((r->status & FLAG_ICLASS_AIA) == FLAG_ICLASS_AIA) {
+            PrintAndLogEx(SUCCESS, "    AIA: %s application issuer area", sprint_hex(hdr->app_issuer_area, sizeof(hdr->app_issuer_area)));
         }
+    }
 
-        PrintAndLogEx(INFO, "------------------------ " _CYAN_("Fingerprint") " -----------------------");
+    if ((r->status & FLAG_ICLASS_CONF) == FLAG_ICLASS_CONF) {
+        print_picopass_info(hdr);
+    }
 
-        uint8_t aia[8];
-        if (pagemap == PICOPASS_NON_SECURE_PAGEMODE) {
-            memcpy(aia, ns_hdr->app_issuer_area, sizeof(aia));
-        } else {
-            memcpy(aia, hdr->app_issuer_area, sizeof(aia));
-        }
+    PrintAndLogEx(INFO, "------------------------ " _CYAN_("Fingerprint") " -----------------------");
 
-        // if CSN ends with FF12E0, it's inside HID CSN range.
-        bool isHidRange = (memcmp(hdr->csn + 5, "\xFF\x12\xE0", 3) == 0);
+    uint8_t aia[8];
+    if (pagemap == PICOPASS_NON_SECURE_PAGEMODE) {
+        memcpy(aia, ns_hdr->app_issuer_area, sizeof(aia));
+    } else {
+        memcpy(aia, hdr->app_issuer_area, sizeof(aia));
+    }
 
-        bool legacy = (memcmp(aia, "\xff\xff\xff\xff\xff\xff\xff\xff", 8) == 0);
-        bool se_enabled = (memcmp(aia, "\xff\xff\xff\x00\x06\xff\xff\xff", 8) == 0);
+    // if CSN ends with FF12E0, it's inside HID CSN range.
+    bool isHidRange = (memcmp(hdr->csn + 5, "\xFF\x12\xE0", 3) == 0);
 
-        if (isHidRange) {
-            PrintAndLogEx(SUCCESS, "    CSN.......... " _YELLOW_("HID range"));
-            if (legacy)
-                PrintAndLogEx(SUCCESS, "    Credential... " _GREEN_("iCLASS legacy"));
-            if (se_enabled)
-                PrintAndLogEx(SUCCESS, "    Credential... " _GREEN_("iCLASS SE"));
-        } else {
-            PrintAndLogEx(SUCCESS, "    CSN.......... " _YELLOW_("outside HID range"));
-        }
+    bool legacy = (memcmp(aia, "\xff\xff\xff\xff\xff\xff\xff\xff", 8) == 0);
+    bool se_enabled = (memcmp(aia, "\xff\xff\xff\x00\x06\xff\xff\xff", 8) == 0);
 
-        uint8_t cardtype = get_mem_config(hdr);
-        PrintAndLogEx(SUCCESS, "    Card type.... " _GREEN_("%s"), card_types[cardtype]);
+    if (isHidRange) {
+        PrintAndLogEx(SUCCESS, "    CSN.......... " _YELLOW_("HID range"));
+        if (legacy)
+            PrintAndLogEx(SUCCESS, "    Credential... " _GREEN_("iCLASS legacy"));
+        if (se_enabled)
+            PrintAndLogEx(SUCCESS, "    Credential... " _GREEN_("iCLASS SE"));
+    } else {
+        PrintAndLogEx(SUCCESS, "    CSN.......... " _YELLOW_("outside HID range"));
     }
-    DropField();
+
+    uint8_t cardtype = get_mem_config(hdr);
+    PrintAndLogEx(SUCCESS, "    Card type.... " _GREEN_("%s"), card_types[cardtype]);
+
     return PM3_SUCCESS;
 }
 
diff --git a/client/src/cmdhficlass.h b/client/src/cmdhficlass.h
index 73d34df0b..9474dde45 100644
--- a/client/src/cmdhficlass.h
+++ b/client/src/cmdhficlass.h
@@ -13,21 +13,7 @@
 
 #include "common.h"
 #include "fileutils.h"
-#include "pm3_cmd.h"
-
-typedef struct iclass_block {
-    uint8_t d[8];
-} iclass_block_t;
-
-typedef struct iclass_prekey {
-    uint8_t mac[4];
-    uint8_t key[8];
-} iclass_prekey_t;
-
-typedef struct {
-    char desc[70];
-    uint8_t data[16];
-} iclass_config_card_item_t;
+#include "iclass_cmd.h"
 
 int CmdHFiClass(const char *Cmd);
 
diff --git a/client/src/ui.c b/client/src/ui.c
index 8ee9c783e..250b1cb0e 100644
--- a/client/src/ui.c
+++ b/client/src/ui.c
@@ -47,7 +47,7 @@ double g_PlotGridX = 0, g_PlotGridY = 0, g_PlotGridXdefault = 64, g_PlotGridYdef
 uint32_t g_CursorCPos = 0, g_CursorDPos = 0, g_GraphStop = 0;
 uint32_t g_GraphStart = 0; // Starting point/offset for the left side of the graph
 double g_GraphPixelsPerPoint = 1.f; // How many visual pixels are between each sample point (x axis)
-static bool flushAfterWrite = 0;
+static bool flushAfterWrite = false;
 double g_GridOffset = 0;
 bool g_GridLocked = false;
 
@@ -411,6 +411,10 @@ void SetFlushAfterWrite(bool value) {
     flushAfterWrite = value;
 }
 
+bool GetFlushAfterWrite(void) {
+    return flushAfterWrite;
+}
+
 void memcpy_filter_rlmarkers(void *dest, const void *src, size_t n) {
     uint8_t *rdest = (uint8_t *)dest;
     uint8_t *rsrc = (uint8_t *)src;
diff --git a/client/src/ui.h b/client/src/ui.h
index 36f6fd22e..115234ee9 100644
--- a/client/src/ui.h
+++ b/client/src/ui.h
@@ -63,6 +63,7 @@ extern session_arg_t g_session;
 void PrintAndLogOptions(const char *str[][2], size_t size, size_t space);
 void PrintAndLogEx(logLevel_t level, const char *fmt, ...);
 void SetFlushAfterWrite(bool value);
+bool GetFlushAfterWrite(void);
 void memcpy_filter_ansi(void *dest, const void *src, size_t n, bool filter);
 void memcpy_filter_rlmarkers(void *dest, const void *src, size_t n);
 void memcpy_filter_emoji(void *dest, const void *src, size_t n, emojiMode_t mode);
diff --git a/include/iclass_cmd.h b/include/iclass_cmd.h
new file mode 100644
index 000000000..5bb4f9c92
--- /dev/null
+++ b/include/iclass_cmd.h
@@ -0,0 +1,159 @@
+//-----------------------------------------------------------------------------
+// (c) 2021 Iceman
+//
+// This code is licensed to you under the terms of the GNU GPL, version 2 or,
+// at your option, any later version. See the LICENSE.txt file for the text of
+// the license.
+//-----------------------------------------------------------------------------
+// iCLASS type prototyping
+//-----------------------------------------------------------------------------
+
+#ifndef _ICLASS_CMD_H_
+#define _ICLASS_CMD_H_
+
+#include "common.h"
+
+//-----------------------------------------------------------------------------
+// iCLASS / PICOPASS
+//-----------------------------------------------------------------------------
+
+
+// iCLASS reader flags
+#define FLAG_ICLASS_READER_INIT        0x01
+#define FLAG_ICLASS_READER_CLEARTRACE  0x02
+//#define FLAG_ICLASS_READER_ONLY_ONCE   0x04
+#define FLAG_ICLASS_READER_CREDITKEY   0x08
+#define FLAG_ICLASS_READER_AIA         0x10
+
+// iCLASS reader status flags
+#define FLAG_ICLASS_NULL               0x00
+#define FLAG_ICLASS_CSN                0x01
+#define FLAG_ICLASS_CC                 0x02
+#define FLAG_ICLASS_CONF               0x04
+#define FLAG_ICLASS_AIA                0x08
+
+// iCLASS simulation modes
+#define ICLASS_SIM_MODE_CSN                   0
+#define ICLASS_SIM_MODE_CSN_DEFAULT           1
+#define ICLASS_SIM_MODE_READER_ATTACK         2
+#define ICLASS_SIM_MODE_FULL                  3
+#define ICLASS_SIM_MODE_READER_ATTACK_KEYROLL 4
+#define ICLASS_SIM_MODE_EXIT_AFTER_MAC        5  // note: device internal only
+#define ICLASS_SIM_MODE_CONFIG_CARD           6
+
+
+// iCLASS auth request data structure
+// used with read block, dump, write block
+typedef struct {
+    uint8_t key[8];
+    bool use_raw;
+    bool use_elite;
+    bool use_credit_key;
+    bool use_replay;
+    bool send_reply;
+    bool do_auth;
+    uint8_t blockno;
+} PACKED iclass_auth_req_t;
+
+// iCLASS read block response data structure
+typedef struct {
+    bool isOK;
+    uint8_t div_key[8];
+    uint8_t mac[4];
+    uint8_t data[8];
+} PACKED iclass_readblock_resp_t;
+
+// iCLASS dump data structure
+typedef struct {
+    iclass_auth_req_t req;
+    uint8_t start_block;
+    uint8_t end_block;
+} PACKED iclass_dump_req_t;
+
+// iCLASS write block request data structure
+typedef struct {
+    iclass_auth_req_t req;
+    uint8_t data[8];
+} PACKED iclass_writeblock_req_t;
+
+// iCLASS dump data structure
+typedef struct {
+    uint8_t blockno;
+    uint8_t data[8];
+} PACKED iclass_restore_item_t;
+
+typedef struct {
+    iclass_auth_req_t req;
+    uint8_t item_cnt;
+    iclass_restore_item_t blocks[];
+} PACKED iclass_restore_req_t;
+
+typedef struct iclass_premac {
+    uint8_t mac[4];
+} PACKED iclass_premac_t;
+
+typedef struct {
+    bool use_credit_key;
+    uint8_t count;
+    iclass_premac_t items[];
+} PACKED iclass_chk_t;
+
+typedef struct iclass_block {
+    uint8_t d[8];
+} iclass_block_t;
+
+typedef struct iclass_prekey {
+    uint8_t mac[4];
+    uint8_t key[8];
+} iclass_prekey_t;
+
+typedef struct {
+    char desc[70];
+    uint8_t data[16];
+} iclass_config_card_item_t;
+
+
+// iclass / picopass chip config structures and shared routines
+typedef struct {
+    uint8_t app_limit;      //[8]
+    uint8_t otp[2];         //[9-10]
+    uint8_t block_writelock;//[11]
+    uint8_t chip_config;    //[12]
+    uint8_t mem_config;     //[13]
+    uint8_t eas;            //[14]
+    uint8_t fuses;          //[15]
+} PACKED picopass_conf_block_t;
+
+// iCLASS secure mode memory mapping
+typedef struct {
+    uint8_t csn[8];
+    picopass_conf_block_t conf;
+    uint8_t epurse[8];
+    uint8_t key_d[8];
+    uint8_t key_c[8];
+    uint8_t app_issuer_area[8];
+} PACKED picopass_hdr_t;
+
+// iCLASS non-secure mode memory mapping
+typedef struct {
+    uint8_t csn[8];
+    picopass_conf_block_t conf;
+    uint8_t app_issuer_area[8];
+} PACKED picopass_ns_hdr_t;
+
+// reader flags
+typedef struct {    
+    uint8_t flags;
+} PACKED iclass_card_select_t;
+
+// reader flags
+typedef struct {    
+    uint8_t status;
+    union {
+        picopass_hdr_t hdr;
+        picopass_ns_hdr_t ns_hdr;
+    } header;
+} PACKED iclass_card_select_resp_t;
+
+
+#endif // _ICLASS_H_
\ No newline at end of file
diff --git a/include/pm3_cmd.h b/include/pm3_cmd.h
index c8f4ab4d7..fe3a55862 100644
--- a/include/pm3_cmd.h
+++ b/include/pm3_cmd.h
@@ -291,92 +291,6 @@ typedef struct {
 } PACKED ecdsa_publickey_t;
 
 
-// iCLASS auth request data structure
-// used with read block, dump, write block
-typedef struct {
-    uint8_t key[8];
-    bool use_raw;
-    bool use_elite;
-    bool use_credit_key;
-    bool use_replay;
-    bool send_reply;
-    bool do_auth;
-    uint8_t blockno;
-} PACKED iclass_auth_req_t;
-
-// iCLASS read block response data structure
-typedef struct {
-    bool isOK;
-    uint8_t div_key[8];
-    uint8_t mac[4];
-    uint8_t data[8];
-} PACKED iclass_readblock_resp_t;
-
-// iCLASS dump data structure
-typedef struct {
-    iclass_auth_req_t req;
-    uint8_t start_block;
-    uint8_t end_block;
-} PACKED iclass_dump_req_t;
-
-// iCLASS write block request data structure
-typedef struct {
-    iclass_auth_req_t req;
-    uint8_t data[8];
-} PACKED iclass_writeblock_req_t;
-
-// iCLASS dump data structure
-typedef struct {
-    uint8_t blockno;
-    uint8_t data[8];
-} PACKED iclass_restore_item_t;
-
-typedef struct {
-    iclass_auth_req_t req;
-    uint8_t item_cnt;
-    iclass_restore_item_t blocks[];
-} PACKED iclass_restore_req_t;
-
-typedef struct iclass_premac {
-    uint8_t mac[4];
-} PACKED iclass_premac_t;
-
-typedef struct {
-    bool use_credit_key;
-    uint8_t count;
-    iclass_premac_t items[];
-} PACKED iclass_chk_t;
-
-
-// iclass / picopass chip config structures and shared routines
-typedef struct {
-    uint8_t app_limit;      //[8]
-    uint8_t otp[2];         //[9-10]
-    uint8_t block_writelock;//[11]
-    uint8_t chip_config;    //[12]
-    uint8_t mem_config;     //[13]
-    uint8_t eas;            //[14]
-    uint8_t fuses;          //[15]
-} PACKED picopass_conf_block_t;
-
-// iCLASS secure mode memory mapping
-typedef struct {
-    uint8_t csn[8];
-    picopass_conf_block_t conf;
-    uint8_t epurse[8];
-    uint8_t key_d[8];
-    uint8_t key_c[8];
-    uint8_t app_issuer_area[8];
-} PACKED picopass_hdr_t;
-
-// iCLASS non-secure mode memory mapping
-typedef struct {
-    uint8_t csn[8];
-    picopass_conf_block_t conf;
-    uint8_t app_issuer_area[8];
-} PACKED picopass_ns_hdr_t;
-
-
 typedef struct {
     uint16_t delay_us;
     bool on;
@@ -767,28 +681,6 @@ typedef struct {
 #define FLAG_CVE21_0430         0x2000
 
 
-// iCLASS reader flags
-#define FLAG_ICLASS_READER_INIT        0x01
-#define FLAG_ICLASS_READER_CLEARTRACE  0x02
-#define FLAG_ICLASS_READER_ONLY_ONCE   0x04
-#define FLAG_ICLASS_READER_CREDITKEY   0x08
-#define FLAG_ICLASS_READER_AIA         0x10
-
-// iCLASS reader status flags
-#define FLAG_ICLASS_CSN         0x01
-#define FLAG_ICLASS_CC          0x02
-#define FLAG_ICLASS_CONF        0x04
-#define FLAG_ICLASS_AIA         0x08
-
-// iCLASS simulation modes
-#define ICLASS_SIM_MODE_CSN                   0
-#define ICLASS_SIM_MODE_CSN_DEFAULT           1
-#define ICLASS_SIM_MODE_READER_ATTACK         2
-#define ICLASS_SIM_MODE_FULL                  3
-#define ICLASS_SIM_MODE_READER_ATTACK_KEYROLL 4
-#define ICLASS_SIM_MODE_EXIT_AFTER_MAC        5  // note: device internal only
-#define ICLASS_SIM_MODE_CONFIG_CARD           6
-
 #define MODE_SIM_CSN        0
 #define MODE_EXIT_AFTER_MAC 1
 #define MODE_FULLSIM        2
