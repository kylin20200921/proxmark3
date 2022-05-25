commit 6a444eb20880760d5fbbcf51557e90376c37d276
Author: iceman1001 <iceman@iuse.se>
Date:   Thu May 20 10:07:51 2021 +0200

    style clean

diff --git a/include/pm3_cmd.h b/include/pm3_cmd.h
index 97dc91e49..ed96c8467 100644
--- a/include/pm3_cmd.h
+++ b/include/pm3_cmd.h
@@ -145,17 +145,7 @@ typedef struct {
 #define TRACELOG_HDR_LEN        sizeof(tracelog_hdr_t)
 #define TRACELOG_PARITY_LEN(x)  (((x)->data_len - 1) / 8 + 1)
 
-/*
-typedef struct {
-    uint16_t start_gap;
-    uint16_t write_gap;
-    uint16_t write_0;
-    uint16_t write_1;
-    uint16_t read_gap;
-} t55xx_config;
-*/
-
-// Extended to support 1 of 4 timing
+// T55XX - Extended to support 1 of 4 timing
 typedef struct  {
     uint16_t start_gap;
     uint16_t write_gap;
@@ -166,22 +156,14 @@ typedef struct  {
     uint16_t write_3;
 } t55xx_config_t;
 
-// This setup will allow for the 4 downlink modes "m" as well as other items if needed.
+// T55XX - This setup will allow for the 4 downlink modes "m" as well as other items if needed.
 // Given the one struct we can then read/write to flash/client in one go.
 typedef struct {
     t55xx_config_t m[4]; // mode
 } t55xx_configurations_t;
 
-/*typedef struct  {
-    uint16_t start_gap [4];
-    uint16_t write_gap [4];
-    uint16_t write_0   [4];
-    uint16_t write_1   [4];
-    uint16_t write_2   [4];
-    uint16_t write_3   [4];
-    uint16_t read_gap  [4];
-} t55xx_config;
-*/
+
+// Capabilities struct to keep track of what functions was compiled in the device firmware
 typedef struct {
     uint8_t version;
     uint32_t baudrate;
