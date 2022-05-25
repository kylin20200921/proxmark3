commit 4e0fe278e4db3e5a3103650a4863a2a7870379d3
Author: iceman1001 <iceman@iuse.se>
Date:   Mon Jun 21 10:40:39 2021 +0200

    added two tests for ndef parsing

diff --git a/tools/pm3_tests.sh b/tools/pm3_tests.sh
index b812bdd03..38d66d23f 100755
--- a/tools/pm3_tests.sh
+++ b/tools/pm3_tests.sh
@@ -367,6 +367,8 @@ while true; do
       if ! CheckExecute "jooki encode test"       "$CLIENTBIN -c 'hf jooki encode -t'" "04 28 F4 DA F0 4A 81  ( ok )"; then break; fi
       if ! CheckExecute "trace load/list 14a"     "$CLIENTBIN -c 'trace load -f traces/hf_14a_mfu.trace; trace list -1 -t 14a;'" "READBLOCK(8)"; then break; fi
       if ! CheckExecute "trace load/list x"       "$CLIENTBIN -c 'trace load -f traces/hf_14a_mfu.trace; trace list -x1 -t 14a;'" "0.0101840425"; then break; fi
+      if ! CheckExecute "nfc decode test - oob"           "$CLIENTBIN -c 'nfc decode -d DA2010016170706C69636174696F6E2F766E642E626C7565746F6F74682E65702E6F6F62301000649201B96DFB0709466C65782032'" "Flex 2"; then break; fi
+      if ! CheckExecute "nfc decode test - device info"   "$CLIENTBIN -c 'nfc decode -d d1025744690004536f6e79010752432d533338300220426c61636b204e46432052656164657220636f6e6e656374656420746f2050430310123e4567e89b12d3a45642665544000004124e464320506f72742d3130302076312e3032'" "NFC Port-100 v1.02"; then break; fi
 
       echo -e "\n${C_BLUE}Testing LF:${C_NC}"
       if ! CheckExecute "lf AWID test"          "$CLIENTBIN -c 'data load -f traces/lf_AWID-15-259.pm3;lf search -1'" "AWID ID found"; then break; fi
