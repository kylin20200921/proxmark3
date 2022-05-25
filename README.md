commit 70694ff28185647de4fd8a08d893fdf3b50b07be
Author: iceman1001 <iceman@iuse.se>
Date:   Mon May 17 20:35:11 2021 +0200

    style

diff --git a/client/src/util.c b/client/src/util.c
index c18917f08..c8cc26752 100644
--- a/client/src/util.c
+++ b/client/src/util.c
@@ -305,7 +305,7 @@ char *sprint_bin_break(const uint8_t *data, const size_t len, const uint8_t brea
         *(tmp++) = c;
 
         // check if a line break is needed and we have room to print it in our array
-        if (breaks) {
+        if (breaks > 1) {
             if (((i + 1) % breaks) == 0) {
 
                 *(tmp++) = '\n';
@@ -826,7 +826,9 @@ int binarraytohex(char *target, const size_t targetlen, char *source, size_t src
             x += (source[s] << (3 - i));
             i++;
             if (i == 4) {
-                if (t >= targetlen - 2) return r;
+                if (t >= targetlen - 2) {
+                    return r;
+                }
                 sprintf(target + t, "%X", x);
                 t++;
                 r += 4;
@@ -835,16 +837,20 @@ int binarraytohex(char *target, const size_t targetlen, char *source, size_t src
             }
         } else {
             if (i > 0) {
-                if (t >= targetlen - 5) return r;
-                w = 0;
+                if (t >= targetlen - 5) {
+                    return r;
+                }
                 sprintf(target + t, "%X[%i]", x, i);
                 t += 4;
                 r += i;
                 x = 0;
                 i = 0;
+                w = 1;
             }
             if (w == 0) {
-                if (t >= targetlen - 2) return r;
+                if (t >= targetlen - 2) {
+                    return r;
+                }
                 sprintf(target + t, " ");
                 t++;
             }
