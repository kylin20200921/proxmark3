commit dd08c69f7e8e29483b7f46bd0f4605945591b3c6
Author: iceman1001 <iceman@iuse.se>
Date:   Wed Dec 22 21:09:28 2021 +0100

    cleaner

diff --git a/client/src/uart/uart_posix.c b/client/src/uart/uart_posix.c
index 8b4c73cc2..8d1547913 100644
--- a/client/src/uart/uart_posix.c
+++ b/client/src/uart/uart_posix.c
@@ -161,17 +161,15 @@ serial_port uart_open(const char *pcPortName, uint32_t speed) {
             close(sfd);
         }
 
+        freeaddrinfo(addr);
+        free(addrstr);
+
         if (rp == NULL) {               /* No address succeeded */
             PrintAndLogEx(ERR, "error: Could not connect");
-            freeaddrinfo(addr);
-            free(addrstr);
             free(sp);
             return INVALID_SERIAL_PORT;
         }
 
-        freeaddrinfo(addr);
-        free(addrstr);
-
         sp->fd = sfd;
 
         int one = 1;
