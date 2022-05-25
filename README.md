commit fe9348768dad8ba851c0461bced031a716fe5860
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jan 9 00:33:33 2022 +0100

    use calloc instead

diff --git a/client/src/emv/crypto_polarssl.c b/client/src/emv/crypto_polarssl.c
index a6509ca0a..00e484bf7 100644
--- a/client/src/emv/crypto_polarssl.c
+++ b/client/src/emv/crypto_polarssl.c
@@ -63,7 +63,7 @@ static struct crypto_hash *crypto_hash_polarssl_open(enum crypto_algo_hash hash)
     if (hash != HASH_SHA_1)
         return NULL;
 
-    struct crypto_hash_polarssl *ch = malloc(sizeof(*ch));
+    struct crypto_hash_polarssl *ch = calloc(1, sizeof(*ch));
 
     mbedtls_sha1_starts(&(ch->ctx));
 
@@ -81,7 +81,7 @@ struct crypto_pk_polarssl {
 };
 
 static struct crypto_pk *crypto_pk_polarssl_open_rsa(va_list vl) {
-    struct crypto_pk_polarssl *cp = malloc(sizeof(*cp));
+    struct crypto_pk_polarssl *cp = calloc(1, sizeof(*cp));
     memset(cp, 0x00, sizeof(*cp));
 
     char *mod = va_arg(vl, char *); // N
@@ -106,7 +106,7 @@ static struct crypto_pk *crypto_pk_polarssl_open_rsa(va_list vl) {
 }
 
 static struct crypto_pk *crypto_pk_polarssl_open_priv_rsa(va_list vl) {
-    struct crypto_pk_polarssl *cp = malloc(sizeof(*cp));
+    struct crypto_pk_polarssl *cp = calloc(1, sizeof(*cp));
     memset(cp, 0x00, sizeof(*cp));
     char *mod = va_arg(vl, char *);
     int modlen = va_arg(vl, size_t);
@@ -166,7 +166,7 @@ static int myrand(void *rng_state, unsigned char *output, size_t len) {
 }
 
 static struct crypto_pk *crypto_pk_polarssl_genkey_rsa(va_list vl) {
-    struct crypto_pk_polarssl *cp = malloc(sizeof(*cp));
+    struct crypto_pk_polarssl *cp = calloc(1, sizeof(*cp));
     memset(cp, 0x00, sizeof(*cp));
 
     int transient = va_arg(vl, int);
@@ -198,7 +198,7 @@ static unsigned char *crypto_pk_polarssl_encrypt(const struct crypto_pk *_cp, co
     *clen = 0;
     size_t keylen = mbedtls_mpi_size(&cp->ctx.N);
 
-    unsigned char *result = malloc(keylen);
+    unsigned char *result = calloc(1, keylen);
     if (!result) {
         PrintAndLogEx(WARNING, "RSA encrypt failed. Can't allocate result memory");
         return NULL;
@@ -220,7 +220,7 @@ static unsigned char *crypto_pk_polarssl_decrypt(const struct crypto_pk *_cp, co
     *clen = 0;
     size_t keylen = mbedtls_mpi_size(&cp->ctx.N);
 
-    unsigned char *result = malloc(keylen);
+    unsigned char *result = calloc(1, keylen);
     if (!result) {
         PrintAndLogEx(WARNING, "RSA encrypt failed. Can't allocate result memory");
         return NULL;
@@ -250,7 +250,7 @@ static unsigned char *crypto_pk_polarssl_get_parameter(const struct crypto_pk *_
         // mod
         case 0:
             *plen = mbedtls_mpi_size(&cp->ctx.N);
-            result = malloc(*plen);
+            result = calloc(1, *plen);
             memset(result, 0x00, *plen);
             res = mbedtls_mpi_write_binary(&cp->ctx.N, result, *plen);
             if (res < 0) {
@@ -262,7 +262,7 @@ static unsigned char *crypto_pk_polarssl_get_parameter(const struct crypto_pk *_
         // exp
         case 1:
             *plen = mbedtls_mpi_size(&cp->ctx.E);
-            result = malloc(*plen);
+            result = calloc(1, *plen);
             memset(result, 0x00, *plen);
             res = mbedtls_mpi_write_binary(&cp->ctx.E, result, *plen);
             if (res < 0) {
diff --git a/client/src/emv/dol.c b/client/src/emv/dol.c
index 5eb1c0351..dddefaf4a 100644
--- a/client/src/emv/dol.c
+++ b/client/src/emv/dol.c
@@ -54,7 +54,7 @@ static size_t dol_calculate_len(const struct tlv *tlv, size_t data_len) {
 struct tlv *dol_process(const struct tlv *tlv, const struct tlvdb *tlvdb, tlv_tag_t tag) {
     size_t res_len;
     if (!tlv || !(res_len = dol_calculate_len(tlv, 0))) {
-        struct tlv *res_tlv = malloc(sizeof(*res_tlv));
+        struct tlv *res_tlv = calloc(1, sizeof(*res_tlv));
 
         res_tlv->tag = tag;
         res_tlv->len = 0;
@@ -63,7 +63,7 @@ struct tlv *dol_process(const struct tlv *tlv, const struct tlvdb *tlvdb, tlv_ta
         return res_tlv;
     }
 
-    struct tlv *res_tlv = malloc(sizeof(*res_tlv) + res_len);
+    struct tlv *res_tlv = calloc(1, sizeof(*res_tlv) + res_len);
     if (!res_tlv)
         return NULL;
 
diff --git a/client/src/emv/emv_pk.c b/client/src/emv/emv_pk.c
index ab700310c..e3f871fd7 100644
--- a/client/src/emv/emv_pk.c
+++ b/client/src/emv/emv_pk.c
@@ -202,7 +202,7 @@ struct emv_pk *emv_pk_parse_pk(char *buf, size_t buflen) {
         goto out;
     buf += l;
 
-    r->modulus = malloc(2048 / 8);
+    r->modulus = calloc(1, (2048 / 8));
     l = emv_pk_read_bin(buf, buflen, r->modulus, 2048 / 8, &r->mlen);
     if (l <= 0)
         goto out2;
@@ -267,8 +267,8 @@ static size_t emv_pk_write_str(char *out, size_t outlen, const char *str) {
 
 char *emv_pk_dump_pk(const struct emv_pk *pk) {
     size_t outpos = 0;
-    size_t outsize = 1024; /* should be enough */
-    char *out = malloc(outsize); /* should be enough */
+    size_t outsize = 1024; // should be enough
+    char *out = calloc(1, outsize); // should be enough
     if (!out)
         return NULL;
 
diff --git a/client/src/emv/emv_pki.c b/client/src/emv/emv_pki.c
index c3f016003..17eb7e0ea 100644
--- a/client/src/emv/emv_pki.c
+++ b/client/src/emv/emv_pki.c
@@ -310,7 +310,7 @@ struct emv_pk *emv_pki_recover_icc_cert(const struct emv_pk *pk, struct tlvdb *d
                                             sda_tlv,
                                             &sda_tdata);
 
-    free(sdatl); // malloc here: emv_pki_sdatl_fill
+    free(sdatl); // calloc here: emv_pki_sdatl_fill
     return res;
 }
 
@@ -345,7 +345,7 @@ unsigned char *emv_pki_sdatl_fill(const struct tlvdb *db, size_t *sdatl_len) {
 
     if (len) {
         *sdatl_len = len;
-        unsigned char *value = malloc(len);
+        unsigned char *value = calloc(1, len);
         memcpy(value, buf, len);
         return value;
     }
@@ -372,7 +372,7 @@ struct tlvdb *emv_pki_recover_dac_ex(const struct emv_pk *enc_pk, const struct t
                                                  &sda_tdata,
                                                  (uint8_t *)NULL);
 
-    free(sdatl); // malloc here: emv_pki_sdatl_fill
+    free(sdatl); // calloc here: emv_pki_sdatl_fill
 
     if (!data || data_len < 5)
         return NULL;
diff --git a/client/src/emv/emv_pki_priv.c b/client/src/emv/emv_pki_priv.c
index c559bd6c1..5fb3ed620 100644
--- a/client/src/emv/emv_pki_priv.c
+++ b/client/src/emv/emv_pki_priv.c
@@ -90,15 +90,15 @@ static struct tlvdb *emv_pki_sign_message(const struct crypto_pk *cp,
                                           ... /* A list of tlv pointers, end with NULL */
                                          ) {
     size_t tmp_len = (crypto_pk_get_nbits(cp) + 7) / 8;
-    unsigned char *tmp = malloc(tmp_len);
-    if (!tmp)
+    unsigned char *tmp = calloc(1, tmp_len);
+    if (!tmp) {
         return NULL;
+    }
 
     // XXX
     struct crypto_hash *ch = crypto_hash_open(HASH_SHA_1);
     if (!ch) {
         free(tmp);
-
         return NULL;
     }
 
@@ -179,7 +179,7 @@ static struct tlvdb *emv_pki_sign_key(const struct crypto_pk *cp,
                                       const struct tlv *add_tlv
                                      ) {
     unsigned pos = 0;
-    unsigned char *msg = malloc(1 + pan_len + 2 + 3 + 1 + 1 + 1 + 1 + ipk->mlen);
+    unsigned char *msg = calloc(1, 1 + pan_len + 2 + 3 + 1 + 1 + 1 + 1 + ipk->mlen);
 
     if (!msg)
         return NULL;
@@ -235,7 +235,7 @@ struct tlvdb *emv_pki_sign_icc_pe_cert(const struct crypto_pk *cp, struct emv_pk
 
 struct tlvdb *emv_pki_sign_dac(const struct crypto_pk *cp, const struct tlv *dac_tlv, const struct tlv *sda_tlv) {
     unsigned pos = 0;
-    unsigned char *msg = malloc(1 + 1 + dac_tlv->len);
+    unsigned char *msg = calloc(1, 1 + 1 + dac_tlv->len);
 
     if (!msg)
         return NULL;
@@ -258,7 +258,7 @@ struct tlvdb *emv_pki_sign_dac(const struct crypto_pk *cp, const struct tlv *dac
 
 struct tlvdb *emv_pki_sign_idn(const struct crypto_pk *cp, const struct tlv *idn_tlv, const struct tlv *dyn_tlv) {
     unsigned pos = 0;
-    unsigned char *msg = malloc(1 + 1 + 1 + 1 + idn_tlv->len);
+    unsigned char *msg = calloc(1, 1 + 1 + 1 + 1 + idn_tlv->len);
 
     if (!msg)
         return NULL;
diff --git a/client/src/emv/test/cda_test.c b/client/src/emv/test/cda_test.c
index c9a8323b6..0ab49f3ca 100644
--- a/client/src/emv/test/cda_test.c
+++ b/client/src/emv/test/cda_test.c
@@ -23,7 +23,7 @@
 #endif
 
 #include <string.h>     // memcpy
-#include <stdlib.h>     // malloc
+#include <stdlib.h>     // calloc
 #include "cda_test.h"
 
 #include "../emv_pk.h"
@@ -181,7 +181,7 @@ static int cda_test_raw(bool verbose) {
     }
 
     size_t ipk_pk_len = ipk_data[13];
-    unsigned char *ipk_pk = malloc(ipk_pk_len);
+    unsigned char *ipk_pk = calloc(1, ipk_pk_len);
     memcpy(ipk_pk, ipk_data + 15, ipk_data_len - 36);
     memcpy(ipk_pk + ipk_data_len - 36, c_issuer_rem, sizeof(c_issuer_rem));
 
@@ -240,7 +240,7 @@ static int cda_test_raw(bool verbose) {
     }
 
     size_t iccpk_pk_len = iccpk_data[19];
-    unsigned char *iccpk_pk = malloc(iccpk_pk_len);
+    unsigned char *iccpk_pk = calloc(1, iccpk_pk_len);
     memcpy(iccpk_pk, iccpk_data + 21, /*iccpk_data_len - 36*/iccpk_pk_len);
     /*memcpy(iccpk_pk + iccpk_data_len - 36, icc_rem, sizeof(icc_rem));*/
 
diff --git a/client/src/emv/test/crypto_test.c b/client/src/emv/test/crypto_test.c
index 5e6bcaac4..332203ad1 100644
--- a/client/src/emv/test/crypto_test.c
+++ b/client/src/emv/test/crypto_test.c
@@ -23,7 +23,7 @@
 #endif
 
 #include <string.h>     // memcpy
-#include <stdlib.h>     // malloc
+#include <stdlib.h>     // calloc
 #include <inttypes.h>
 #include "crypto_test.h"
 
diff --git a/client/src/emv/test/dda_test.c b/client/src/emv/test/dda_test.c
index 6a6b91bab..9b56a4096 100644
--- a/client/src/emv/test/dda_test.c
+++ b/client/src/emv/test/dda_test.c
@@ -23,7 +23,7 @@
 #endif
 
 #include <string.h>     // memcpy
-#include <stdlib.h>     // malloc
+#include <stdlib.h>     // calloc
 
 #include "dda_test.h"
 #include "../emv_pk.h"
@@ -169,7 +169,7 @@ static int dda_test_raw(bool verbose) {
     }
 
     size_t ipk_pk_len = ipk_data[13];
-    unsigned char *ipk_pk = malloc(ipk_pk_len);
+    unsigned char *ipk_pk = calloc(1, ipk_pk_len);
     memcpy(ipk_pk, ipk_data + 15, ipk_data_len - 36);
     memcpy(ipk_pk + ipk_data_len - 36, d_issuer_rem, sizeof(d_issuer_rem));
 
@@ -228,7 +228,7 @@ static int dda_test_raw(bool verbose) {
     }
 
     size_t iccpk_pk_len = iccpk_data[19];
-    unsigned char *iccpk_pk = malloc(iccpk_pk_len);
+    unsigned char *iccpk_pk = calloc(1, iccpk_pk_len);
     memcpy(iccpk_pk, iccpk_data + 21, /*iccpk_data_len - 36*/iccpk_pk_len);
     /*memcpy(iccpk_pk + iccpk_data_len - 36, icc_rem, sizeof(icc_rem));*/
 
diff --git a/client/src/emv/test/sda_test.c b/client/src/emv/test/sda_test.c
index bf64e6cb4..ebf5f1015 100644
--- a/client/src/emv/test/sda_test.c
+++ b/client/src/emv/test/sda_test.c
@@ -131,7 +131,7 @@ static int sda_test_raw(bool verbose) {
     }
 
     size_t ipk_pk_len = ipk_data[13];
-    unsigned char *ipk_pk = malloc(ipk_pk_len);
+    unsigned char *ipk_pk = calloc(1, ipk_pk_len);
     memcpy(ipk_pk, ipk_data + 15, ipk_data_len - 36);
     memcpy(ipk_pk + ipk_data_len - 36, issuer_rem, sizeof(issuer_rem));
 
diff --git a/client/src/emv/tlv.c b/client/src/emv/tlv.c
index 884e340b3..64a3c0a1b 100644
--- a/client/src/emv/tlv.c
+++ b/client/src/emv/tlv.c
@@ -26,6 +26,7 @@
 
 #include <string.h>
 #include <stdlib.h>
+#include <stdbool.h>
 
 #define TLV_TAG_CLASS_MASK  0xc0
 #define TLV_TAG_COMPLEX     0x20
@@ -163,7 +164,7 @@ static struct tlvdb *tlvdb_parse_children(struct tlvdb *parent) {
     struct tlvdb *tlvdb, *first = NULL, *prev = NULL;
 
     while (left != 0) {
-        tlvdb = malloc(sizeof(*tlvdb));
+        tlvdb = calloc(1, sizeof(*tlvdb));
         if (prev)
             prev->next = tlvdb;
         else
@@ -192,7 +193,7 @@ struct tlvdb *tlvdb_parse(const unsigned char *buf, size_t len) {
     if (!len || !buf)
         return NULL;
 
-    root = malloc(sizeof(*root) + len);
+    root = calloc(1, sizeof(*root) + len);
     root->len = len;
     memcpy(root->buf, buf, len);
 
@@ -221,7 +222,7 @@ struct tlvdb *tlvdb_parse_multi(const unsigned char *buf, size_t len) {
     if (!len || !buf)
         return NULL;
 
-    root = malloc(sizeof(*root) + len);
+    root = calloc(1, sizeof(*root) + len);
     root->len = len;
     memcpy(root->buf, buf, len);
 
@@ -232,7 +233,7 @@ struct tlvdb *tlvdb_parse_multi(const unsigned char *buf, size_t len) {
         goto err;
 
     while (left != 0) {
-        struct tlvdb *db = malloc(sizeof(*db));
+        struct tlvdb *db = calloc(1, sizeof(*db));
         if (!tlvdb_parse_one(db, NULL, &tmp, &left)) {
             free(db);
             goto err;
@@ -250,7 +251,7 @@ err:
 }
 
 struct tlvdb *tlvdb_fixed(tlv_tag_t tag, size_t len, const unsigned char *value) {
-    struct tlvdb_root *root = malloc(sizeof(*root) + len);
+    struct tlvdb_root *root = calloc(1, sizeof(*root) + len);
 
     root->len = len;
     memcpy(root->buf, value, len);
@@ -264,7 +265,7 @@ struct tlvdb *tlvdb_fixed(tlv_tag_t tag, size_t len, const unsigned char *value)
 }
 
 struct tlvdb *tlvdb_external(tlv_tag_t tag, size_t len, const unsigned char *value) {
-    struct tlvdb_root *root = malloc(sizeof(*root));
+    struct tlvdb_root *root = calloc(1, sizeof(*root));
 
     root->len = 0;
 
@@ -489,7 +490,7 @@ unsigned char *tlv_encode(const struct tlv *tlv, size_t *len) {
     else
         size += 1;
 
-    data = malloc(size);
+    data = calloc(1, size);
     if (!data) {
         *len = 0;
         return NULL;
diff --git a/client/src/pm3_pywrap.c b/client/src/pm3_pywrap.c
index a289475a3..7837fb5e2 100644
--- a/client/src/pm3_pywrap.c
+++ b/client/src/pm3_pywrap.c
@@ -1912,7 +1912,7 @@ SWIGRUNTIME PyObject *
 SwigPyPacked_New(void *ptr, size_t size, swig_type_info *ty) {
     SwigPyPacked *sobj = PyObject_NEW(SwigPyPacked, SwigPyPacked_type());
     if (sobj) {
-        void *pack = malloc(size);
+        void *pack = calloc(1, size);
         if (pack) {
             memcpy(pack, ptr, size);
             sobj->pack = pack;
diff --git a/client/src/uart/uart_posix.c b/client/src/uart/uart_posix.c
index b49458cb7..b2a858e09 100644
--- a/client/src/uart/uart_posix.c
+++ b/client/src/uart/uart_posix.c
@@ -82,7 +82,7 @@ serial_port uart_open(const char *pcPortName, uint32_t speed) {
 
     char *prefix = strdup(pcPortName);
     if (prefix == NULL) {
-        PrintAndLogEx(ERR, "error: malloc");
+        PrintAndLogEx(ERR, "error:  string duplication");
         free(sp);
         return INVALID_SERIAL_PORT;
     }
@@ -100,7 +100,7 @@ serial_port uart_open(const char *pcPortName, uint32_t speed) {
 
         char *addrstr = strdup(pcPortName + 4);
         if (addrstr == NULL) {
-            PrintAndLogEx(ERR, "error: malloc");
+            PrintAndLogEx(ERR, "error: string duplication");
             free(sp);
             return INVALID_SERIAL_PORT;
         }
@@ -175,7 +175,7 @@ serial_port uart_open(const char *pcPortName, uint32_t speed) {
 
         char *addrstr = strndup(pcPortName + 3, 17);
         if (addrstr == NULL) {
-            PrintAndLogEx(ERR, "error: malloc");
+            PrintAndLogEx(ERR, "error: string duplication");
             free(sp);
             return INVALID_SERIAL_PORT;
         }
diff --git a/common/crapto1/crapto1.c b/common/crapto1/crapto1.c
index 7d06fee39..6715b12ed 100644
--- a/common/crapto1/crapto1.c
+++ b/common/crapto1/crapto1.c
@@ -141,9 +141,9 @@ struct Crypto1State *lfsr_recovery32(uint32_t ks2, uint32_t in) {
     for (i = 30; i >= 0; i -= 2)
         eks = eks << 1 | BEBIT(ks2, i);
 
-    odd_head = odd_tail = malloc(sizeof(uint32_t) << 21);
-    even_head = even_tail = malloc(sizeof(uint32_t) << 21);
-    statelist =  malloc(sizeof(struct Crypto1State) << 18);
+    odd_head = odd_tail = calloc(1, sizeof(uint32_t) << 21);
+    even_head = even_tail = calloc(1, sizeof(uint32_t) << 21);
+    statelist =  calloc(1, sizeof(struct Crypto1State) << 18);
     if (!odd_tail-- || !even_tail-- || !statelist) {
         free(statelist);
         statelist = 0;
@@ -157,7 +157,7 @@ struct Crypto1State *lfsr_recovery32(uint32_t ks2, uint32_t in) {
 
     for (i = 0; i < 2; i++) {
         for (uint32_t j = 0; j <= 0xff; j++) {
-            bucket[i][j].head = malloc(sizeof(uint32_t) << 14);
+            bucket[i][j].head = calloc(1, sizeof(uint32_t) << 14);
             if (!bucket[i][j].head) {
                 goto out;
             }
@@ -227,7 +227,7 @@ struct Crypto1State *lfsr_recovery64(uint32_t ks2, uint32_t ks3) {
     uint32_t *tail, table[1 << 16];
     int i, j;
 
-    sl = statelist = malloc(sizeof(struct Crypto1State) << 4);
+    sl = statelist = calloc(1, sizeof(struct Crypto1State) << 4);
     if (!sl)
         return 0;
     sl->odd = sl->even = 0;
@@ -488,7 +488,7 @@ struct Crypto1State *lfsr_common_prefix(uint32_t pfx, uint32_t rr, uint8_t ks[8]
     odd = lfsr_prefix_ks(ks, 1);
     even = lfsr_prefix_ks(ks, 0);
 
-    s = statelist = malloc((sizeof * statelist) << 24); // was << 20. Need more for no_par special attack. Enough???
+    s = statelist = calloc(1, (sizeof * statelist) << 24); // was << 20. Need more for no_par special attack. Enough???
     if (!s || !odd || !even) {
         free(statelist);
         statelist = 0;
diff --git a/common/lfdemod.c b/common/lfdemod.c
index 8314206d0..738b2060d 100644
--- a/common/lfdemod.c
+++ b/common/lfdemod.c
@@ -1340,7 +1340,7 @@ bool DetectST(uint8_t *buffer, size_t *size, int *foundclock, size_t *ststart, s
     int tol = 0;
     int j = 0, high, low, skip = 0, start = 0, end = 0, minClk = 255;
     size_t i = 0;
-    //probably should malloc... || test if memory is available ... handle device side? memory danger!!! [marshmellow]
+    //probably should calloc... || test if memory is available ... handle device side? memory danger!!! [marshmellow]
     int tmpbuff[bufsize / LOWEST_DEFAULT_CLOCK]; // low to low wave count //guess rf/32 clock, if click is smaller we will only have room for a fraction of the samples captured
     int waveLen[bufsize / LOWEST_DEFAULT_CLOCK]; // high to low wave count //if clock is larger then we waste memory in array size that is not needed...
     //size_t testsize = (bufsize < 512) ? bufsize : 512;
diff --git a/tools/hitag2crack/crack2/ht2crack2buildtable.c b/tools/hitag2crack/crack2/ht2crack2buildtable.c
index 5454b0717..a8f576f5f 100644
--- a/tools/hitag2crack/crack2/ht2crack2buildtable.c
+++ b/tools/hitag2crack/crack2/ht2crack2buildtable.c
@@ -60,9 +60,9 @@ static void create_table(struct table *tt, int d_1, int d_2) {
     }
 
     // create some space
-    tt->data = (unsigned char *)malloc(DATAMAX);
+    tt->data = (unsigned char *)calloc(1, DATAMAX);
     if (!(tt->data)) {
-        printf("create_table: cannot malloc data\n");
+        printf("create_table: cannot calloc data\n");
         exit(1);
     }
 
@@ -373,9 +373,9 @@ static void *sorttable(void *dd) {
     int space = 0x100 / NUM_SORT_THREADS;
 
     // create table - 50MB should be enough
-    unsigned char *table = (unsigned char *)malloc(50UL * 1024UL * 1024UL);
+    unsigned char *table = (unsigned char *)calloc(1, 50UL * 1024UL * 1024UL);
     if (!table) {
-        printf("sorttable: cannot malloc table\n");
+        printf("sorttable: cannot calloc table\n");
         exit(1);
     }
 
diff --git a/tools/hitag2crack/crack2/ht2crack2search.c b/tools/hitag2crack/crack2/ht2crack2search.c
index f9d4a779b..c775f7011 100644
--- a/tools/hitag2crack/crack2/ht2crack2search.c
+++ b/tools/hitag2crack/crack2/ht2crack2search.c
@@ -59,9 +59,9 @@ static int loadrngdata(struct rngdata *r, char *file) {
     r->len = filestat.st_size / 2;
 //    printf("r->len = %d\n", r->len);
 
-    r->data = (unsigned char *)malloc(r->len);
+    r->data = (unsigned char *)calloc(1, r->len);
     if (!(r->data)) {
-        printf("cannot malloc\n");
+        printf("cannot calloc\n");
         exit(1);
     }
 
diff --git a/tools/hitag2crack/crack3/ht2crack3.c b/tools/hitag2crack/crack3/ht2crack3.c
index b341aedce..a81d33d40 100644
--- a/tools/hitag2crack/crack3/ht2crack3.c
+++ b/tools/hitag2crack/crack3/ht2crack3.c
@@ -332,9 +332,9 @@ int main(int argc, char *argv[]) {
 
     // read in nR aR pairs
     numnrar = 0;
-    buf = (char *)malloc(lenbuf);
+    buf = (char *)calloc(1, lenbuf);
     if (!buf) {
-        printf("cannot malloc buf\n");
+        printf("cannot calloc buf\n");
         exit(1);
     }
 
@@ -368,9 +368,9 @@ int main(int argc, char *argv[]) {
     printf("Loaded %u NrAr pairs\n", numnrar);
 
     // create table of thread data
-    tdata = (struct threaddata *)malloc(sizeof(struct threaddata) * NUM_THREADS);
+    tdata = (struct threaddata *)calloc(1, sizeof(struct threaddata) * NUM_THREADS);
     if (!tdata) {
-        printf("cannot malloc threaddata\n");
+        printf("cannot calloc threaddata\n");
         exit(1);
     }
 
diff --git a/tools/hitag2crack/crack4/ht2crack4.c b/tools/hitag2crack/crack4/ht2crack4.c
index f4ebb2459..3c2680692 100644
--- a/tools/hitag2crack/crack4/ht2crack4.c
+++ b/tools/hitag2crack/crack4/ht2crack4.c
@@ -225,9 +225,9 @@ static uint64_t packstate(uint64_t s) {
 
 /* create_guess_table mallocs the tables */
 static void create_guess_table(void) {
-    guesses = (struct guess *)malloc(sizeof(struct guess) * maxtablesize);
+    guesses = (struct guess *)calloc(1, sizeof(struct guess) * maxtablesize);
     if (!guesses) {
-        printf("cannot malloc guess table\n");
+        printf("cannot allocate memory for guess table\n");
         exit(1);
     }
 }
@@ -264,9 +264,9 @@ static void init_guess_table(char *filename, char *uidstr) {
     }
 
     num_nRaR = 0;
-    buf = (char *)malloc(lenbuf);
+    buf = (char *)calloc(1, lenbuf);
     if (!buf) {
-        printf("cannot malloc buf\n");
+        printf("cannot calloc buf\n");
         exit(1);
     }
 
diff --git a/tools/hitag2crack/crack5gpu/ht2crack5gpu.c b/tools/hitag2crack/crack5gpu/ht2crack5gpu.c
index 9074d8bd1..77d470f22 100644
--- a/tools/hitag2crack/crack5gpu/ht2crack5gpu.c
+++ b/tools/hitag2crack/crack5gpu/ht2crack5gpu.c
@@ -261,9 +261,9 @@ int main(int argc, char *argv[]) {
         exit(1);
     }
 
-    ctx.kernelSource = (char *)malloc(filestat.st_size);
+    ctx.kernelSource = (char *)calloc(1, filestat.st_size);
     if (!ctx.kernelSource) {
-        printf("Cannot malloc kernelSource\n");
+        printf("Cannot calloc kernelSource\n");
         exit(1);
     }
 
diff --git a/tools/hitag2crack/crack5opencl/ht2crack5opencl.c b/tools/hitag2crack/crack5opencl/ht2crack5opencl.c
index 74fbd3764..da23bd451 100644
--- a/tools/hitag2crack/crack5opencl/ht2crack5opencl.c
+++ b/tools/hitag2crack/crack5opencl/ht2crack5opencl.c
@@ -413,9 +413,9 @@ int main(int argc, char **argv) {
     int freeListIdx = 0;
 
     // todo, calculate the max number of allocations to remove 0x40
-    void **freeList = (void **) malloc(0x40 * sizeof(void *));
+    void **freeList = (void **) calloc(1, 0x40 * sizeof(void *));
     if (!freeList) {
-        printf("Error: malloc (freeList) failed (%d): %s\n", errno, strerror(errno));
+        printf("Error: calloc (freeList) failed (%d): %s\n", errno, strerror(errno));
         exit(3);
     }
 
diff --git a/tools/hitag2crack/crack5opencl/queue.c b/tools/hitag2crack/crack5opencl/queue.c
index fcb1f3a0a..606394d42 100644
--- a/tools/hitag2crack/crack5opencl/queue.c
+++ b/tools/hitag2crack/crack5opencl/queue.c
@@ -249,9 +249,9 @@ int wu_queue_push(wu_queue_ctx_t *ctx, size_t id, size_t off, size_t max) {
 
     if (ctx->queue_head == 0) first = 1;
 
-    if (!(ptr = (wu_queue_item_t *) malloc(sizeof(wu_queue_item_t)))) {
+    if (!(ptr = (wu_queue_item_t *) calloc(1, sizeof(wu_queue_item_t)))) {
 #if TEST_UNIT == 1
-        fprintf(stderr, "! Error: malloc() failed (%d): %s\n", errno, strerror(errno));
+        fprintf(stderr, "! Error: calloc() failed (%d): %s\n", errno, strerror(errno));
 #endif
         pthread_mutex_unlock(&ctx->queue_mutex);
         return ERROR_ALLOC;
diff --git a/tools/mf_nonce_brute/mf_nonce_brute.c b/tools/mf_nonce_brute/mf_nonce_brute.c
index 35edc9387..32b4d9bd6 100644
--- a/tools/mf_nonce_brute/mf_nonce_brute.c
+++ b/tools/mf_nonce_brute/mf_nonce_brute.c
@@ -641,7 +641,7 @@ int main(int argc, char *argv[]) {
 
     // threads
     for (int i = 0; i < thread_count; ++i) {
-        struct thread_key_args *b = malloc(sizeof(struct thread_key_args));
+        struct thread_key_args *b = calloc(1, sizeof(struct thread_key_args));
         b->thread = i;
         b->idx = i;
         b->uid = uid;
