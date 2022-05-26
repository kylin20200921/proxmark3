commit 9abb026d00794c5db719a1bbe411bef639f8257b
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Feb 13 20:53:32 2022 +0100

    cleaning out unused SendCommandOLD in lua scripting

diff --git a/client/src/scripting.c b/client/src/scripting.c
index e93e223d1..a12a53fd3 100644
--- a/client/src/scripting.c
+++ b/client/src/scripting.c
@@ -94,57 +94,6 @@ static int l_fast_push_mode(lua_State *L) {
     return 1;
 }
 
-/**
- * The following params expected:
- * @brief l_SendCommandOLD
- * @param L - a lua string with the following five params.
- * @param cmd  must be hexstring, max u64
- * @param arg0  must be hexstring, max u64
- * @param arg1  must be hexstring, max u64
- * @param arg2  must be hexstring, max u64
- * @param data  must be hexstring less than 1024 chars(512bytes)
- * @return
- */
-static int l_SendCommandOLD(lua_State *L) {
-//  SendCommandMIX(CMD_HF_SNIFF, skippairs, skiptriggers, 0, NULL, 0);
-// (uint64_t cmd, uint64_t arg0, uint64_t arg1, uint64_t arg2, void *data, size_t len)
-
-    uint64_t cmd, arg0, arg1, arg2;
-    uint8_t data[PM3_CMD_DATA_SIZE] = {0};
-    size_t len = 0, size;
-
-    //Check number of arguments
-    int n = lua_gettop(L);
-    if (n != 5)  {
-        return returnToLuaWithError(L, "You need to supply five parameters");
-    }
-
-    // parse input
-    cmd = luaL_checknumber(L, 1);
-    arg0 = luaL_checknumber(L, 2);
-    arg1 = luaL_checknumber(L, 3);
-    arg2 = luaL_checknumber(L, 4);
-
-    // data
-    const char *p_data = luaL_checklstring(L, 5, &size);
-    if (size) {
-        if (size > 1024)
-            size = 1024;
-
-        uint32_t tmp;
-        for (int i = 0; i < size; i += 2) {
-            sscanf(&p_data[i], "%02x", &tmp);
-            data[i >> 1] = tmp & 0xFF;
-            len++;
-        }
-    }
-
-    clearCommandBuffer();
-    SendCommandOLD(cmd, arg0, arg1, arg2, data, len);
-    lua_pushboolean(L, true);
-    return 1;
-}
-
 /**
  * The following params expected:
  * @brief l_SendCommandMIX
@@ -1363,7 +1312,6 @@ static int setLuaPath(lua_State *L, const char *path) {
 
 int set_pm3_libraries(lua_State *L) {
     static const luaL_Reg libs[] = {
-        {"SendCommandOLD",              l_SendCommandOLD},
         {"SendCommandMIX",              l_SendCommandMIX},
         {"SendCommandNG",               l_SendCommandNG},
         {"GetFromBigBuf",               l_GetFromBigBuf},
