commit 7bfc3d8f58de5bfeec67016c1ef388e7c178bbcd
Author: iceman1001 <iceman@iuse.se>
Date:   Tue Jun 15 11:38:59 2021 +0200

    fix spiffs comms

diff --git a/client/src/scripting.c b/client/src/scripting.c
index 37769bffe..0184c78a5 100644
--- a/client/src/scripting.c
+++ b/client/src/scripting.c
@@ -342,12 +342,12 @@ static int l_GetFromFlashMemSpiffs(lua_State *L) {
         return returnToLuaWithError(L, "Filename missing or invalid");
 
     // get size from spiffs itself !
-    SendCommandMIX(CMD_SPIFFS_STAT, 0, 0, 0, (uint8_t *)destfilename, 32);
+    SendCommandNG(CMD_SPIFFS_STAT, (uint8_t *)destfilename, 32);
     PacketResponseNG resp;
-    if (!WaitForResponseTimeout(CMD_ACK, &resp, 2000))
+    if (!WaitForResponseTimeout(CMD_SPIFFS_STAT, &resp, 2000))
         return returnToLuaWithError(L, "No response from the device");
 
-    len = resp.oldarg[0];
+    len = resp.data.asDwords[0];
 
     if (len == 0)
         return returnToLuaWithError(L, "Filename invalid or empty");
