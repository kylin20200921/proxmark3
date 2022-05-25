commit 7510fcee4f399a1dc23d3ecdb34d42aa8f0878aa
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Sep 17 18:28:12 2021 +0200

    text

diff --git a/client/src/mifare/mifare4.c b/client/src/mifare/mifare4.c
index dcbc9ec6b..b7ceb0929 100644
--- a/client/src/mifare/mifare4.c
+++ b/client/src/mifare/mifare4.c
@@ -158,7 +158,7 @@ int CalculateMAC(mf4Session_t *mf4session, MACType_t mtype, uint8_t blockNum, ui
     }
 
     if (verbose)
-        PrintAndLogEx(NORMAL, "MAC data[%d]: %s", macdatalen, sprint_hex(macdata, macdatalen));
+        PrintAndLogEx(INFO, "MAC data[%d]: %s", macdatalen, sprint_hex(macdata, macdatalen));
 
     return aes_cmac8(NULL, mf4session->Kmac, macdata, mac, macdatalen);
 }
