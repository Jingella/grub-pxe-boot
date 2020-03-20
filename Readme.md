
# Linux PXE Grub Boot files

This is a collection of boot files, not including relevant linux boot files, however they can usually be easily
obtained or built.

# Grub installation

To correctly build the grub directory, use the following command;

```bash
apt-get install -y grub-efi-amd64 grub-efi-amd64-bin grub-efi-amd64-signed \
              grub-imageboot grub-pc-bin grub2-splashimages shim shim-signed
grub-mknetdir --net-directory=<tftp root folder>
```
This should generate the following output;
```
Netboot directory for i386-pc created. Configure your DHCP server to point to <tftp root folder>/boot/grub/i386-pc/core.0
Netboot directory for x86_64-efi created. Configure your DHCP server to point to <tftp root folder>/boot/grub/x86_64-efi/core.efi
```

Please note at this point, the `core.efi` file provided is *not* signed.  In order to provide a signed grub EFI image, it
must be copied from `grub-efi-amd64-signed` package.

```bash
cp /usr/lib/grub/x86_64-efi-signed/grubnetx64.efi.signed <tftp root folder>/boot/grub/x86_64-efi/grubx64.efi
```

The `signed` grub binary is still signed by the linux vendor and cannot be used for secure boot. The `shim-signed` package provides a signed bootable EFI binary which can chain-load grub.

```bash
cp /usr/lib/shim/shimx64.efi.signed <tftp root folder>/boot/grub/x86_64-efi/shimx64.efi
```

Ensure the dhcp boot server (option 67) points either to the `grubx64.efi` file directly, or to the `shimx64.efi` file to support secure boot without loading generic vendor certificates..

### Notes:

1. This collection has received very limited testing with secure boot

2. This repository includes a '.gitattributes' file to ensure filetypes are detected correctly.  The file type definitions
are stored in .gitconfig at the root of the repository. To [optionally] enable this configuration, use the following command:
    ```bash
    git config include.path ../.gitconfig
    ```

 3. Secure boot will not load grub modules, so configuration is limited to functions built into `grubnet64.efi`
