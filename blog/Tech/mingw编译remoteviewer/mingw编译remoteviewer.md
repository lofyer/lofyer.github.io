---
title: "mingw编译remoteviewer"
date: "2012-11-17"
categories: 
  - "cloud-infra"
---

**UPDATE 2015.3.18 fedora 21中编译： # yum install mingw\* libtool\* auto-buildrequires intltool glib2-devel icoutils msitools # ./autogen.sh # mingw64-configure # mingw64-make -j4 # rpmbuild -bb mingw-virt-viewer.spec 编译msi过程中，可能会提示缺少storageencryption.rng，从以下地址下载即可。 https://raw.githubusercontent.com/wido/libvirt/master/docs/schemas/storageencryption.rng**

在fedora环境中，需要的有spice-gtk，libusbx，usbredir，remote-review，全部下载最新git源 spice-gtk，usbredir http://cgit.freedesktop.org/spice libusbx git://github.com/libusbx/libusbx.git remote-viewer git.fedorahosted.org/git/virt-viewer.git

libusbx和usbredir：

mingw32-configure;mingw32-make;mingw32-make install

spice-gtk： mingw32-configure --with-sasl=no --with-audio=gstreamer --enable-smartcard=no --with-gtk=2.0 --without-python 因为virtviewer要gtk2.0的，所以这也就2.0的；mingw32下没找到pygtk，图省事儿就不要python了，其他的缺啥下啥

mingw32-make
mingw32-make install

virt-viewer：

mingw32-configure
mingw32-make
mingw32-make install

生成remote viewer.exe

安装包 运行nsiswrapper生成windows安装包

nsiswrapper --run 
    --name "Virt-Viewer" 
    --outfile "Virt-Viewer-for-Windows.exe" 
    --with-gtk 
    /usr/i686-pc-mingw32/sys-root/mingw/bin/virt-viewer.exe

缺少的dll从网上下或者windows环境中安装的virtviewer加到$PATH中 这样会缺少三个xml文件，在virtviewer/src里，直接copy过去

export PATH=$PATH:/usr/i686-pc-mingw32/sys-root/mingw/bin:.

windows client中使用usbredir： 需要libwdi或者zadig或者usbclerk [链接](http://lists.freedesktop.org/archives/spice-devel/2012-November/011629.html "usbredir in windows client")

todo: 只有libvirt不是最新的了，可是目前来看没影响
