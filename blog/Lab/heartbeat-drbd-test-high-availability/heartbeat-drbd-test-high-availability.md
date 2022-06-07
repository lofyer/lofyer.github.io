---
title: "Heartbeat and drbd test high availability"
date: "2014-01-16"
categories: 
  - "linux-admin"
---

Hosts: 192.168.1.101 ha1.lofyer.org, 2 hard drive disks, two ethernet ports 192.168.1.103 ha2.lofyer.org, almost same as ha1

Server host, this is the IP of heartbeat service: 192.168.1.100

## Install

The repos you need in centos

\[epel\]
name=Extra Packages for Enterprise Linux 6 - $basearch
#baseurl=http://download.fedoraproject.org/pub/epel/6/$basearch
mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=epel-6&arch=$basearch
failovermethod=priority
enabled=1
gpgcheck=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6

\[elrepo\]
name=ELRepo.org Community Enterprise Linux Repository - el6
baseurl=http://elrepo.org/linux/elrepo/el6/$basearch/
mirrorlist=http://elrepo.org/mirrors-elrepo.el6
enabled=1
gpgcheck=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-elrepo.org
protect=0

\# yum install drbd84 kmod-drbd84 heartbeat mysql-server

## Setup

### 1\. Drbd configuration both hosts

**Add following content to file: /etc/hosts**

192.168.1.101 ha1.lofyer.org
192.168.1.103 ha2.lofyer.org

**Disable selinux and iptables**

\# sed -i 's/enforcing/permissive/' /etc/selinux/config
# setenforce 0
# chkconfig iptables off
# service iptables stop

**Prepare the disk partion**

\# fdisk /dev/sdb << EOF
n
p
1

w
EOF

**Configuration for mysql** # mkdir db # sed -i 's/datadir=\\/var\\/lib\\/mysql/datadir=\\/db/' /etc/my.cnf **Configuration for drbd** file: /etc/drbd.conf

 global {
   minor-count 64;
   usage-count yes;
 }
 common {
   syncer { rate 1000M; }
 }
 resource ha {
   protocol C;
   handlers {
     pri-on-incon-degr "/usr/lib/drbd/notify-pri-on-incon-degr.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger ; reboot -f";
     pri-lost-after-sb "/usr/lib/drbd/notify-pri-lost-after-sb.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger ; reboot -f";
     local-io-error "/usr/lib/drbd/notify-local-io-error.sh; /usr/lib/drbd/notify-emergency-shutdown.sh; echo o > /proc/sysrq-trigger ; halt -f";
     fence-peer "/usr/lib/heartbeat/drbd-peer-outdater -t 5";
     pri-lost "/usr/lib/drbd/notify-pri-lost.sh; /usr/lib/drbd/notify-emergency-reboot.sh; echo b > /proc/sysrq-trigger ; reboot -f";
     split-brain "/usr/lib/drbd/notify-split-brain.sh root";
     out-of-sync "/usr/lib/drbd/notify-out-of-sync.sh root";
   }
   startup {
     wfc-timeout 60;
     degr-wfc-timeout 120;
     outdated-wfc-timeout 2;
   }
   disk {
     on-io-error detach;
     fencing resource-only;
   }
   syncer {
     rate 1000M;
   }
   on ha1.lofyer.org {
     device /dev/drbd0;
     disk /dev/sdb1;
     address 192.168.1.101:7788;
     meta-disk internal;
   }
   on ha2.lofyer.org {
     device /dev/drbd0;
     disk /dev/sdb1;
     address 192.168.1.103:7788;
     meta-disk internal;
   }
 }

**Chmod for drbd**

 # chgrp haclient /sbin/drbdsetup
 # chmod o-x /sbin/drbdsetup
 # chmod u+s /sbin/drbdsetup
 # chgrp haclient /sbin/drbdmeta
 # chmod o-x /sbin/drbdmeta
 # chmod u+s /sbin/drbdmeta

**Resource for drbd**

 # modprobe drbd
 # dd if=/dev/zero of=/dev/hdb1 bs=1M count=100
 # drbdadm create-md ha
 # service drbd start
 # chkconfig drbd on 

**Watch drbd status**

\# watch -n 1 service drbd status

You can see that both hosts are Secondary/Secondary.

### 2\. Drbd configuration on one of hosts, like ha1

**Make ha1 Primary**

 # drbdadm -- --overwrite-data-of-peer primary ha
 # service drbd status

Then you should see Primary and wait for both hosts are UpToDate. **Initialization for Mysql** Make a

\# mkfs.ext4 /dev/drbd0
# mount /dev/drbd0 /db
# service mysqld start

Now you should see what you have got in /db, then umount /db, stop mysql-server and make ha1 Secondary.

\# service mysqld stop
# umount /dev/drbd0
# drbdadm secondary ha

### 3\. Heartbeat configuration on both hosts

**cluster authkey**

\# (echo -ne "auth 1\\n1 sha1 "; dd if=/dev/urandom bs=512 count=1 | openssl md5) > /etc/ha.d/authkeys
# cat /etc/ha.d/authkeys
 auth 1
 1 sha1 71461fc5e160d7846c2f4b524f952128
# chmod 600 /etc/ha.d/authkeys
# scp /etc/ha.d/authkeys node2:/etc/ha.d/

YOU SHOULD MODIFY THE IP IN THE FILE. file: /etc/ha.d/ha.cf

debugfile /var/log/ha-debug
logfile /var/log/ha-log
logfacility local0
autojoin none
ucast eth0 192.168.1.101
ucast eth1 192.168.1.102
ping 192.168.1.100 
respawn hacluster /usr/lib64/heartbeat/ipfail
respawn hacluster /usr/lib64/heartbeat/dopd
apiauth dopd gid=haclient uid=hacluster
udpport 694
warntime 5
deadtime 15
initdead 60
keepalive 2
node ha1.lofyer.org
node ha2.lofyer.org
auto\_failback off    

The service will be serve on IP 192.168.1.100. file: /etc/ha.d/haresources

mysql.lofyer.org 192.168.1.100 drbddisk::ha Filesystem::/dev/drbd0::/db::ext4 mysql

If you just wanna a virtual ip, use this

hosta.vf.com IPaddr::192.168.0.100/24/eth0:0

**Add mysql entry to heartbeat** file: /etc/ha.d/resource.d/mysql

#!/bin/bash
. /etc/ha.d/shellfuncs
case "$1" in
start)
  res=\`/etc/init.d/mysqld start\`
  ret=$?
  ha\_log $res
  exit $ret
  ;;
stop)
  res=\`/etc/init.d/mysqld stop\`
  ret=$?
  ha\_log $res
  exit $ret
  ;;
status)
  if \[\[ \`ps -ef | grep '\[m\]ysqld'\` > 1 \]\]; then
     echo "running"
  else
     echo "stopped"
  fi
  ;;
\*)
  echo "Usage: mysqld {start|stop|status}"
  exit 1
  ;;
esac
exit 0

Add excute permission to it.

\# chmod 755 /etc/ha.d/resource.d/mysql 

**Add heartbeat service to system**

\# chkconfig --add heartbeat
# chkconfig heartbeat on
# service heartbeat start

_You may need modify order of drbd and heartbeat service. In /etc/init.d/, the number 85 and 15 represent the order number which the script is to be run at start up time and shutdown time. # chkconfig: - 85 15_

## Test HA
