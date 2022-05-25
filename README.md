commit 0e7e13db7dd608ac5cf3c40db172ae054eeed23e
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jun 20 23:02:18 2021 +0200

    fix for client crash in lf hitag eload.  Hitag simulation still now working (wip)

diff --git a/armsrc/appmain.c b/armsrc/appmain.c
index a419617d1..af5c01c72 100644
--- a/armsrc/appmain.c
+++ b/armsrc/appmain.c
@@ -1114,15 +1114,9 @@ static void PacketReceived(PacketCommandNG *packet) {
             break;
         }
         case CMD_LF_HITAG_ELOAD: {
-            /*
-            struct p {
-                uint16_t len;
-                uint8_t *data;
-            } PACKED;
-            struct p *payload = (struct p *) packet->data.asBytes;
+            lf_hitag_t *payload = (lf_hitag_t *) packet->data.asBytes;
             uint8_t *mem = BigBuf_get_EM_addr();
-            memcpy((uint8_t *)mem.sectors, payload->data, payload->len);
-            */
+            memcpy((uint8_t *)mem, payload->data, payload->len);
             break;
         }
 #endif
diff --git a/client/src/cmdlfhitag.c b/client/src/cmdlfhitag.c
index 232acf068..287f39f06 100644
--- a/client/src/cmdlfhitag.c
+++ b/client/src/cmdlfhitag.c
@@ -210,14 +210,15 @@ static int CmdLFHitagEload(const char *Cmd) {
     CLIParserContext *ctx;
     CLIParserInit(&ctx, "lf hitag eload",
                   "Loads hitag tag dump into emulator memory on device",
-                  "lf hitag eload -f lf-hitag-11223344-dump.bin\n");
+                  "lf hitag eload -2 -f lf-hitag-11223344-dump.bin\n");
 
     void *argtable[] = {
         arg_param_begin,
         arg_str1("f", "file", "<filename>", "filename of dump"),
-        arg_lit0("1", NULL, "simulate Hitag1"),
-        arg_lit0("2", NULL, "simulate Hitag2"),
-        arg_lit0("s", NULL, "simulate HitagS"),
+        arg_lit0("1", NULL, "Card type Hitag1"),
+        arg_lit0("2", NULL, "Card type Hitag2"),
+        arg_lit0("s", NULL, "Card type HitagS"),
+        arg_lit0("m", NULL, "Card type HitagM"),
         arg_param_end
     };
     CLIExecWithReturn(ctx, Cmd, argtable, false);
@@ -229,9 +230,10 @@ static int CmdLFHitagEload(const char *Cmd) {
     bool use_ht1 = arg_get_lit(ctx, 2);
     bool use_ht2 = arg_get_lit(ctx, 3);
     bool use_hts = arg_get_lit(ctx, 4);
+    bool use_htm = arg_get_lit(ctx, 5);
     CLIParserFree(ctx);
 
-    uint8_t n = (use_ht1 + use_ht2 + use_hts);
+    uint8_t n = (use_ht1 + use_ht2 + use_hts + use_htm);
     if (n != 1) {
         PrintAndLogEx(ERR, "error, only specify one Hitag type");
         return PM3_EINVARG;
@@ -274,15 +276,24 @@ static int CmdLFHitagEload(const char *Cmd) {
 
     // check dump len..
     if (dumplen == 48 ||  dumplen == 4 * 64) {
-        struct {
-            uint16_t len;
-            uint8_t *data;
-        } PACKED payload;
-        payload.len = dumplen;
-        memcpy(payload.data, dump, dumplen);
+
+        lf_hitag_t *payload =  calloc(1, sizeof(lf_hitag_t) + dumplen);
+        
+        if (use_ht1)
+            payload->type = 1;
+        if (use_ht2)
+            payload->type = 2;
+        if (use_hts)
+            payload->type = 3;
+        if (use_htm)
+            payload->type = 4;
+
+        payload->len = dumplen;
+        memcpy(payload->data, dump, dumplen);
 
         clearCommandBuffer();
-        SendCommandNG(CMD_LF_HITAG_ELOAD, (uint8_t *)&payload, 2 + dumplen);
+        SendCommandNG(CMD_LF_HITAG_ELOAD, (uint8_t *)payload, 3 + dumplen);
+        free(payload);
     } else {
         PrintAndLogEx(ERR, "error, wrong dump file size. got %zu", dumplen);
     }
diff --git a/include/pm3_cmd.h b/include/pm3_cmd.h
index ed96c8467..4e89e4f86 100644
--- a/include/pm3_cmd.h
+++ b/include/pm3_cmd.h
@@ -259,6 +259,12 @@ typedef struct {
     uint8_t data[];
 } PACKED lf_nrzsim_t;
 
+typedef struct {
+    uint8_t type;
+    uint16_t len;
+    uint8_t *data;
+} PACKED lf_hitag_t;
+
 typedef struct {
     uint8_t blockno;
     uint8_t keytype;
