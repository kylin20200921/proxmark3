commit aec3ff64e21c9c1abe2c86b3f61ee7942370ba4e
Merge: c52e6bbc5 eb1e94f38
Author: iceman1001 <iceman@iuse.se>
Date:   Thu Apr 7 18:45:39 2022 +0200

    Merge pull request #1654 from wh201906/standalone_14a
    
    Make HF_14ASNIFF standalone mode compatible with old PM3 (without flashmem)

diff --cc armsrc/Standalone/Makefile.hal
index 737209e29,20fd5f5ab..6b4510da8
--- a/armsrc/Standalone/Makefile.hal
+++ b/armsrc/Standalone/Makefile.hal
@@@ -62,11 -62,10 +62,11 @@@ define KNOWN_STANDALONE_DEFINITION
  | LF_THAREXDE     | Simulate/read EM4x50 tags              |
  | (RDV4 only)     | storing in flashmem                    |
  +----------------------------------------------------------+
- | HF_14ASNIFF     | 14a sniff to flashmem                  |
- | (RDV4 only)     |                                        |
+ | HF_14ASNIFF     | 14a sniff to flashmem (rdv4) or ram    |
+ |                 |                                        |
  +----------------------------------------------------------+
 -| HF_14BSNIFF     | 14b sniff                              |
 +| HF_14BSNIFF     | 14b sniff to flashmem (rdv4) or ram    |
 +|                 |                                        |
  +----------------------------------------------------------+
  | HF_15SNIFF      | 15693 sniff to flashmem (rdv4) or ram  |
  |                 |                                        |
