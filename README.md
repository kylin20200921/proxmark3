commit ef8fbc68025a3c4de5542ea3040b39eff29db79f
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Dec 16 03:25:18 2021 +0100

    textual

diff --git a/.github/ISSUE_TEMPLATE/checklist-for-release.md b/.github/ISSUE_TEMPLATE/checklist-for-release.md
index a3d8b2657..193516862 100644
--- a/.github/ISSUE_TEMPLATE/checklist-for-release.md
+++ b/.github/ISSUE_TEMPLATE/checklist-for-release.md
@@ -19,7 +19,6 @@ assignees: doegox, iceman1001
 - [ ] `experimental_lib` compilation & tests
 - [ ] `experimental_client_with_swig` compilation & tests
 - [ ] GitHub Actions - green across the board ( MacOS, Ubuntu, Windows)
-- [ ] [Appveyor](https://ci.appveyor.com/project/RfidResearchGroup/proxmark3/history) green (PS)
 
 # OS compilation and tests
 
@@ -60,7 +59,7 @@ make hitag2crack/clean && make hitag2crack && tools/pm3_tests.sh hitag2crack ||
 
 - [ ] `make release RELEASE_NAME="ice awesome"`
   - last line of output,  gives you next command to run.
-  - Sample:  `git push && git push origin v4.15000`
+  - Sample:  `git push && git push origin v4.12345`
 - [ ] CHANGELOG.md: edit title to add version info: `## [releasename.4.12345][YYYY-MM-DD]`
 
 ## Step Github releases
@@ -70,7 +69,7 @@ make hitag2crack/clean && make hitag2crack && tools/pm3_tests.sh hitag2crack ||
 ## Step Homebrew updates
 
 - [ ] update homebrew repo, file `proxmark3.rb`
-  - with a SHA256 sum of the file `v4.15000.tar.gz`
+  - with a SHA256 sum of the file `v4.12345.tar.gz`
   - with updated list of standalone modes
 
 ## Step package maintains
