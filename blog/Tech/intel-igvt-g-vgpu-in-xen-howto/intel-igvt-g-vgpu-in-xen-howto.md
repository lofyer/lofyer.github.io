---
title: "Intel iGVT-g vGPU in Xen - XenGT howto"
date: "2015-01-19"
categories: 
  - "cloud-infra"
---

Here's the original article. [Xen - Intel GVT-g(XenGT) Pubic Release](https://01.org/zh/xen/blogs/wangbo85/2014/intel-gvt-gxengt-pubic-release?langredirect=1 "Xen - Intel GVT-g(XenGT) Pubic Release")

Here's my env. CPU: i5-4460 OS: Ubuntu 14.04 Desktop

Gernerally, you can follow the [XenGT\_Setup\_Guide.pdf](https://github.com/01org/XenGT-Preview-kernel/raw/master/XenGT_Setup_Guide.pdf "Guide").

Before the kernel building, you may copy /boot/config-3.XX to the XenGT-Preview-kernel/.config.old to make a most adaptive kernel.

Please note that, direct mode will show vm-display to your monitor connected with your iGPU, indirect mode will not show anything to your monitor but [a dummy monitor](http://blog.lofyer.org/create-dummy-vga-adpter/ "Create a dummy VGA adpter") instead, and you can connect to it with VNC or RDP.

And here's the grub menuentry you should add.

menuentry 'Ubuntu, with Linux 3.17.0-rc4-vgt' --class ubuntu --class gnu-linux --class gnu --class os $menuentry\_id\_option 'gnulinux-3.17.0-rc4-vgt-advanced-0c268053-24fb-4ff7-8d2a-053a9006ef50' {
                insmod part\_msdos
                insmod ext2
                set root='hd0,msdos1'
                if \[ x$feature\_platform\_search\_hint = xy \]; then
                  search --no-floppy --fs-uuid --set=root --hint-bios=hd0,msdos1 --hint-efi=hd0,msdos1 --hint-baremetal=ahci0,msdos1  4c4f10ff-0c03-40a9-bba7-72fa86fb4b61
                else
                  search --no-floppy --fs-uuid --set=root 4c4f10ff-0c03-40a9-bba7-72fa86fb4b61
                fi
                echo    'Loading Linux 3.17.0-rc4-vgt ...'
                multiboot /xen-vgt.gz dom0\_mem=2048M loglvl=all guest\_loglvl=all conring\_size=4M noreboot
                module  /vmlinuz-vgt-3.17.0-rc4-vgt placeholder root=UUID=0c268053-24fb-4ff7-8d2a-053a9006ef50 ro  quiet splash KEYBOARDTYPE=pc KEYTABLE=us rd\_NO\_DM ignore\_loglevel console=tty0 console=hvc0 consoleblank=0 log\_buf\_len=4M xen\_vgt.hvm\_boot\_foreground=1 
                module /initrd-vgt-3.17.0-rc4-vgt.img        
}

If it occurs that you could not create a new VM with vgt=1, just 'cat /sys/kernel/vgt/control/available\_resources' to see how many resources left. **You may change Graphic Memory allocated for your graphic card in BIOS configuration，and it may work.**.

Below content comes from [here](https://github.com/01org/KVMGT-kernel/blob/master/XenGT-API.txt).

3.1.8 Remaining graphics memory size,the number of fence regs
PATH:		/sys/kernel/vgt/control/available\_resources

DESCRIPTION:	This entry shows remaining free CPU visible graphics memory size,
                available CPU invisible graphics memory size and available
		fence registers.It can be used to determine how many VMs with
		VGT instance can still be created.
		The output consists of 3 lines in hexadecimal and looks like this:
		(Using "\\" to represent the continuing of the same line)

		0x00000200, 0x00000180, 0x00000600, 0x00000480, 0x00000010, 0x0000000c\\
		00000000,00000000,00000000,00000000,00000000,00000000,00000000,\\
		00000000,00000000,00000000,00000000,00000000,00000000,00000000,\\
		00000000,00000000,00000000,00000000,00000000,00000000,00000000,\\
		00000000,00000000,00000000,00000000,00000000,00000000,00000000,\\
		00000000,00000000,00000000,00000000,00000000,00000000,00000000,\\
		00000000,ffffffff,ffffffff,ffffffff,ffffffff,ffffffff,ffffffff,\\
		ffffffff,ffffffff,ffffffff,ffffffff,ffffffff,ffffffff,ffffffff,\\
		ffffffff,00000000,00000000,00000000,00000000,00000000,00000000,\\
		00000000,00000000,00000000,00000000,00000000,00000000,ffffffff,\\
		ffffffff
                000f

		The first line shows 6 numbers: 
                    total CPU visible Graphics memory size,
		    free CPU visible graphics memory size, 
                    toal CPU invisible graphics memory size,
		    free CPU invisible graphics memory size, 
                    total number of fence register,
		    the number of free fence registers. (Note: the first 4 are in 'MB').
		The second and third line show the bitmap of graphics memory and
		fence register allocation. Bits of "1" mean the resources have
		been taken.
