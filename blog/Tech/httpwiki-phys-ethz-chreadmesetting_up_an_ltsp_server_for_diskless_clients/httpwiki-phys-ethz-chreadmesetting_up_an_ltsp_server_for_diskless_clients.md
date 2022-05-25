---
title: "ltsp相关"
date: "2013-03-15"
categories: 
  - "linux-admin"
---

参考： https://help.ubuntu.com/community/UbuntuLTSP

安装： apt-get install ltsp-server-standalone

启动要素： nbd-server dhcpd tftp-hpa openssh-server

绑定客户端地址： \[dhcpd.conf\] host client201 { hardware ethernet 08:00:27:89:70:01; fixed-address 192.168.0.201; } 另外一种是在启动pxe配置文件中指定 http://wiki.phys.ethz.ch/readme/setting\_up\_an\_ltsp\_server\_for\_diskless\_clients

session & windows \[/usr/share/xsession/\*\] Exec=/root/.xsession

\[/root/.xsession\] #!/bin/bash gnome-session & firefox logout

获取session list \[/usr/share/ldm/ldminfod\] \[/etc/X11/xinit/Xsession\] \[/etc/X11/Xsession\] \*\*failsafe \[/etc/X11/xdm/Xsession\] \[/usr/lib/X11/xdm/Xsession\] \*\[/usr/share/xsession\]

\*\*default session exported by Xsession.d echo $DEFAULTS\_PATH /usr/share/gconf/

修改default session \[/var/lib/tftp.../lts.conf\] LDM\_SESSION="gnome-session &;firefox;logout"

FatClient \[/var/lib/tftp.../lts.conf\] \[default\] LDM\_DIRECTX=true

\[00:A1:08:EB:43:27\] LTSP\_FATCLIENT=false

AutoLogin \[/var/lib/tftp.../lts.conf\] \[Default\] LDM\_AUTOLOGIN = True

\[192.168.1.101\] LDM\_USERNAME = user1 LDM\_PASSWORD = password1

\[192.168.1.102\] LDM\_USERNAME = user2 LDM\_PASSWORD = password2

一些参考配置 \[lts.conf\] # Global defaults for all clients # if you refer to the local server, just use the # "server" keyword as value # see lts\_parameters.txt for valid values ################ \[default\] X\_COLOR\_DEPTH=24 LOCALDEV=True SOUND=True USE\_LOCAL\_SWAP=True NBD\_SWAP=False SYSLOG\_HOST=server #XKBLAYOUT=de SCREEN\_02=shell SCREEN\_03=shell SCREEN\_04=shell SCREEN\_05=shell SCREEN\_06=shell SCREEN\_07=ldm # LDM\_DIRECTX=True allows greater scalability and performance # Turn this off if you want greater security instead. LDM\_DIRECTX=True # LDM\_SYSLOG=True writes to server's syslog LDM\_SYSLOG=True
