commit cbecb8a20943239fe217ec329f05e8460bfc49e9
Author: pwpiwi <micki.held@gmx.de>
Date:   Thu Mar 18 08:25:16 2021 +0100

    gcc10 compiler fixes:
    - Check for availability of getenv_s() in argtable3.c

diff --git a/client/cliparser/argtable3.c b/client/cliparser/argtable3.c
index 180ec6c..dfcc9c1 100644
--- a/client/cliparser/argtable3.c
+++ b/client/cliparser/argtable3.c
@@ -1705,7 +1705,7 @@ getopt_internal(int nargc, char * const *nargv, const char *options,
 	 * string begins with a '+'.
 	 */
 	if (posixly_correct == -1 || optreset) {
-#ifdef _WIN32
+#if (defined(__STDC_LIB_EXT1__) && defined(__STDC_WANT_LIB_EXT1__)) || (defined(__STDC_SECURE_LIB__) && defined(__STDC_WANT_SECURE_LIB__))
 		size_t requiredSize;
 		getenv_s(&requiredSize, NULL, 0, "POSIXLY_CORRECT");
 		posixly_correct = requiredSize != 0;
