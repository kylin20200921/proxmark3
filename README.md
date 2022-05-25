commit 04d7f18e551324c2ad51c9f541b488a3802d5349
Author: iceman1001 <iceman@iuse.se>
Date:   Fri Dec 31 10:52:28 2021 +0100

    text

diff --git a/doc/desfire.md b/doc/desfire.md
index 6739d3322..95bdc4c58 100644
--- a/doc/desfire.md
+++ b/doc/desfire.md
@@ -1,6 +1,35 @@
+<a id="Top"></a>
+
 # DESFire card
 
+# Table of Contents
+
+- [DESFire card](#desfire-card)
+- [Table of Contents](#table-of-contents)
+  - [Documentation](#documentation)
+  - [Source code](#source-code)
+  - [Communication channel with a card](#communication-channel-with-a-card)
+  - [Card architecture](#card-architecture)
+  - [Card structure](#card-structure)
+  - [DESFire Light](#desfire-light)
+  - [How to](#how-to)
+    - [How to get card UID](#how-to-get-card-uid)
+    - [How to get/set default communication channel settings](#how-to-getset-default-communication-channel-settings)
+    - [How to guess default communication channel settings](#how-to-guess-default-communication-channel-settings)
+    - [How to try communication channel settings](#how-to-try-communication-channel-settings)
+    - [How to look at the application list on the card](#how-to-look-at-the-application-list-on-the-card)
+    - [How to look/dump files from the application file list](#how-to-lookdump-files-from-the-application-file-list)
+    - [How to change key](#how-to-change-key)
+    - [How to create the application](#how-to-create-the-application)
+    - [How to create files](#how-to-create-files)
+    - [How to delete files](#how-to-delete-files)
+    - [How to read/write files](#how-to-readwrite-files)
+    - [How to work with transaction mac](#how-to-work-with-transaction-mac)
+    - [How to switch DESFire Light to LRP mode](#how-to-switch-desfire-light-to-lrp-mode)
+
+
 ## Documentation
+^[Top](#top)
 
 [DESFire Light datasheet MF2DL(H)x0](https://www.nxp.com/docs/en/data-sheet/MF2DLHX0.pdf)
 
@@ -21,6 +50,7 @@
 [Symmetric key diversifications AN10922](https://www.nxp.com/docs/en/application-note/AN10922.pdf)
 
 ## Source code
+^[Top](#top)
 
 [desfire_crypto from proxmark3](https://github.com/RfidResearchGroup/proxmark3/blob/master/armsrc/desfire_crypto.c)
 
@@ -36,7 +66,8 @@
 
 [LRP/ev2 nfc-ev2-crypto](https://github.com/icedevml/nfc-ev2-crypto)
 
-## Communication channel with a card:
+## Communication channel with a card
+^[Top](#top)
 
 The card can work with a combination of: key type - command set - secure channel - communication mode
 
@@ -75,6 +106,7 @@ The card can work with a combination of: key type - command set - secure channel
 **encrypted** - encrypted data in the request/response/both in the ev2 channel data signed with mac.
 
 ## Card architecture
+^[Top](#top)
 
 The card has several applications on it and the applications have files and some other objects.
 
@@ -84,7 +116,8 @@ Master application has many keys with different purposes, but commands show that
 
 Each application may have its own key type and set of keys. Each file can only have links to these keys in its access rights.
 
-## Card structure:
+## Card structure
+^[Top](#top)
 
 - Application
 - Application number: 1 byte
@@ -101,7 +134,8 @@ Each application may have its own key type and set of keys. Each file can only h
   - File communication mode: plain/maced/encrypted
   - File access right: there are 4 modes: read/write/read-write/change settings. And each mode access can be: key0..keyD, E - free access, F - deny access
 
-## DESFire Light:
+## DESFire Light
+^[Top](#top)
 
 The card has one preinstalled master file (ISO ID 0x3f00) and one application (0xdf01)
 
@@ -131,7 +165,9 @@ If it needs to have more space for FCI - just change the ID of one of the bigger
 
 ## How to
 
+
 ### How to get card UID
+^[Top](#top)
 
 The card can return UID in encrypted communication mode. Needs to authenticate with any key from the card.
 
@@ -142,6 +178,7 @@ The card can return UID in encrypted communication mode. Needs to authenticate w
 `hf mfdes getuid -s ev2 -t aes -k 11223344556677889900112233445566` - via ev2 secure channel with specified aes key
 
 ### How to get/set default communication channel settings
+^[Top](#top)
 
 All the commands use these settings by default if a more important setting is not specified in the command line.
 
@@ -150,6 +187,7 @@ All the commands use these settings by default if a more important setting is no
 `hf mfdes default -n 1 -t aes` - set key number 1 and key type aes
 
 ### How to guess default communication channel settings
+^[Top](#top)
 
 `hf mfdes detect` - simply detect key for master application (PICC level)
 
@@ -162,12 +200,14 @@ All the commands use these settings by default if a more important setting is no
 `hf mfdes detect --aid 123456 -n 2` - detect key 2 from application with AID 123456
 
 ### How to try communication channel settings
+^[Top](#top)
 
 `hf mfdes auth -n 0 -t des -k 1122334455667788 --aid 123456` - try application 123456 master key
 
 `hf mfdes auth -n 0 -t aes --save` - try PICC AES master key and save the configuration to defaults if authentication succeeds
 
 ### How to look at the application list on the card
+^[Top](#top)
 
 `hf mfdes lsapp --no-auth` - show applications list without authentication
 
@@ -178,12 +218,14 @@ All the commands use these settings by default if a more important setting is no
 `hf mfdes getaids --no-auth` - this command can return a simple AID list if it is enabled in the card settings
 
 ### How to look/dump files from the application file list
+^[Top](#top)
 
 `hf mfdes lsfiles --aid 123456 -t aes` - file list for application 123456 with aes key
 
 `hf mfdes dump --aid 123456` - shows files and their contents from application 123456
 
 ### How to change key
+^[Top](#top)
 
 Changing key algorithm can be done only in one case - change card master key.
 
@@ -196,12 +238,14 @@ Key algorithm for application can be chosen only on its creation.
 `hf mfdes changekey --aid 123456 -t des -n 0 --newkeyno 1 --oldkey 5555555555555555 --newkey 1122334455667788` - change key 1 with authentication with key 0 (app master key)
 
 ### How to create the application
+^[Top](#top)
 
 `hf mfdes createapp --aid 123456 --fid 2345 --dfname aid123456 --dstalgo aes` - create an application with ISO file ID, df name, and key algorithm AES
 
 `hf mfdes createapp --aid 123456` - create an application 123456 with DES key algorithm and without ISO file ID. in this case, iso file id can't be provided for application's files
 
 ### How to create files
+^[Top](#top)
 
 `hf mfdes createfile --aid 123456 --fid 01 --isofid 0001 --size 000010` - create standard file with ISO ID and default access settings
 
@@ -220,10 +264,12 @@ Create standard file with mac access mode and specified access settings. access
 `hf mfdes createmacfile --aid 123456 --fid 01 --rawrights 0FF0 --mackey 00112233445566778899aabbccddeeff --mackeyver 01` - create transaction mac file
 
 ### How to delete files
+^[Top](#top)
 
 `hf mfdes deletefile --aid 123456 --fid 01` - delete file
 
 ### How to read/write files
+^[Top](#top)
 
 *read:*
 
@@ -270,6 +316,7 @@ For more detailed samples look at the next howto.
 `hf mfdes write --aid 123456 --fid 01 -d 01020304 --readerid 010203` write data to the file with CommitReaderID command before and CommitTransaction after write
 
 ### How to work with transaction mac
+^[Top](#top)
 
 There are two types of transactions with mac: with and without the CommitReaderID command. The type can be chosen by `hf mfdes createmacfile` command.
 
@@ -328,6 +375,7 @@ step 2. write something to a file with CommitReaderID command and provide the ke
 `hf mfdes write --aid 123456 --fid 01 -d 01020304 --readerid 010203 --trkey 00112233445566778899aabbccddeeff`
 
 ### How to switch DESFire Light to LRP mode
+^[Top](#top)
 
 Remove failed authentication counters (if needs, but strongly recommended)
 
