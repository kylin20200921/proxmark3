commit b776c984222b07edffd4f109c6e94e26afebf5ba
Author: iceman1001 <iceman@iuse.se>
Date:   Sat Feb 26 17:40:24 2022 +0100

    fix copy pasta

diff --git a/client/src/flash.c b/client/src/flash.c
index c43d6fd28..373f3b785 100644
--- a/client/src/flash.c
+++ b/client/src/flash.c
@@ -306,39 +306,45 @@ int flash_load(flash_file_t *ctx, bool force) {
         res = PM3_EFILE;
         goto fail;
     }
+
     if (le16(ehdr->e_type) != ET_EXEC) {
         PrintAndLogEx(ERR, "ELF is not executable");
         res = PM3_EFILE;
         goto fail;
     }
+
     if (le16(ehdr->e_machine) != EM_ARM) {
         PrintAndLogEx(ERR, "Wrong ELF architecture");
         res = PM3_EFILE;
         goto fail;
     }
+
     if (!ehdr->e_phnum || !ehdr->e_phoff) {
         PrintAndLogEx(ERR, "ELF has no PHDRs");
         res = PM3_EFILE;
         goto fail;
     }
+
     if (le16(ehdr->e_phentsize) != sizeof(Elf32_Phdr_t)) {
         // could be a structure padding issue...
         PrintAndLogEx(ERR, "Either the ELF file or this code is made of fail");
         res = PM3_EFILE;
         goto fail;
     }
+
     ctx->num_phdrs = le16(ehdr->e_phnum);
     ctx->phdrs = (Elf32_Phdr_t *)(ctx->elf + le32(ehdr->e_phoff));
     shdrs = (Elf32_Shdr_t *)(ctx->elf + le32(ehdr->e_shoff));
-    shdrs = (Elf32_Shdr_t *)(ctx->elf + le32(ehdr->e_shoff));
     shstr = ctx->elf + le32(shdrs[ehdr->e_shstrndx].sh_offset);
 
     for (uint16_t i = 0; i < le16(ehdr->e_shnum); i++) {
+
         if (strcmp(((char *)shstr) + shdrs[i].sh_name, ".version_information") == 0) {
             vi = (struct version_information_t *)(ctx->elf + le32(shdrs[i].sh_offset));
             res = print_and_validate_version(vi);
             break;
         }
+
         if (strcmp(((char *)shstr) + shdrs[i].sh_name, ".bootphase1") == 0) {
             uint32_t offset = *(uint32_t*)(ctx->elf + le32(shdrs[i].sh_offset) + le32(shdrs[i].sh_size) - 4);
             if (offset >= le32(shdrs[i].sh_addr)) {
@@ -353,11 +359,14 @@ int flash_load(flash_file_t *ctx, bool force) {
     }
     if (res == PM3_SUCCESS)
         return res;
+
     // We could not find proper version_information
     if (res == PM3_EUNDEF)
         PrintAndLogEx(WARNING, "Unable to check version_information");
+
     if (force)
         return PM3_SUCCESS;
+
     PrintAndLogEx(INFO,  "Make sure to flash a correct and up-to-date version");
     PrintAndLogEx(INFO,  "You can force flashing this firmware by using the option '--force'");
 fail:
