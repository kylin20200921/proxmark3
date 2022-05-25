commit 4506b0a517c4f97e84233fdeb54f0fb1acfd16e7
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Sep 4 11:43:51 2021 +0200

    text

diff --git a/.github/ISSUE_TEMPLATE/checklist-for-release.md b/.github/ISSUE_TEMPLATE/checklist-for-release.md
index d1bcbc4e5..ee7704aa1 100644
--- a/.github/ISSUE_TEMPLATE/checklist-for-release.md
+++ b/.github/ISSUE_TEMPLATE/checklist-for-release.md
@@ -34,12 +34,13 @@ sudo make install; pushd /tmp; proxmark3 -c 'data load -f lf_EM4x05.pm3;lf searc
 ```
 
 - [ ] RPI Zero
+- [ ] Jetson Nano
 - [ ] WSL
 - [ ] PSv3.10
 - [ ] Archlinux
 - [ ] Kali
 - [ ] Debian
-- [ ] Ubuntu20
+- [ ] Ubuntu21
 - [ ] ParrotOS
 - [ ] Fedora
 - [ ] OpenSuse
@@ -52,11 +53,11 @@ sudo make install; pushd /tmp; proxmark3 -c 'data load -f lf_EM4x05.pm3;lf searc
 last line of output,  gives you next command to run
 Sample:  `git push && git push origin v4.15000`
 
-# Step Github releases
+## Step Github releases
 Go to Github releases,  create release based on the new created tag and publish
 
-# Step Homebrew updates
+## Step Homebrew updates
 update homebrew repo, file `proxmark3.rb` with a SHA256 sum of the file `v4.15000.tar.gz`  
 
-# Step package maintains
+## Step package maintains
 make a list of new standalone modes,  so when we alert package maintainers they have a sporting chance of adding them
