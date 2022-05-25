commit 7e7c3e7fdf1dec582c0c023eed5d8fce94f5b731
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Dec 31 09:25:04 2021 +0100

    style

diff --git a/armsrc/hitagS.c b/armsrc/hitagS.c
index 79cfe89ad..11425dba2 100644
--- a/armsrc/hitagS.c
+++ b/armsrc/hitagS.c
@@ -320,10 +320,13 @@ static int check_select(uint8_t *rx, uint32_t uid) {
     uint32_t ans = 0x0;
     for (int i = 0; i < 48; i++)
         resp[i] = (rx[i / 8] >> (7 - (i % 8))) & 0x1;
+
     for (int i = 0; i < 32; i++)
         ans += resp[5 + i] << (31 - i);
 
+    // global var?
     temp_uid = ans;
+
     if (ans == tag.uid)
         return 1;
 
@@ -370,11 +373,13 @@ static void hitagS_handle_reader_command(uint8_t *rx, const size_t rxlen,
             //UID request with a selected response protocol mode
             if (g_dbglevel >= DBG_EXTENDED)
                 Dbprintf("UID request: length: %i first byte: %02x", rxlen, rx[0]);
+
             tag.pstate = HT_READY;
             tag.tstate = HT_NO_OP;
             if ((rx[0] & 0xf0) == 0x30) {
                 if (g_dbglevel >= DBG_EXTENDED)
                     Dbprintf("HT_STANDARD");
+
                 tag.mode = HT_STANDARD;
                 sof_bits = 1;
                 m = AC2K;
@@ -383,6 +388,7 @@ static void hitagS_handle_reader_command(uint8_t *rx, const size_t rxlen,
                 tag.mode = HT_ADVANCED;
                 if (g_dbglevel >= DBG_EXTENDED)
                     Dbprintf("HT_ADVANCED");
+
                 sof_bits = 3;
                 m = AC2K;
             }
@@ -390,36 +396,45 @@ static void hitagS_handle_reader_command(uint8_t *rx, const size_t rxlen,
             if ((rx[0] & 0xf0) == 0xd0) {
                 if (g_dbglevel >= DBG_EXTENDED)
                     Dbprintf("HT_FAST_ADVANCED");
+
                 tag.mode = HT_FAST_ADVANCED;
                 sof_bits = 3;
                 m = AC4K;
             }
             //send uid as a response
             *txlen = 32;
-            for (int i = 0; i < 4; i++)
-                tx[i] = (tag.uid >> (24 - (i * 8))) & 0xff;
+            for (int i = 0; i < 4; i++) {
+                tx[i] = (tag.uid >> (24 - (i * 8))) & 0xFF;
+            }
         }
         break;
         case 45: {
             //select command from reader received
             if (g_dbglevel >= DBG_EXTENDED)
                 DbpString("SELECT");
+
             if (check_select(rx, tag.uid) == 1) {
                 if (g_dbglevel >= DBG_EXTENDED)
                     DbpString("SELECT match");
+
                 //if the right tag was selected
                 *txlen = 32;
                 hitagS_set_frame_modulation();
 
                 //send configuration
-                for (int i = 0; i < 4; i++)
+                for (int i = 0; i < 4; i++) {
                     tx[i] = tag.pages[1][i];
+                }
+
                 tx[3] = 0xff;
+
                 if (tag.mode != HT_STANDARD) {
                     *txlen = 40;
                     crc = CRC_PRESET;
-                    for (int i = 0; i < 4; i++)
+                    for (int i = 0; i < 4; i++) {
                         calc_crc(&crc, tx[i], 8);
+                    }
+
                     tx[4] = crc;
                 }
             }
@@ -435,18 +450,22 @@ static void hitagS_handle_reader_command(uint8_t *rx, const size_t rxlen,
                                  REV32((rx[3] << 24) + (rx[2] << 16) + (rx[1] << 8) + rx[0])
                                 );
             Dbprintf(",{0x%02X, 0x%02X, 0x%02X, 0x%02X, 0x%02X, 0x%02X, 0x%02X, 0x%02X}",
-                     rx[0], rx[1], rx[2], rx[3], rx[4], rx[5], rx[6], rx[7]);
+                     rx[0], rx[1], rx[2], rx[3], 
+                     rx[4], rx[5], rx[6], rx[7]
+                    );
 
             hitagS_set_frame_modulation();
 
-            for (int i = 0; i < 4; i++)
+            for (int i = 0; i < 4; i++) {
                 _hitag2_byte(&state);
+            }
 
             //send con2, pwdh0, pwdl0, pwdl1 encrypted as a response
             tx[0] = _hitag2_byte(&state) ^ tag.pages[1][2];
             tx[1] = _hitag2_byte(&state) ^ tag.pwdh0;
             tx[2] = _hitag2_byte(&state) ^ tag.pwdl0;
             tx[3] = _hitag2_byte(&state) ^ tag.pwdl1;
+
             if (tag.mode != HT_STANDARD) {
                 //add crc8
                 *txlen = 40;
@@ -492,6 +511,7 @@ static void hitagS_handle_reader_command(uint8_t *rx, const size_t rxlen,
                 tx[0] = 0x40;
                 page_to_be_written = 0;
                 hitagS_set_frame_modulation();
+
             } else if (tag.tstate == HT_WRITING_BLOCK_DATA) {
                 tag.pages[page_to_be_written][0] = rx[0];
                 tag.pages[page_to_be_written][1] = rx[1];
@@ -503,6 +523,7 @@ static void hitagS_handle_reader_command(uint8_t *rx, const size_t rxlen,
                 hitagS_set_frame_modulation();
                 page_to_be_written++;
                 block_data_left--;
+
                 if (block_data_left == 0) {
                     tag.tstate = HT_NO_OP;
                     page_to_be_written = 0;
@@ -519,8 +540,10 @@ static void hitagS_handle_reader_command(uint8_t *rx, const size_t rxlen,
                 tx[1] = tag.pages[page][1];
                 tx[2] = tag.pages[page][2];
                 tx[3] = tag.pages[page][3];
-                if (tag.LKP && page == 1)
-                    tx[3] = 0xff;
+
+                if (tag.LKP && page == 1) {
+                    tx[3] = 0xFF;
+                }
 
                 hitagS_set_frame_modulation();
 
@@ -528,8 +551,9 @@ static void hitagS_handle_reader_command(uint8_t *rx, const size_t rxlen,
                     //add crc8
                     *txlen = 40;
                     crc = CRC_PRESET;
-                    for (int i = 0; i < 4; i++)
+                    for (int i = 0; i < 4; i++) {
                         calc_crc(&crc, tx[i], 8);
+                    }
                     tx[4] = crc;
                 }
 
@@ -555,8 +579,9 @@ static void hitagS_handle_reader_command(uint8_t *rx, const size_t rxlen,
                     //add crc8
                     *txlen = 32 * 4 + 8;
                     crc = CRC_PRESET;
-                    for (int i = 0; i < 16; i++)
+                    for (int i = 0; i < 16; i++) {
                         calc_crc(&crc, tx[i], 8);
+                    }
                     tx[16] = crc;
                 }
 
@@ -623,22 +648,27 @@ static int hitagS_handle_tag_auth(hitag_function htf, uint64_t key, uint64_t NrA
             Dbprintf("authentication failed!");
             return -1;
         }
+
         int z = 0;
         for (int i = 0; i < 10; i++) {
             for (int j = 0; j < 8; j++) {
                 response_bit[z] = 0;
-                if ((rx[i] & ((mask << 7) >> j)) != 0)
+                if ((rx[i] & ((mask << 7) >> j)) != 0) {
                     response_bit[z] = 1;
+                }
                 z++;
             }
         }
+
         uint16_t k = 0;
         for (int i = 5; i < z; i += 2) {
             uid[k] = response_bit[i];
             k++;
-            if (k > 31)
+            if (k > 31) {
                 break;
+            }
         }
+
         uint8_t uid1 = (uid[0] << 7)
                        | (uid[1] << 6)
                        | (uid[2] << 5)
@@ -704,8 +734,9 @@ static int hitagS_handle_tag_auth(hitag_function htf, uint64_t key, uint64_t NrA
 
             for (int j = 0; j < 8; j++) {
                 response_bit[i] = 0;
-                if ((crc & ((mask << 7) >> j)) != 0)
+                if ((crc & ((mask << 7) >> j)) != 0){
                     response_bit[i] = 1;
+                }
                 i++;
             }
         }
@@ -730,8 +761,9 @@ static int hitagS_handle_tag_auth(hitag_function htf, uint64_t key, uint64_t NrA
         for (int i = 0; i < 6; i++) {
             for (int j = 0; j < 8; j++) {
                 response_bit[z] = 0;
-                if ((rx[i] & ((mask << 7) >> j)) != 0)
+                if ((rx[i] & ((mask << 7) >> j)) != 0) {
                     response_bit[z] = 1;
+                }
                 z++;
             }
         }
@@ -739,27 +771,37 @@ static int hitagS_handle_tag_auth(hitag_function htf, uint64_t key, uint64_t NrA
                          | (response_bit[6] << 5) | (response_bit[7] << 4)
                          | (response_bit[8] << 3) | (response_bit[9] << 2)
                          | (response_bit[10] << 1) | response_bit[11]);
+
         //check which memorysize this tag has
-        if (response_bit[10] == 0 && response_bit[11] == 0)
+        if (response_bit[10] == 0 && response_bit[11] == 0) {
             tag.max_page = 32 / 32;
-        if (response_bit[10] == 0 && response_bit[11] == 1)
+        }
+
+        if (response_bit[10] == 0 && response_bit[11] == 1) {
             tag.max_page = 256 / 32;
-        if (response_bit[10] == 1 && response_bit[11] == 0)
+        }
+
+        if (response_bit[10] == 1 && response_bit[11] == 0) {
             tag.max_page = 2048 / 32;
+        }
+
         conf_pages[1] = ((response_bit[12] << 7) | (response_bit[13] << 6)
                          | (response_bit[14] << 5) | (response_bit[15] << 4)
                          | (response_bit[16] << 3) | (response_bit[17] << 2)
                          | (response_bit[18] << 1) | response_bit[19]);
+
         tag.auth = response_bit[12];
         tag.TTFC = response_bit[13];
         //tag.TTFDR in response_bit[14] and response_bit[15]
         //tag.TTFM in response_bit[16] and response_bit[17]
         tag.LCON = response_bit[18];
         tag.LKP = response_bit[19];
+
         conf_pages[2] = ((response_bit[20] << 7) | (response_bit[21] << 6)
                          | (response_bit[22] << 5) | (response_bit[23] << 4)
                          | (response_bit[24] << 3) | (response_bit[25] << 2)
                          | (response_bit[26] << 1) | response_bit[27]);
+
         tag.LCK7 = response_bit[20];
         tag.LCK6 = response_bit[21];
         tag.LCK5 = response_bit[22];
@@ -782,6 +824,7 @@ static int hitagS_handle_tag_auth(hitag_function htf, uint64_t key, uint64_t NrA
                     for (int i = 0; i < 4; i++) {
                         auth_ks[i] = _hitag2_byte(&state) ^ 0xff;
                     }
+
                     *txlen = 64;
                     tx[0] = rnd & 0xff;
                     tx[1] = (rnd >> 8) & 0xff;
@@ -795,9 +838,11 @@ static int hitagS_handle_tag_auth(hitag_function htf, uint64_t key, uint64_t NrA
                     if (g_dbglevel >= DBG_EXTENDED)
                         Dbprintf("%02X %02X %02X %02X %02X %02X %02X %02X", tx[0],
                                  tx[1], tx[2], tx[3], tx[4], tx[5], tx[6], tx[7]);
+
                 } else if (htf == 01 || htf == 03) { //RHTS_CHALLENGE //WHTS_CHALLENGE
-                    for (int i = 0; i < 8; i++)
-                        tx[i] = ((NrAr >> (56 - (i * 8))) & 0xff);
+                    for (int i = 0; i < 8; i++) {
+                        tx[i] = ((NrAr >> (56 - (i * 8))) & 0xFF);
+                    }
                 }
                 end = true;
                 tag.pstate = HT_AUTHENTICATE;
@@ -829,8 +874,9 @@ static int hitagS_handle_tag_auth(hitag_function htf, uint64_t key, uint64_t NrA
         if (htf == 02 || htf == 04) { //RHTS_KEY //WHTS_KEY
             {
                 state = _hitag2_init(REV64(key), REV32(tag.uid), REV32(rnd));
-                for (int i = 0; i < 5; i++)
+                for (int i = 0; i < 5; i++) {
                     _hitag2_byte(&state);
+                }
 
                 pwdh0 = ((rx[1] & 0x0f) * 16 + ((rx[2] & 0xf0) / 16)) ^ _hitag2_byte(&state);
                 pwdl0 = ((rx[2] & 0x0f) * 16 + ((rx[3] & 0xf0) / 16)) ^ _hitag2_byte(&state);
@@ -884,9 +930,11 @@ void SimulateHitagSTag(bool tag_mem_supplied, uint8_t *data, bool ledcontrol) {
 
     // read tag data into memory
     if (tag_mem_supplied) {
-        for (int i = 0; i < 16; i++)
-            for (int j = 0; j < 4; j++)
+        for (int i = 0; i < 16; i++) {
+            for (int j = 0; j < 4; j++) {
                 tag.pages[i][j] = 0x0;
+            }
+        }
 
         DbpString("Loading hitagS memory...");
         memcpy((uint8_t *)tag.pages, data, 4 * 64);
@@ -901,58 +949,87 @@ void SimulateHitagSTag(bool tag_mem_supplied, uint8_t *data, bool ledcontrol) {
               (((uint64_t)tag.pages[3][0]) << 16) |
               (((uint64_t)tag.pages[2][3]) << 8) |
               (((uint64_t)tag.pages[2][2]));
+
     tag.pwdl0 = tag.pages[2][0];
     tag.pwdl1 = tag.pages[2][1];
     tag.pwdh0 = tag.pages[1][3];
     //con0
     tag.max_page = 64;
-    if ((tag.pages[1][0] & 0x2) == 0 && (tag.pages[1][0] & 0x1) == 1)
+
+    if ((tag.pages[1][0] & 0x2) == 0 && (tag.pages[1][0] & 0x1) == 1) {
         tag.max_page = 8;
-    if ((tag.pages[1][0] & 0x2) == 0 && (tag.pages[1][0] & 0x1) == 0)
+    }
+
+    if ((tag.pages[1][0] & 0x2) == 0 && (tag.pages[1][0] & 0x1) == 0) {
         tag.max_page = 0;
+    }
+
     if (g_dbglevel >= DBG_EXTENDED)
-        for (int i = 0; i < tag.max_page; i++)
+        for (int i = 0; i < tag.max_page; i++) {
             Dbprintf("Page[%2d]: %02X %02X %02X %02X", i,
-                     (tag.pages[i][3]) & 0xff,
-                     (tag.pages[i][2]) & 0xff,
-                     (tag.pages[i][1]) & 0xff,
-                     tag.pages[i][0] & 0xff);
+                     (tag.pages[i][3]) & 0xFF,
+                     (tag.pages[i][2]) & 0xFF,
+                     (tag.pages[i][1]) & 0xFF,
+                     tag.pages[i][0] & 0xFF
+                    );
+        }
     //con1
     tag.auth = 0;
-    if ((tag.pages[1][1] & 0x80) == 0x80)
+    if ((tag.pages[1][1] & 0x80) == 0x80) {
         tag.auth = 1;
+    }
+
     tag.LCON = 0;
-    if ((tag.pages[1][1] & 0x2) == 0x02)
+    if ((tag.pages[1][1] & 0x2) == 0x02) {
         tag.LCON = 1;
+    }
+
     tag.LKP = 0;
-    if ((tag.pages[1][1] & 0x1) == 0x01)
+    if ((tag.pages[1][1] & 0x1) == 0x01) {
         tag.LKP = 1;
+    }
+
     //con2
     //0=read write 1=read only
     tag.LCK7 = 0;
-    if ((tag.pages[1][2] & 0x80) == 0x80)
+    if ((tag.pages[1][2] & 0x80) == 0x80) {
         tag.LCK7 = 1;
+    }
+
     tag.LCK6 = 0;
-    if ((tag.pages[1][2] & 0x40) == 0x040)
+    if ((tag.pages[1][2] & 0x40) == 0x040) {
         tag.LCK6 = 1;
+    }
+
     tag.LCK5 = 0;
-    if ((tag.pages[1][2] & 0x20) == 0x20)
+    if ((tag.pages[1][2] & 0x20) == 0x20) {
         tag.LCK5 = 1;
+    }
+
     tag.LCK4 = 0;
-    if ((tag.pages[1][2] & 0x10) == 0x10)
+    if ((tag.pages[1][2] & 0x10) == 0x10) {
         tag.LCK4 = 1;
+    }
+
     tag.LCK3 = 0;
-    if ((tag.pages[1][2] & 0x8) == 0x08)
+    if ((tag.pages[1][2] & 0x8) == 0x08) {
         tag.LCK3 = 1;
+    }
+
     tag.LCK2 = 0;
-    if ((tag.pages[1][2] & 0x4) == 0x04)
+    if ((tag.pages[1][2] & 0x4) == 0x04) {
         tag.LCK2 = 1;
+    }
+
     tag.LCK1 = 0;
-    if ((tag.pages[1][2] & 0x2) == 0x02)
+    if ((tag.pages[1][2] & 0x2) == 0x02) {
         tag.LCK1 = 1;
+    }
+
     tag.LCK0 = 0;
-    if ((tag.pages[1][2] & 0x1) == 0x01)
+    if ((tag.pages[1][2] & 0x1) == 0x01) {
         tag.LCK0 = 1;
+    }
 
 
     // Set up simulator mode, frequency divisor which will drive the FPGA
@@ -1198,15 +1275,28 @@ void ReadHitagS(hitag_function htf, hitag_data *htd, bool ledcontrol) {
             DbpString("Authenticating using nr,ar pair:");
             memcpy(NrAr_, htd->auth.NrAr, 8);
             Dbhexdump(8, NrAr_, false);
-            NrAr = NrAr_[7] | ((uint64_t)NrAr_[6]) << 8 | ((uint64_t)NrAr_[5]) << 16 | ((uint64_t)NrAr_[4]) << 24 | ((uint64_t)NrAr_[3]) << 32 |
-                   ((uint64_t)NrAr_[2]) << 40 | ((uint64_t)NrAr_[1]) << 48 | ((uint64_t)NrAr_[0]) << 56;
+            NrAr = NrAr_[7] | 
+                   ((uint64_t)NrAr_[6]) <<  8 |
+                   ((uint64_t)NrAr_[5]) << 16 |
+                   ((uint64_t)NrAr_[4]) << 24 |
+                   ((uint64_t)NrAr_[3]) << 32 |
+                   ((uint64_t)NrAr_[2]) << 40 |
+                   ((uint64_t)NrAr_[1]) << 48 |
+                   ((uint64_t)NrAr_[0]) << 56
+                ;
             break;
         }
         case RHTSF_KEY: {
             DbpString("Authenticating using key:");
             memcpy(key_, htd->crypto.key, 6);
             Dbhexdump(6, key_, false);
-            key = key_[5] | ((uint64_t)key_[4]) << 8 | ((uint64_t)key_[3]) << 16 | ((uint64_t)key_[2]) << 24 | ((uint64_t)key_[1]) << 32 | ((uint64_t)key_[0]) << 40;
+            key = key_[5] |
+                  ((uint64_t)key_[4]) <<  8 |
+                  ((uint64_t)key_[3]) << 16 |
+                  ((uint64_t)key_[2]) << 24 |
+                  ((uint64_t)key_[1]) << 32 |
+                  ((uint64_t)key_[0]) << 40
+                ;
             break;
         }
         default: {
@@ -1322,6 +1412,7 @@ void ReadHitagS(hitag_function htf, hitag_data *htd, bool ledcontrol) {
             calc_crc(&crc, 0x00 + ((pageNum % 16) * 16), 4);
             tx[1] = 0x00 + ((pageNum % 16) * 16) + (crc / 16);
             tx[2] = 0x00 + (crc % 16) * 16;
+
         } else if (tag.pstate == HT_SELECTED
                    && tag.tstate == HT_READING_PAGE
                    && rxlen > 0) {
@@ -1329,6 +1420,7 @@ void ReadHitagS(hitag_function htf, hitag_data *htd, bool ledcontrol) {
             for (i = 0; i < 4 && i < k; i++) {   // set page bytes from received bits
                 tag.pages[pageNum][i] = response_bytes[i];
             }
+
             if (tag.auth && tag.LKP && pageNum == 1) {
                 Dbprintf("Page[%2d]: %02X %02X %02X %02X", pageNum, pwdh0,
                          (tag.pages[pageNum][2]) & 0xff,
@@ -1454,8 +1546,15 @@ void WritePageHitagS(hitag_function htf, hitag_data *htd, int page, bool ledcont
             DbpString("Authenticating using nr,ar pair:");
             memcpy(NrAr_, htd->auth.NrAr, 8);
             Dbhexdump(8, NrAr_, false);
-            NrAr = NrAr_[7] | ((uint64_t)NrAr_[6]) << 8 | ((uint64_t)NrAr_[5]) << 16 | ((uint64_t)NrAr_[4]) << 24 | ((uint64_t)NrAr_[3]) << 32 |
-                   ((uint64_t)NrAr_[2]) << 40 | ((uint64_t)NrAr_[1]) << 48 | ((uint64_t)NrAr_[0]) << 56;
+            NrAr = NrAr_[7] |
+                   ((uint64_t)NrAr_[6]) <<  8 |
+                   ((uint64_t)NrAr_[5]) << 16 |
+                   ((uint64_t)NrAr_[4]) << 24 |
+                   ((uint64_t)NrAr_[3]) << 32 |
+                   ((uint64_t)NrAr_[2]) << 40 |
+                   ((uint64_t)NrAr_[1]) << 48 |
+                   ((uint64_t)NrAr_[0]) << 56
+                ;
             break;
         }
 
@@ -1464,7 +1563,13 @@ void WritePageHitagS(hitag_function htf, hitag_data *htd, int page, bool ledcont
             DbpString("Authenticating using key:");
             memcpy(key_, htd->crypto.key, 6);
             Dbhexdump(6, key_, false);
-            key = key_[5] | ((uint64_t)key_[4]) << 8 | ((uint64_t)key_[3]) << 16 | ((uint64_t)key_[2]) << 24 | ((uint64_t)key_[1]) << 32 | ((uint64_t)key_[0]) << 40;
+            key = key_[5] |
+                  ((uint64_t)key_[4]) <<  8 |
+                  ((uint64_t)key_[3]) << 16 |
+                  ((uint64_t)key_[2]) << 24 |
+                  ((uint64_t)key_[1]) << 32 |
+                  ((uint64_t)key_[0]) << 40
+                ;
             break;
         }
         default: {
@@ -1533,6 +1638,7 @@ void WritePageHitagS(hitag_function htf, hitag_data *htd, int page, bool ledcont
             for (i = 0; i < rxlen; i++) {
                 response_bit[i] = (rx[i / 8] >> (7 - (i % 8))) & 1;
             }
+
             unsigned char response_bytes[ARRAYLEN(response_bit) / 8] = {0};
             if (tag.pstate == HT_INIT) {
                 // Tag Response is AC encoded
@@ -1768,8 +1874,9 @@ void check_challenges(bool file_given, uint8_t *data, bool ledcontrol) {
             for (i = 0; i < 10; i++) {
                 for (j = 0; j < 8; j++) {
                     response_bit[z] = 0;
-                    if ((rx[i] & ((mask << 7) >> j)) != 0)
+                    if ((rx[i] & ((mask << 7) >> j)) != 0) {
                         response_bit[z] = 1;
+                    }
                     z++;
                 }
             }
@@ -1780,18 +1887,18 @@ void check_challenges(bool file_given, uint8_t *data, bool ledcontrol) {
                 if (k > 31)
                     break;
             }
-            uid_byte[0] = (uid[0] << 7) | (uid[1] << 6) | (uid[2] << 5)
-                          | (uid[3] << 4) | (uid[4] << 3) | (uid[5] << 2)
-                          | (uid[6] << 1) | uid[7];
-            uid_byte[1] = (uid[8] << 7) | (uid[9] << 6) | (uid[10] << 5)
-                          | (uid[11] << 4) | (uid[12] << 3) | (uid[13] << 2)
-                          | (uid[14] << 1) | uid[15];
-            uid_byte[2] = (uid[16] << 7) | (uid[17] << 6) | (uid[18] << 5)
-                          | (uid[19] << 4) | (uid[20] << 3) | (uid[21] << 2)
-                          | (uid[22] << 1) | uid[23];
-            uid_byte[3] = (uid[24] << 7) | (uid[25] << 6) | (uid[26] << 5)
-                          | (uid[27] << 4) | (uid[28] << 3) | (uid[29] << 2)
-                          | (uid[30] << 1) | uid[31];
+            uid_byte[0] = (uid[0] << 7) | (uid[1] << 6) | (uid[2] << 5) | (uid[3] << 4) |
+                          (uid[4] << 3) | (uid[5] << 2) | (uid[6] << 1) | uid[7];
+
+            uid_byte[1] = (uid[8] << 7) | (uid[9] << 6) | (uid[10] << 5) | (uid[11] << 4) |
+                          (uid[12] << 3) | (uid[13] << 2) | (uid[14] << 1) | uid[15];
+
+            uid_byte[2] = (uid[16] << 7) | (uid[17] << 6) | (uid[18] << 5) | (uid[19] << 4) |
+                          (uid[20] << 3) | (uid[21] << 2) | (uid[22] << 1) | uid[23];
+
+            uid_byte[3] = (uid[24] << 7) | (uid[25] << 6) | (uid[26] << 5) | (uid[27] << 4) |
+                          (uid[28] << 3) | (uid[29] << 2) | (uid[30] << 1) | uid[31];
+
             //Dbhexdump(10, rx, rxlen);
             STATE = 1;
             txlen = 45;
@@ -1801,29 +1908,36 @@ void check_challenges(bool file_given, uint8_t *data, bool ledcontrol) {
             calc_crc(&crc, uid_byte[1], 8);
             calc_crc(&crc, uid_byte[2], 8);
             calc_crc(&crc, uid_byte[3], 8);
+
             for (i = 0; i < 100; i++) {
                 response_bit[i] = 0;
             }
+
             for (i = 0; i < 5; i++) {
                 response_bit[i] = 0;
             }
+
             for (i = 5; i < 37; i++) {
                 response_bit[i] = uid[i - 5];
             }
+
             for (j = 0; j < 8; j++) {
                 response_bit[i] = 0;
-                if ((crc & ((mask << 7) >> j)) != 0)
+                if ((crc & ((mask << 7) >> j)) != 0) {
                     response_bit[i] = 1;
+                }
                 i++;
             }
             k = 0;
             for (i = 0; i < 6; i++) {
-                tx[i] = (response_bit[k] << 7) | (response_bit[k + 1] << 6)
+                tx[i] = (response_bit[k] << 7)
+                        | (response_bit[k + 1] << 6)
                         | (response_bit[k + 2] << 5)
                         | (response_bit[k + 3] << 4)
                         | (response_bit[k + 4] << 3)
                         | (response_bit[k + 5] << 2)
-                        | (response_bit[k + 6] << 1) | response_bit[k + 7];
+                        | (response_bit[k + 6] << 1)
+                        | response_bit[k + 7];
                 k += 8;
             }
 
@@ -1834,8 +1948,9 @@ void check_challenges(bool file_given, uint8_t *data, bool ledcontrol) {
             for (i = 0; i < 6; i++) {
                 for (j = 0; j < 8; j++) {
                     response_bit[z] = 0;
-                    if ((rx[i] & ((mask << 7) >> j)) != 0)
+                    if ((rx[i] & ((mask << 7) >> j)) != 0) {
                         response_bit[z] = 1;
+                    }
                     z++;
                 }
             }
@@ -1843,8 +1958,10 @@ void check_challenges(bool file_given, uint8_t *data, bool ledcontrol) {
 
             if (u1 >= ARRAYLEN(unlocker))
                 bStop = !false;
-            for (i = 0; i < 8; i++)
+
+            for (i = 0; i < 8; i++) {
                 tx[i] = unlocker[u1][i];
+            }
             u1++;
 
         } else if (STATE == 2 && rxlen >= 44) {
