---
title: "实用笔记"
date: 2012-09-18
categories: 
  - "linux-admin"
---

# 实用笔记

## WebFramework:

Django Cappuccino->mockingbird

## 搜索Tab

grep -P "t"

## 批量替换

sed -i "s/qwe/asd/g" \`grep -rl "qwe"\`

## cmake 配置安装完opencv后，将安装路径中到lib/pkg-config/opencv.pc复制到/usr/lib/pkg-config，然后可以进行pkg-config 查找

## gnome3任务栏快捷方式的存放位置拖到浏览器或者终端里你就看见了。。

## curl POST

curl -X POST -d '4142430A02000' 
http://user:pass@myhost/hudson/job/\_jobName\_/postBuildResult

curl -X POST -d @myfilename http://user:pass@myhost/hudson/job/\_jobName\_/postBuildResult

## ddwrt 内网不能访问内网域名

iptables -t nat -I POSTROUTING -o br0 -s 192.168.0.188/24 -d 192.168.0.188/24 -j MASQUERADE

## vpn的内网的问题

iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o eth0 -j SNAT --to-source 70.8.7.6

iptables -t nat -A POSTROUTING -o eth0 -s 172.16.1.0/24 -j MASQUERADE

## delete git branch

git push origin :yourbranch

## ipa client reinstall

删除_/etc/ipa/ca.crt_证书

## fedora制作安装cd

#!/bin/bash
livecd-creator --config=/usr/share/spin-kickstarts/fedora-livecd-desktop.ks \\
--fslabel=Test \\
--tmpdir=/root/iso/tmp \\
--cache=/root/iso/cache \\
--nocleanup \\
--cacheonly #为节约下载时间可在以后制作时忽略

## mysql grant

grant all privileges on \*.\* to root@'%' identified by '123456';
flush privileges;

## 打开kvm nested参数

cat /sys/module/kvm\_intel/parameters/nested

为N则

modprobe -r kvm-intel
modprobe kvm-intel nested=1

## Fedora滚动升级版 Rawhide

yum install fedora-release-rawhide
yum-config-manager --disable fedora updates updates-testing
yum-config-manager --enable rawhide
yum update yum
yum --releasever=rawhide distro-sync --nogpgcheck

or

dnf upgrade --refresh
dnf install dnf-plugin-system-upgrade
dnf system-upgrade download --refresh --releasever=rawhide --allowerasing
dnf system-upgrade reboot

## Opensuse滚动升级版 Tumbleweed

zypper ar --refresh http://download.opensuse.org/repositories/openSUSE:/Tumbleweed/standard/ Tumbleweed
zypper ar --refresh http://download.opensuse.org/distribution/openSUSE-current/repo/oss/ 'openSUSE Current OSS'
zypper ar --refresh http://download.opensuse.org/distribution/openSUSE-current/repo/non-oss/ 'openSUSE Current non-OSS'
zypper ar --refresh http://download.opensuse.org/update/openSUSE-current/ 'openSUSE Current updates'
zypper ar --refresh http://download.opensuse.org/update/openSUSE-non-oss-current/ 'openSUSE Current non-OSS updates' 
zypper dup

## vs2012修复补丁

由于升级.net 4.5会出现vs2012部分项目失败，可以打这个补丁修复 [Microsoft Visual Studio 2012 更新 (KB2781514)](http://www.microsoft.com/zh-cn/download/details.aspx?id=36020 "http://www.microsoft.com/zh-cn/download/details.aspx?id=36020")

## RESTClient

针对RESTful的API可以使用这个工具 [http://restclient.org](http://restclient.org "http://restclient.org")

## dl-ssl.google.com

74.125.237.1

## owncloud repo

http://software.opensuse.org/download/package?project=isv:ownCloud:devel&package=owncloud-client

## android google play

cp /data/app/googleplay /system/app/

## 获取Gravatar头像

curl http://www.gravatar.com/avatar/\`echo -n me@mydomain.com | md5sum\` > gravatar.jpg

## Maven insecure SSL

\-Dmaven.wagon.http.ssl.insecure=true -Dmaven.wagon.http.ssl.allowall=true

## bVNC build

FreeRDP: checkout dbbb341; cmake .; ant release Singed keystore: keytool -genkey -alias android.keystore -keyalg RSA -validity 20000 -keystore android.keystore http://www.mail-archive.com/spice-devel@lists.freedesktop.org/msg13763.html

## IPA ADD-USER

#!/bin/bash
echo -e "pswd4admin\\n" | kinit admin
echo -e "$1\\n$1\\n$1\\n" | ipa user-add
echo -e "$1\\n$1\\n" | ipa passwd $1
sleep 1
echo -e "$1\\n$1\\n$1\\n" | kinit $1

## NTP server

server 192.168.1.11 mask 255.255.0.0 nomodify 启动ntpd后要等个几分钟，client才能去同步，否则报错说未找到合适的ntp服务器

## rsync

rsync -zvrtopgal -e ssh --delete --stats --progress --exclude 'ignore.txt' root@lofyer.org:/var/log /home/lofyer/lofyer.org/

## CentOS Smartcard

SmartCard service: openct pcscd

## zathura

很好的PDF阅读器

## ssh without password

# Generate rsa key pair without passphrase
\[demo@A ~\]$ ssh-keygen -t rsa
# Copy pub key to B
\[demo@A ~\]$ cat .ssh/id\_rsa.pub | ssh alpha@B 'cat >> .ssh/authorized\_keys2'
# Add private key to A
\[demo@A ~\]$ ssh-add  ~/.ssh/id\_rsa
or just
\[demo@A ~\]$ ssh-copy-id alpha@B

## ssh to a new host without key checking promption

# vim /etc/ssh/ssh\_config
...
StrictHostKeyChecking no
...

## debian offcie乱码

安装文泉逸字体 apt-get install fonts-wenquanyi

## debian virt-viewer with spice

deb http://mirrors.163.com/debian/ unstable main
deb-src http://mirrors.163.com/debian/ unstable main

deb http://security.debian.org/ unstable/updates main
deb-src http://security.debian.org/ unstable/updates main

# unstable-updates, previously known as 'volatile'
deb http://mirrors.163.com/debian/ unstable-updates main
deb-src http://mirrors.163.com/debian/ unstable-updates main
deb http://apt.wxwidgets.org/ unstable-wx main
deb-src http://apt.wxwidgets.org/ unstable-wx main

## Better DNS

dns1: 199.91.73.222 dns2: 8.8.8.8

## audit log

Modify _/etc/audit/audit.conf_ to 8192 or bigger.

## memcached

**PHP**

# apt-get install memcached php5-memcached

modify _/etc/php5/conf.d/memcached.ini_

## disable TSO GSO to improve virtio-net

# ethtool -K eth0 gso off
# ethtool -K eth0 tso off

## disable PackageKit in Fedora

_/etc/yum/pluginconf.d/refresh-packagekit.conf_

 enabled=0

## 推荐一种工作方式

集成显卡下运行linux，将另一块硬盘或者vdisk，还有独立显卡passthrough给虚拟机Win7，协议使用spice，这是很爽啊

## crt & key to pem

openssl x509 -inform DER -outform PEM -in server.crt -out server.crt.pem openssl rsa -inform DER -outform PEM -in server.key -out server.key.pem cat server.crt.pem server.key.pem >> server.pem

## wireless relay for tomata

设置无线中继时确保主路由和从路由的SSID、密码、加密方式一致，不要使用WPA/WPA-2混合模式

## libvirt password

# saslpasswd2 -a libvirt root

## netcat(nc) tips

**大传输文件(file transfer)**

server: nc -l 5555 < file\_to\_copy
client: nc server\_ip 5555 > new\_file

**聊天(chat)**

client1: nc -l 5555
client2: nc server\_ip 5555

**udp connection**

server: nc -4 -u -l 5555
clinet: nc -4 -u localhost 5555

## owncloud repo

\[isv\_ownCloud\_community\]
name=Latest stable community release of ownCloud (CentOS\_CentOS-6)
type=rpm-md
baseurl=http://download.opensuse.org/repositories/isv:/ownCloud:/community/CentOS\_CentOS-6/
gpgcheck=1
gpgkey=http://download.opensuse.org/repositories/isv:/ownCloud:/community/CentOS\_CentOS-6/repodata/repomd.xml.key
enabled=1

## gnome terminal proxy

$ ssh -ND 7070 yourname@yourserver
$ gconftool-2 -R /system/proxy # show configuration
$ gconftool-2 -s /system/proxy/socks\_host -t int 127.0.0.1
$ gconftool-2 -s /system/proxy/socks\_port -t int 7070
$ gconftool-2 -s /system/proxy/mode -t string manual # enable
$ gconftool-2 -s /system/proxy/mode -t string none # disable

## update xcode cmd

# xcode-select --install

## Test linux watchdog

# echo c > /proc/sysrq-trigger

## Bash环境变量漏洞检测

env x='() { :;}; echo asdasd' bash -c "echo asd"

## maven socks5 proxy

\-DsocksProxyHost=127.0.0.1 -DsocksProxyPort=7070

## When your vm has 2 ethernet, DO noticed which one is the default gateway, or you will fail in ROUTER PORT FORWARDING.

## Delete RAID info in your disk

# dmraid -n /dev/sda
  DDF1 Anchor at 12345678
# dd if=/dev/zero of=/dev/sda seek=12345678 bs=512 count=1

## Postgresql recovery when xlog failed

$ pg\_resetxlog -f -x 0x9A00000 -m 0x10000 -O 0x10000 -l 0x1,0xB67,0x58 -D /var/lib/pgsql/data
or
$ pg\_resetxlog -f /var/lib/pg\_sql/data
Transaction log reset

## 解决mysql＋php中文显示问号问题

比如在vtigercrm，zabbix中

创建utf8排序的数据库即可
# mysql
> create database mydb DEFAULT CHARSET utf8 COLLATE utf8\_general\_ci;

## Mysql delete duplicated rows

delete from posts where id not in (select \* from (select max(n.id) from posts n group by n.title) x);

OR

create table new as select \* from posts where 1 group by url;
rename table new master;

## pip upgrade all

pip freeze --local | grep -v '^\\-e' | cut -d = -f 1  | xargs -n1 pip install -U

## wget 递归下载

wget -c -r -np -k -L -p http://example.com/dir0/

## 修改gem源

$ gem sources --remove https://rubygems.org/
$ gem sources -a https://ruby.taobao.org/
$ gem sources -l
\*\*\* CURRENT SOURCES \*\*\*

https://ruby.taobao.org
# 请确保只有 ruby.taobao.org

## 使用extundelete恢复rm -fr数据

从[http://extundelete.sourceforge.net/](http://extundelete.sourceforge.net/)下载源码，安装efs2progs-devel并编译。

# mount /dev/sdb2 /mnt -o ro
# extundelete /dev/sdb2 --restore-files relative-path-to-files --after 20140404
OR
# extundelete /dev/sdb2 --restore-all

## 使用TestDisk/PhotoRec恢复分区/数据

从[http://www.cgsecurity.org/wiki/TestDisk](http://www.cgsecurity.org/wiki/TestDisk)下载TestDisk，解压后会发现源码以及静态编译文件，直接执行即可，其中TestDisk恢复分区，PhotoRec恢复图片文件。

## Wordpress 摘要字数搜索关键字excerpt\_length

## Eclipse multiuser

Add following code to your eclipse/configuration/config.ini

osgi.configuration.area=@user.home/Eclipse/configuration
osgi.sharedConfiguration.area=/opt/eclipse/configuration
osgi.configuration.cascaded=true

## get cer and key out of pfx

openssl pkcs12 -in domain.pfx -clcerts -nokeys -out domain.cer
openssl pkcs12 -in domain.pfx -nocerts -nodes  -out domain.key

## openssl/gpg encrypt/decrypt

$ echo Hi | openssl enc -aes-128-cbc -a -salt -pass pass:wtf
U2FsdGVkX18qAdhqop1SffsewHue6EOPNKv9dXc/0rI=
$ echo U2FsdGVkX18qAdhqop1SffsewHue6EOPNKv9dXc/0rI= | openssl enc -aes-128-cbc -a -d -salt -pass pass:wtf
Hi

OR

# generate a 2048-bit RSA key and store it in key.txt
openssl genrsa -out key.txt 2048
# encrypt "hello world" using the RSA key in key.txt
echo "hello world" | openssl rsautl -inkey key.txt -encrypt >output.bin
# decrypt the message and output to stdout
openssl rsautl -inkey key.txt -decrypt 
OR

gpg --encrypt -r recipient@example.com >tempfile
gpg --decrypt  

## ssh port forward

 

# Local Forwarding, with -g to share the local port with other hosts
(localpc)$ ssh -g -L 2222:localhost:22 root@lofyer.org
# ssh to localhost:2222 to connect lofyer.org
(localpc)$ ssh -lroot -p 2222 localhost

# Remote Forwarding
(localpc)$ ssh -R -L 2222:localhost:22 root@lofyer.org
# On host lofyer.org, ssh to localhost:2222 to connect localpc.
(lofyer.org)$ ssh localhost -p 2222

# Mixed, localpc:22->lofyer.org:2222
# On lofyer.org we make a tunnel(2222) to localpc:22
(localpc)$ ssh -R 2222:localhost:22 lofyer.org
# Make a local tunnel(3333) listening on all interface to localpc:2222
(lofyer.org)$ ssh -g -L 3333:localhost:2222 localhost
# ssh to lofyer.org:3333 to connect localpc:22
(localpc) ssh lofyer.org -p 3333

# Dynamic Forwarding, with -CN to compress data and close terminal input.
# To start a SOCKS v4/v5 server on localhost:7777
# Then you can use this as a proxy for your browser.
(localpc)$ ssh -CND 7777 root@lofyer.org

 

## Export private key of IIS that cannot be exported

1. git clone https://github.com/iSECPartners/jailbreak-Windows.git
2. Run mcc.msc, add Cert, save this MCC as a file(i.e. aaa).
3. Run jailbreak64.exe mcc.msc aaa
4. Then you can export the private key from the cert.

## iptables route to local pc

 

iptables -t nat -A PREROUTING -p tcp  --dport 5060:5081 -j DNAT --to-destination 192.168.122.222:5060:5081

 

**Change vm root password**
 

virt-sysprep --root-password password:123456 -a os.img

 

## Ubuntu ap-hotspot PPA

 

sudo add-apt-repository ppa:nilarimogard/webupd8 
sudo apt-get update
sudo apt-get install ap-hotspot

 

## 恢复IO ERROR文件

dd if=ioerror.file of=file bs=1M conv=noerror

## QEMU LSI SCSI

 

qemu-kvm -m 1024 -drive file=centos58-RAW.img,index=0,if=scsi,format=raw -boot c -net nic -net user -nographic -vnc :1 -option-rom 8xx\_64.rom,bootindex=1

 
[lsi\_bios 下载](http://blog.lofyer.org/quick-notes/lsi_bios/)

## yum remove nodeps

rpm -e --nodpes PKG
yum install PKG

## File too large

ulimit -n 102400
ulimit -f 102400

## HTTP REFRESH

 

<meta HTTP-EQUIV="REFRESH" content="0; url=/SOGo/">

 

## AUTORECONF

autoreconf -i

## RPi x11vnc auto start and prevent screen from sleeping

 

pi@raspberrypi:~ $ cat .config/lxsession/LXDE-pi/autostart 
@lxpanel --profile LXDE-pi
@pcmanfm --desktop --profile LXDE-pi
@xscreensaver -no-splash
x11vnc -auth /home/pi/.Xauthority -display :0
@xset s noblank
@xset s off
@xset -dpms

 

## Crawl date yesterday

select id,title,content,url,date\_format(str\_to\_date(date, '%d-%M-%Y'), '%Y%m%d') as date\_new from posts where date\_format(str\_to\_date(date, '%d-%M-%Y'), '%Y%m%d')=curdate()+0-1;

 

## Simple PHP server

php -S 127.0.0.1:80 -t .

 

## MacOS reinstall

 

# Format U disk at first with 区分大小写，日志式
# /Volume/Install\\ OSX/Install\\ OSX.app/Contents/Resources/createinstallmedia --volume /Volume/install --applicationpath /Volume/Install\\ OSX/OSX.app

 

## MySQL select items yesterday

 

select id,title,content,url,date\_format(str\_to\_date(date, '%d-%M-%Y'), '%Y%m%d') as date\_new from posts where date\_format(str\_to\_date(date, '%d-%M-%Y'), '%Y%m%d')=curdate()+0-1;

 

## MACOS all source

 

# macOS 允许所有来源
 sudo spctl --master-disable

 

## wp-github all repos limit

 

120    public function get\_repositories() {
121    $contents = $this->get\_response('users/' . $this->username . '/repos?per\_page=100');

 

## 修改iconic右侧widget超链接颜色

 

.widget-area .widget a {
	/\*color: #757575;\*/
	text-decoration:none;
	font-size: 98%;
}

 

## CPU IPC usage 4-wide CPU

perf stat -a -- sleep 10

Linux, when adding a new disc
 

First find your host bus number

grep mpt /sys/class/scsi\_host/host?/proc\_name

Which should return a line like

/sys/class/scsi\_host/host0/proc\_name:mptspi

where host0 is the relevant field.

use this to rescan the bus with the following command

echo "- - -" > /sys/class/scsi\_host/host0/scan

In the above command the the hyphens represent controller,channel,lun, so – – – indicates all controllers, all channels and all luns should be scanned.

 

## ens/enp rename to eth0

Edit /etc/default/grub
At the end of GRUB\_CMDLINE\_LINUX line append "net.ifnames=0 biosdevname=0"
Save the file
Type "grub2-mkconfig -o /boot/grub2/grub.cfg"
Type "reboot"

 

## Change mysql database or table encoding

SELECT neutron\_ml2;
ALTER DATABASE neutron\_ml2 CHARACTER SET utf8mb4 COLLATE utf8mb4\_unicode\_ci;
ALTER TABLE standardattributes CONVERT TO CHARACTER SET utf8mb4 COLLATE utf8mb4\_unicode\_ci;

Or if you're still on MySQL 5.5.2 or older which didn't support 4-byte UTF-8, use utf8 instead of utf8mb4:

ALTER DATABASE databasename CHARACTER SET utf8 COLLATE utf8\_unicode\_ci;
ALTER TABLE tablename CONVERT TO CHARACTER SET utf8 COLLATE utf8\_unicode\_ci;

## Google Pixel 2 network "X" symbol

./adb shell "settings put global captive\_portal\_https\_url https://captive.v2ex.co/generate\_204";
---
title: "transfer IPv6 to IPv4 via socat"
date: 2018-10-09
categories: 
  - "linux-admin"
---

Suppose that you have an ipv6-only server A with ip \[ipv6-A\], and ipv4-ipv6 server B with ip (ipv4-B, \[ipv6-B\]).

If you wanna visit http://\[ipv6-A\]:80, but your ISV does not provide some ipv6 addresses. Now then, you'll make B be your "ipv6-ipv4 relay server" with following guide.

If TCP:

root@B:~# socat tcp4-listen:2222,fork tcp6:\[2001:19f0:7001:5a34:5400:01ff:feb5:5bcd\]:80

If UDP:

root@B:~# socat udp4-listen:2222,fork udp6:\[2001:19f0:7001:5a34:5400:01ff:feb5:5bcd\]:80

Now you can visit http://ipv4-B:2222 to access http://\[ipv6-A\]:80
