commit 8f9d8fa1bdcd4792c881385fb60e93756363580b
Author: iceman1001 <iceman@iuse.se>
Date:   Fri May 21 11:50:49 2021 +0200

    textual

diff --git a/tools/cryptorf/sma_multi.cpp b/tools/cryptorf/sma_multi.cpp
index 5e38c9b0b..afce275ad 100644
--- a/tools/cryptorf/sma_multi.cpp
+++ b/tools/cryptorf/sma_multi.cpp
@@ -1080,7 +1080,7 @@ int main(int argc, const char *argv[]) {
     printf("Total count of right bins: " _YELLOW_("%zu") "\n", rstates.size());
 
     if (rbits < 96) {
-        printf(_RED_("\n  WARNING!!! Better find another trace, the right top-bin is < 96 bits\n\n"));
+        printf("\n" _RED_("WARNING!!!") ", better find another trace, the right top-bin is smaller than 96 bits\n\n");
     }
 
     for (itrstates = rstates.begin(); itrstates != rstates.end(); ++itrstates) {
