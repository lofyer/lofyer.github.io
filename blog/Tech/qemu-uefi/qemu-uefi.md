---
title: "qemu uefi"
date: "2015-06-08"
categories: 
  - "cloud-infra"
---

Here's a collection of kinds of bioses.

[https://www.kraxel.org/repos/jenkins/](https://www.kraxel.org/repos/jenkins/)

Download **edk2.git-ovmf-x64\*.rpm**, and extract it:

(root)# rpm2cpi edk2.git-ovmf-x64-0-20150606.b1038.g5d832d6.noarch.rpm|cpio -mvid
./usr/share/doc/edk2.git-ovmf-x64-0
./usr/share/doc/edk2.git-ovmf-x64-0/License.txt
./usr/share/doc/edk2.git-ovmf-x64-0/README
./usr/share/edk2.git
./usr/share/edk2.git/ovmf-x64
./usr/share/edk2.git/ovmf-x64/OVMF-pure-efi.fd
./usr/share/edk2.git/ovmf-x64/OVMF-with-csm.fd
./usr/share/edk2.git/ovmf-x64/OVMF\_CODE-pure-efi.fd
./usr/share/edk2.git/ovmf-x64/OVMF\_CODE-with-csm.fd
./usr/share/edk2.git/ovmf-x64/OVMF\_VARS-pure-efi.fd
./usr/share/edk2.git/ovmf-x64/OVMF\_VARS-with-csm.fd
./usr/share/edk2.git/ovmf-x64/UefiShell.iso
22227 blocks

Run qemu with these fd bios files like this:

qemu --boot loader\_type=pflash,loader\_ro=yes,loader=OVMF\_CODE-pure-efi.fd,nvram\_template=OVMF\_VARS-pure-efi.fd XXX

or

qemu -bios OVMF-pure-efi.fd XXX
