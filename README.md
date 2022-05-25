commit cb0991d73cafc76d481358cc06f19858fac10d91
Author: iceman1001 <iceman@iuse.se>
Date:   Tue Oct 12 20:28:06 2021 +0200

    remove appveyor ci,  we use Github actions now

diff --git a/README.md b/README.md
index 4a7fada5d..c2b61e613 100644
--- a/README.md
+++ b/README.md
@@ -8,7 +8,7 @@
 
 | Actions OSX CI    |  Actions Ubuntu CI    | Windows CI |
 | ------------------- | -------------------:| -------------------:|
-| ![MacOS Build and Test](https://github.com/RfidResearchGroup/proxmark3/workflows/MacOS%20Build%20and%20Test/badge.svg?branch=master) | ![Ubuntu Build and Test](https://github.com/RfidResearchGroup/proxmark3/workflows/Ubuntu%20Build%20and%20Test/badge.svg?branch=master) | [![Build status](https://ci.appveyor.com/api/projects/status/b4gwrhq3nc876cuu/branch/master?svg=true)](https://ci.appveyor.com/project/RfidResearchGroup/proxmark3/branch/master) |
+| ![MacOS Build and Test](https://github.com/RfidResearchGroup/proxmark3/workflows/MacOS%20Build%20and%20Test/badge.svg?branch=master) | ![Ubuntu Build and Test](https://github.com/RfidResearchGroup/proxmark3/workflows/Ubuntu%20Build%20and%20Test/badge.svg?branch=master) | [![Windows Build and Test](https://github.com/RfidResearchGroup/proxmark3/actions/workflows/windows.yml/badge.svg?branch=master)](https://github.com/RfidResearchGroup/proxmark3/actions/workflows/windows.yml) |
 
 
 # Table of Contents
diff --git a/appveyor.yml b/appveyor.yml.old
similarity index 100%
rename from appveyor.yml
rename to appveyor.yml.old
