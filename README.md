commit 334e80435a4cfbaed662fc399fffb25187392ef6
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Dec 26 05:57:22 2021 +0100

    added a ndef test URI

diff --git a/tools/pm3_tests.sh b/tools/pm3_tests.sh
index b613d8169..bcba7a301 100755
--- a/tools/pm3_tests.sh
+++ b/tools/pm3_tests.sh
@@ -383,6 +383,7 @@ while true; do
       if ! CheckExecute "nfc decode test - oob"           "$CLIENTBIN -c 'nfc decode -d DA2010016170706C69636174696F6E2F766E642E626C7565746F6F74682E65702E6F6F62301000649201B96DFB0709466C65782032'" "Flex 2"; then break; fi
       if ! CheckExecute "nfc decode test - device info"   "$CLIENTBIN -c 'nfc decode -d d1025744690004536f6e79010752432d533338300220426c61636b204e46432052656164657220636f6e6e656374656420746f2050430310123e4567e89b12d3a45642665544000004124e464320506f72742d3130302076312e3032'" "NFC Port-100 v1.02"; then break; fi
       if ! CheckExecute "nfc decode test - vcard"         "$CLIENTBIN -c 'nfc decode -d d20ca3746578742f782d7643617264424547494e3a56434152440a56455253494f4e3a332e300a4e3a43687269733b4963656d616e3b3b3b0a464e3a476f7468656e627572670a5245563a323032312d30362d32345432303a31353a30385a0a6974656d322e582d4142444154453b747970653d707265663a323032302d30362d32340a4954454d322e582d41424c4142454c3a5f24213c416e6e69766572736172793e21245f0a454e443a56434152440a'" "END:VCARD"; then break; fi
+      if ! CheckExecute "nfc decode test - apple wallet"  "$CLIENTBIN -c 'nfc decode -d 031AD10116550077616C6C65743A2F2F61637469766174652F6E6663FE'" "activate/nfc"; then break; fi
 
       echo -e "\n${C_BLUE}Testing LF:${C_NC}"
       if ! CheckExecute "lf AWID test"          "$CLIENTBIN -c 'data load -f traces/lf_AWID-15-259.pm3;lf search -1'" "AWID ID found"; then break; fi
