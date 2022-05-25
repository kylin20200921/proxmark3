commit 08bb5ab61959175141876c0d54acd08418fed4a9
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Sep 4 10:20:19 2021 +0200

    text

diff --git a/.github/ISSUE_TEMPLATE/checklist-for-release.md b/.github/ISSUE_TEMPLATE/checklist-for-release.md
index fa9d9d30c..d1bcbc4e5 100644
--- a/.github/ISSUE_TEMPLATE/checklist-for-release.md
+++ b/.github/ISSUE_TEMPLATE/checklist-for-release.md
@@ -35,7 +35,7 @@ sudo make install; pushd /tmp; proxmark3 -c 'data load -f lf_EM4x05.pm3;lf searc
 
 - [ ] RPI Zero
 - [ ] WSL
-- [ ] PSv3.9
+- [ ] PSv3.10
 - [ ] Archlinux
 - [ ] Kali
 - [ ] Debian
@@ -52,6 +52,11 @@ sudo make install; pushd /tmp; proxmark3 -c 'data load -f lf_EM4x05.pm3;lf searc
 last line of output,  gives you next command to run
 Sample:  `git push && git push origin v4.15000`
 
-
+# Step Github releases
 Go to Github releases,  create release based on the new created tag and publish
+
+# Step Homebrew updates
 update homebrew repo, file `proxmark3.rb` with a SHA256 sum of the file `v4.15000.tar.gz`  
+
+# Step package maintains
+make a list of new standalone modes,  so when we alert package maintainers they have a sporting chance of adding them
