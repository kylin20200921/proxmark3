commit 0d41c2ef091b4728f341ab746e4c97e2f253fa90
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Jan 3 22:15:01 2022 +0100

    style

diff --git a/include/hitag.h b/include/hitag.h
index 168d3f439..6a42d976c 100644
--- a/include/hitag.h
+++ b/include/hitag.h
@@ -97,32 +97,32 @@ typedef enum SOF_TYPE {
 } stype;
 
 struct hitagS_tag {
-    PSTATE   pstate;  //protocol-state
-    TSATE    tstate;  //tag-state
+    PSTATE   pstate;  // protocol-state
+    TSATE    tstate;  // tag-state
     uint32_t uid;
     uint8_t  pages[64][4];
     uint64_t key;
     uint8_t  pwdl0, pwdl1, pwdh0;
-    //con0
+    // con0
     int      max_page;
     stype    mode;
-    //con1
-    bool     auth;   //0=Plain 1=Auth
-    bool     TTFC;   //Transponder Talks first coding. 0=Manchester 1=Biphase
-    int      TTFDR;  //data rate in TTF Mode
-    int      TTFM;   //the number of pages that are sent to the RWD
-    bool     LCON;   //0=con1/2 read write 1=con1 read only and con2 OTP
-    bool     LKP;    //0=page2/3 read write 1=page2/3 read only in Plain mode and no access in authenticate mode
-    //con2
-    //0=read write 1=read only
-    bool     LCK7;   //page4/5
-    bool     LCK6;   //page6/7
-    bool     LCK5;   //page8-11
-    bool     LCK4;   //page12-15
-    bool     LCK3;   //page16-23
-    bool     LCK2;   //page24-31
-    bool     LCK1;   //page32-47
-    bool     LCK0;   //page48-63
+    // con1
+    bool     auth;   // 0 = Plain 1 = Auth
+    bool     TTFC;   // Transponder Talks first coding. 0 = Manchester 1 = Biphase
+    int      TTFDR;  // data rate in TTF Mode
+    int      TTFM;   // the number of pages that are sent to the RWD
+    bool     LCON;   // 0 = con1/2 read write  1 =con1 read only and con2 OTP
+    bool     LKP;    // 0 = page2/3 read write 1 =page2/3 read only in Plain mode and no access in authenticate mode
+    // con2
+    // 0 = read write 1 = read only
+    bool     LCK7;   // page4/5
+    bool     LCK6;   // page6/7
+    bool     LCK5;   // page8-11
+    bool     LCK4;   // page12-15
+    bool     LCK3;   // page16-23
+    bool     LCK2;   // page24-31
+    bool     LCK1;   // page32-47
+    bool     LCK0;   // page48-63
 };
 
 #endif
