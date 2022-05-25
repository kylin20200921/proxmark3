commit ad326d84ade428fdd41ea8e3de7e8705e1db2277
Author: pwpiwi <micki.held@gmx.de>
Date:   Thu Jan 28 09:00:08 2021 +0100

    Fix gcc 10 link issues
    - avoid linker error by providing string.[ch] in bootrom

diff --git a/armsrc/i2c.c b/armsrc/i2c.c
index 3d16b42..a17dd16 100644
--- a/armsrc/i2c.c
+++ b/armsrc/i2c.c
@@ -19,6 +19,8 @@
 #include "BigBuf.h"
 #include "apps.h"
 #include "usb_cdc.h"
+#include "util.h"
+
 
 #ifdef WITH_SMARTCARD
 #include "smartcard.h"
diff --git a/armsrc/mifaresim.c b/armsrc/mifaresim.c
index b63160c..abf800c 100644
--- a/armsrc/mifaresim.c
+++ b/armsrc/mifaresim.c
@@ -22,6 +22,8 @@
 #include "usb_cdc.h"
 #include "protocols.h"
 #include "apps.h"
+#include "util.h"
+
 
 //mifare emulator states
 #define MFEMUL_NOFIELD           0
diff --git a/armsrc/string.h b/armsrc/string.h
index dd90c7b..8705839 100644
--- a/armsrc/string.h
+++ b/armsrc/string.h
@@ -12,13 +12,12 @@
 #ifndef __STRING_H
 #define __STRING_H
 
-#include <stdint.h>
-#include "util.h"
+#include <stddef.h>
 
-RAMFUNC void *memcpy(void *dest, const void *src, size_t len);
+void *memcpy(void *dest, const void *src, size_t len);
 void *memset(void *dest, int c, size_t len);
 void *memmove(void *dest, const void *src, size_t len);
-RAMFUNC int memcmp(const void *av, const void *bv, size_t len);
+int memcmp(const void *av, const void *bv, size_t len);
 size_t strlen(const char *str);
 char *strncat(char *dest, const char *src, size_t n);
 char *strcat(char *dest, const char *src);
diff --git a/bootrom/Makefile b/bootrom/Makefile
index b1e2e6c..a2cd1b4 100644
--- a/bootrom/Makefile
+++ b/bootrom/Makefile
@@ -7,7 +7,7 @@
 #-----------------------------------------------------------------------------
 
 # DO NOT use thumb mode in the phase 1 bootloader since that generates a section with glue code
-ARMSRC = 
+ARMSRC = string.c
 THUMBSRC = usb_cdc.c bootrom.c
 ASMSRC = ram-reset.s flash-reset.s
 VERSIONSRC = version.c
@@ -19,7 +19,6 @@ VERSIONSRC = version.c
 # ARMSRC := $(ARMSRC) $(THUMBSRC)
 # THUMBSRC := 
 
-# stdint.h provided locally until GCC 4.5 becomes C99 compliant
 APP_CFLAGS = -I.
 
 # version.c should be remade on every compilation
diff --git a/bootrom/string.c b/bootrom/string.c
new file mode 100644
index 0000000..b2710d0
--- /dev/null
+++ b/bootrom/string.c
@@ -0,0 +1,104 @@
+//-----------------------------------------------------------------------------
+// Jonathan Westhues, Sept 2005
+//
+// This code is licensed to you under the terms of the GNU GPL, version 2 or,
+// at your option, any later version. See the LICENSE.txt file for the text of
+// the license.
+//-----------------------------------------------------------------------------
+// Common string.h functions
+//-----------------------------------------------------------------------------
+
+#include "string.h"
+#include <stdint.h>
+
+void *memcpy(void *dest, const void *src, size_t len) {
+	uint8_t *d = dest;
+	const uint8_t *s = src;
+	while((len--) > 0) {
+		*d = *s;
+		d++;
+		s++;
+	}
+	return dest;
+}
+
+
+void *memset(void *dest, int c, size_t len) {
+	uint8_t *d = dest;
+	while((len--) > 0) {
+		*d = c;
+		d++;
+	}
+	return dest;
+}
+
+
+void *memmove(void *dest, const void *src, size_t len) {
+	uint8_t *d = dest;
+	const uint8_t *s = src;
+	if (dest <= src) {
+		while((len--) > 0) {
+			*d = *s;
+			d++;
+			s++;
+		} 
+	} else {
+		d = d + len - 1;
+		s = s + len - 1;
+		while((len--) > 0) {
+			*d = *s;
+			d--;
+			s--;
+		}
+	}
+	return dest;
+}
+
+
+int memcmp(const void *av, const void *bv, size_t len) {
+	const uint8_t *a = av;
+	const uint8_t *b = bv;
+
+	while((len--) > 0) {
+		if(*a != *b) {
+			return *a - *b;
+		}
+		a++;
+		b++;
+	}
+	return 0;
+}
+
+
+size_t strlen(const char *str) {
+	int l = 0;
+	while(*str) {
+		l++;
+		str++;
+	}
+	return l;
+}
+
+
+char* strncat(char *dest, const char *src, size_t n) {
+	unsigned int dest_len = strlen(dest);
+	unsigned int i;
+
+	for (i = 0 ; i < n && src[i] != '\0' ; i++)
+		dest[dest_len + i] = src[i];
+	dest[dest_len + i] = '\0';
+
+	return dest;
+}
+
+
+char* strcat(char *dest, const char *src) {
+	unsigned int dest_len = strlen(dest);
+	unsigned int i;
+
+	for (i = 0 ; src[i] != '\0' ; i++)
+		dest[dest_len + i] = src[i];
+	dest[dest_len + i] = '\0';
+
+	return dest;
+}
diff --git a/bootrom/string.h b/bootrom/string.h
new file mode 100644
index 0000000..8705839
--- /dev/null
+++ b/bootrom/string.h
@@ -0,0 +1,25 @@
+//-----------------------------------------------------------------------------
+// Jonathan Westhues, Aug 2005
+// Copyright (C) 2010 Hector Martin "marcan" <marcan@marcansoft.com>
+//
+// This code is licensed to you under the terms of the GNU GPL, version 2 or,
+// at your option, any later version. See the LICENSE.txt file for the text of
+// the license.
+//-----------------------------------------------------------------------------
+// Common string.h functions
+//-----------------------------------------------------------------------------
+
+#ifndef __STRING_H
+#define __STRING_H
+
+#include <stddef.h>
+
+void *memcpy(void *dest, const void *src, size_t len);
+void *memset(void *dest, int c, size_t len);
+void *memmove(void *dest, const void *src, size_t len);
+int memcmp(const void *av, const void *bv, size_t len);
+size_t strlen(const char *str);
+char *strncat(char *dest, const char *src, size_t n);
+char *strcat(char *dest, const char *src);
+
+#endif /* __STRING_H */
