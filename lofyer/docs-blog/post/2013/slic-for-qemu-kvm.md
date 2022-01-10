---
title: "Slic for qemu-kvm"
date: "2013-12-25"
categories: 
  - "cloud"
---

This article is a howto for activation-ready of Windows. SLIC 2.0 is for 2003 & XP, and 2.1 for Win7 & 2008 Original seabios reads slic table from exactly the host. However, if your motherboard(not OEM) happen to own none, you will most probably make one by your self.

### Seabios

You could get the lastest code from here.

git clone git://git.seabios.org/seabios.git seabios

Or, you can download from here.

[seabios-1.7.2.2.tar.gz](http://code.coreboot.org/p/seabios/downloads/get/seabios-1.7.2.2.tar.gz "seabios-1.7.2.2.tar.gz")

### SLIC-BIN

Here's a collection of various slic table. [SLIC 2.1 BINS](http://blog.lofyer.org/slic-for-qemu-kvm/slic-2-1-bins/)

### Seaslic patch

This is patch for seabios to make it slic table enabled. Download from here.

[Seaslic.tar.xz, seabios-1.7.2 compatible](https://cloud.lofyer.org/public.php?service=files&t=3aa0db051506d88bce8e6d03d621f47e "Seaslic.tar.xz") Here's the patch content.

\--- a/src/acpi.c	2013-01-19 06:44:54.000000000 +0600
+++ b/src/acpi.c	2013-05-07 01:16:30.000000000 +0600
@@ -214,6 +214,11 @@

 #include "acpi-dsdt.hex"

+#define CONFIG\_OEM\_SLIC
+#ifdef CONFIG\_OEM\_SLIC
+#include "acpi-slic.hex"
+#endif
+
 static void
 build\_header(struct acpi\_table\_header \*h, u32 sig, int len, u8 rev)
 {
@@ -226,6 +231,10 @@
     h->oem\_revision = cpu\_to\_le32(1);
     memcpy(h->asl\_compiler\_id, CONFIG\_APPNAME4, 4);
     h->asl\_compiler\_revision = cpu\_to\_le32(1);
+    #ifdef CONFIG\_OEM\_SLIC
+    if (sig == RSDT\_SIGNATURE) // only RSDT is checked by win7 & vista
+	memcpy(h->oem\_id, ((struct acpi\_table\_header\*)SLIC)->oem\_id, 14);
+    #endif
     h->checksum -= checksum(h, len);
 }

@@ -827,6 +836,15 @@
     ACPI\_INIT\_TABLE(build\_srat());
     if (pci->device == PCI\_DEVICE\_ID\_INTEL\_ICH9\_LPC)
         ACPI\_INIT\_TABLE(build\_mcfg\_q35());
+    #ifdef CONFIG\_OEM\_SLIC
+	void \*buf = malloc\_high(sizeof(SLIC));
+	if (!buf)
+	    warn\_noalloc();
+	else {
+	    memcpy(buf, SLIC, sizeof(SLIC));
+	    ACPI\_INIT\_TABLE(buf);
+	}
+    #endif

     u16 i, external\_tables = qemu\_cfg\_acpi\_additional\_tables();

### Compile

You don't have to apply the seaslic patch with patch.sh, you can do that by hand. Before you start, do this:

xxd -i /sys/firmware/acpi/tables/SLIC | grep -v len | sed 's/unsigned ch   ar.\*/static char SLIC\[\] = {/' > seabios.submodule/src/acpi-slic.hex

Or,

xxd -i DELL.BIN | grep -v len | sed 's/unsigned ch   ar.\*/static char SLIC\[\] = {/' > seabios.submodule/src/acpi-slic.hex

After applying the patch, you can compile the bios.bin, and copy that to **/usr/share/qemu-kvm/my-bios.bin** or rewrite **bios.bin** instead. Here's my bios.bin with Dell\[DELL-QA09-NVDA\]2.1.BIN from SLIC BIN

[my-bin.tar.xz](https://raw.githubusercontent.com/lofyer/qemu-cmd-reloaded/master/qemu-slic/my-bin.tar.xz)

### Qemu-cmd

qemu-kvm XXX -bios /usr/share/qemu-kvm/my-bios.bin -acpitable file=Dell\[DELL-QA09-NVDA\]2.1.BIN

In the guest, you could see that SLIC by /sys/firmware/acpi/tables/SLIC in Linux or SLIC\_Toolkit in Windows.
