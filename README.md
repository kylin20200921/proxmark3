commit 5003e79214fabaa451f8f8d34478a5710d9461dc
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Apr 3 06:23:19 2022 +0200

    added the new 14b sniff related

diff --git a/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md b/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md
index 494f5a8ab..ba53d3aaf 100644
--- a/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md
+++ b/doc/md/Use_of_Proxmark/4_Advanced-compilation-parameters.md
@@ -114,6 +114,7 @@ Here are the supported values you can assign to `STANDALONE` in `Makefile.platfo
 | LF_SKELETON     | standalone mode skeleton - Iceman1001
 | LF_THAREXDE     | LF EM4x50 simulator/read standalone mode - tharexde
 | HF_14ASNIFF     | 14a sniff storing to flashmem - Micolous
+| HF_14BSNIFF     | 14b sniff - jacopo-j
 | HF_15SNIFF      | 15693 sniff storing to flashmem - Glaser
 | HF_AVEFUL       | MIFARE Ultralight read/simulation - Ave Ozkal
 | HF_BOG          | 14a sniff with ULC/ULEV1/NTAG auth storing in flashmem - Bogito
diff --git a/tools/build_all_firmwares.sh b/tools/build_all_firmwares.sh
index 29e8472db..482033189 100755
--- a/tools/build_all_firmwares.sh
+++ b/tools/build_all_firmwares.sh
@@ -31,7 +31,7 @@ mv bootrom/obj/bootrom.elf "$DEST/PM3BOOTROM.elf"
 
 # cf armsrc/Standalone/Makefile.hal
 STANDALONE_MODES=(LF_SKELETON LF_EM4100EMUL LF_EM4100RSWB LF_EM4100RSWW LF_EM4100RWC LF_HIDBRUTE LF_HIDFCBRUTE LF_ICEHID LF_PROXBRUTE LF_SAMYRUN LF_THAREXDE LF_NEXID)
-STANDALONE_MODES+=(HF_14ASNIFF HF_15SNIFF HF_AVEFUL HF_BOG HF_COLIN HF_CRAFTBYTE HF_ICECLASS HF_LEGIC HF_LEGICSIM HF_MATTYRUN HF_MFCSIM HF_MSDSAL HF_TCPRST HF_TMUDFORD HF_YOUNG HF_REBLAY DANKARMULTI)
+STANDALONE_MODES+=(HF_14ASNIFF HF_14BSNIFF HF_15SNIFF HF_AVEFUL HF_BOG HF_COLIN HF_CRAFTBYTE HF_ICECLASS HF_LEGIC HF_LEGICSIM HF_MATTYRUN HF_MFCSIM HF_MSDSAL HF_TCPRST HF_TMUDFORD HF_YOUNG HF_REBLAY DANKARMULTI)
 STANDALONE_MODES_REQ_BT=(HF_REBLAY)
 STANDALONE_MODES_REQ_SMARTCARD=()
 STANDALONE_MODES_REQ_FLASH=(LF_HIDFCBRUTE LF_ICEHID LF_NEXID LF_THAREXDE HF_14ASNIFF HF_15SNIFF HF_BOG HF_COLIN HF_ICECLASS HF_MFCSIM)
