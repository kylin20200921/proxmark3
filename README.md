commit d4fece64ec00da3ef51d205e4e6c55af3827a092
Merge: a7b298aaf 352850d15
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Mar 20 07:58:58 2022 +0100

    Merge pull request #1621 from lnv42/iso15sniff2sc
    
    Improve iso15 sniffing: add support for dual subcarriers sniffing

diff --cc armsrc/Standalone/hf_15sniff.c
index 9c8885c2e,069266580..24d068275
--- a/armsrc/Standalone/hf_15sniff.c
+++ b/armsrc/Standalone/hf_15sniff.c
@@@ -98,11 -98,9 +98,11 @@@ void RunMod(void) 
      StandAloneMode();
  
      Dbprintf(_YELLOW_("HF 15693 SNIFF started"));
 +#ifdef WITH_FLASH
      rdv40_spiffs_lazy_mount();
 +#endif
  
-     SniffIso15693(0, NULL);
+     SniffIso15693(0, NULL, false);
  
      Dbprintf("Stopped sniffing");
      SpinDelay(200);
