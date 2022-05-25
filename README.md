commit 48cfb255ef4b843cfbce9793bcec0ef09b304f7b
Author: iceman1001 <iceman@iuse.se>
Date:   Sun Jun 20 08:10:41 2021 +0200

    fix coverity CID 349309

diff --git a/client/src/nfc/ndef.c b/client/src/nfc/ndef.c
index 43f0b085d..5ecddc09f 100644
--- a/client/src/nfc/ndef.c
+++ b/client/src/nfc/ndef.c
@@ -250,7 +250,6 @@ static int ndef_print_signature(uint8_t *data, uint8_t data_len, uint8_t *signat
     PrintAndLogEx(NORMAL, "");
     PrintAndLogEx(INFO, "--- " _CYAN_("NDEF Signature"));
     if (is_valid == false || i == ARRAYLEN(ndef_public_keys)) {
-        PrintAndLogEx(INFO, "    Elliptic curve parameters: %s", get_curve_name(ndef_public_keys[i].grp_id));
         PrintAndLogEx(INFO, "               NDEF Signature: %s", sprint_hex_inrow(signature, 32));
         PrintAndLogEx(SUCCESS, "       Signature verification: " _RED_("failed"));
         return PM3_ESOFT;
