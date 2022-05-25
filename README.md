commit 347b5c96147c9f17ba188e83342e8e8c646c3cb2
Author: iceman1001 <iceman@iuse.se>
Date:   Wed Sep 22 22:30:25 2021 +0200

    textual

diff --git a/client/src/cmdtrace.c b/client/src/cmdtrace.c
index 68f254e9c..b4f4264d8 100644
--- a/client/src/cmdtrace.c
+++ b/client/src/cmdtrace.c
@@ -593,6 +593,7 @@ static int CmdTraceLoad(const char *Cmd) {
     gs_traceLen = (long)len;
 
     PrintAndLogEx(SUCCESS, "Recorded Activity (TraceLen = " _YELLOW_("%lu") " bytes)", gs_traceLen);
+    PrintAndLogEx(HINT, "try " _YELLOW_("`trace list -1 -t ...`") " to view trace.  Remember the " _YELLOW_("`-1`") " param");
     return PM3_SUCCESS;
 }
 
