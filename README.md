commit bd8a40716c7b99933e56a0979eddb55559f44212
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Dec 31 10:55:16 2021 +0100

    text

diff --git a/doc/emv_notes.md b/doc/emv_notes.md
index 2766e6761..60f85ebb8 100644
--- a/doc/emv_notes.md
+++ b/doc/emv_notes.md
@@ -1,7 +1,25 @@
 # EMV commands
 <a id="top"></a>
 
-### EMV Implemented parts:
+Notes on EMV works on Proxmark3
+
+# Table of Contents
+- [EMV commands](#emv-commands)
+- [Table of Contents](#table-of-contents)
+    - [EMV Implemented parts](#emv-implemented-parts)
+    - [Working parts of qVSDC](#working-parts-of-qvsdc)
+    - [`not implemented` parts of EMV](#not-implemented-parts-of-emv)
+    - [Commands](#commands)
+    - [VISA(r) transactions](#visar-transactions)
+    - [Mastercard(r) transactions](#mastercardr-transactions)
+    - [all commands](#all-commands)
+    - [Useful links](#useful-links)
+    - [EMV kernels](#emv-kernels)
+
+
+
+### EMV Implemented parts
+^[Top](#top)
 
 - Get ATR|ATS
 - Get AID by PSE (`emv pse`)
@@ -22,7 +40,7 @@
 - Check ARQC cryptogram (`not implemented`)
 - Issuer scripts processing (`not implemented`)
 
-### Working parts of qVSDC:
+### Working parts of qVSDC
 ^[Top](#top)
 
 - Get ATR|ATS
@@ -58,7 +76,7 @@ command `emv exec` executes EMV transaction. it have parameters:
 ```
 It works for VISA(r) and Mastercard(r) transactions. It may work with other EMV payment system's card (and it works in general cases that is described in EMV).
 
-### VISA(r) transactions:
+### VISA(r) transactions
 ^[Top](#top)
 
 MSD - Magnetic Stripe mode
@@ -74,7 +92,7 @@ M/Chip - contact and contactless transaction
 Different cards have different modes on/of and different behavior in them. So needs to check card in all this modes.
 MSD - compatibility mode. Now it work always. But it less secure and in near future it will be slowly) disabled.
 
-### all commands:
+### all commands
 ^[Top](#top)
 
 ```
@@ -101,7 +119,7 @@ All main commands are parts of EMV specification. Commands than not described th
 
 `emv test` - test all crypto code from emv part of proxmark.
 
-### Useful links:
+### Useful links
 ^[Top](#top)
 
 EMV specifications
diff --git a/doc/ext_flash_notes.md b/doc/ext_flash_notes.md
index 76b4d9279..4b2452d2f 100644
--- a/doc/ext_flash_notes.md
+++ b/doc/ext_flash_notes.md
@@ -1,8 +1,21 @@
 # External flash
+<a id="Top"></a>
 
 External 256kbytes flash is a unique feature of the RDV4 edition.
 
+# Table of Contents
+- [External flash](#external-flash)
+- [Table of Contents](#table-of-contents)
+  - [Addresses](#addresses)
+  - [Layout](#layout)
+  - [Page3 Layout](#page3-layout)
+  - [RSA signature](#rsa-signature)
+- [backup first!](#backup-first)
+
+
+
 ## Addresses
+^[Top](#top)
 
 Flash memory is
 
@@ -19,6 +32,7 @@ Therefore a flash address can be interpreted as such:
 ```
 
 ## Layout
+^[Top](#top)
 
 Page 0:
 * available for user data
@@ -44,6 +58,7 @@ Page 3:
   * Updating keys dictionaries doesn't require to erase page 3.
 
 ## Page3 Layout
+^[Top](#top)
 
 Page3 is used as follows by the Proxmark3 RDV4 firmware:
 
@@ -69,6 +84,7 @@ Page3 is used as follows by the Proxmark3 RDV4 firmware:
   * offset should have been 0x3FF80 but historically it's one byte off and therefore the last byte of the flash is unused
 
 ## RSA signature
+^[Top](#top)
 
 To ensure your Proxmark3 RDV4 is not a counterfeit product, its external flash contains a RSA signature of the flash unique ID.
 You can verify it with: `mem info`
@@ -105,6 +121,8 @@ Here below is a sample output of a RDV4 device.
 ```
 
 # backup first!
+^[Top](#top)
+
 To make a backup of the signature to file:
 
 `mem dump p f flash_signature_dump o 262015 l 128`
