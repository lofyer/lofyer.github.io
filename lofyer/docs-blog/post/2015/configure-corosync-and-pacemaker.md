---
title: "Configure corosync and pacemaker"
date: "2015-02-28"
categories: 
  - "linux-admin"
---

Env: node1 eth0 192.168.0.201 node2 eth0 192.168.0.202

## 1\. Install essential packages

Add following content to _/etc/yum.repos.d/ha.repo_, since you will need crmsh later:

\[haclustering\]
name=HA Clustering
baseurl=http://download.opensuse.org/repositories/network:/ha-clustering:/Stable/CentOS\_CentOS-6/
enabled=1
gpgcheck=0

Install packages:

\# yum install pacemaker corosync crmsh -y

## 2\. Configure corosync

Using configuration files below if you need broadcast:

service {
    # Load the Pacemaker Cluster Resource Manager
    ver: 0
    name: pacemaker
    use\_mgmtd: no
    use\_logd: no
    }

totem {
        version: 2
        secauth: off
        interface {
                member {
                        memberaddr: 192.168.0.201
                }
                member {
                        memberaddr: 192.168.0.202
                }
                ringnumber: 0
                bindnetaddr: 192.168.0.0
                mcastport: 5405
                ttl: 1
        }
        transport: udpu
}

logging {
        fileline: off
        to\_logfile: yes
        to\_syslog: yes
        logfile: /var/log/cluster/corosync.log
        debug: off
        timestamp: on
        logger\_subsys {
                subsys: AMF
                debug: off
        }
}

Here's a sample using multicast:

service {
    # Load the Pacemaker Cluster Resource Manager
    ver: 0
    name: pacemaker
    use\_mgmtd: no
    use\_logd: no
    }

totem {
        version: 2

        # secauth: Enable mutual node authentication. If you choose to
        # enable this ("on"), then do remember to create a shared
        # secret with "corosync-keygen".
        secauth: off

        threads: 0

        # interface: define at least one interface to communicate
        # over. If you define more than one interface stanza, you must
        # also set rrp\_mode.
        interface {
                ringnumber: 0
                bindnetaddr: 192.168.1.0
                mcastaddr: 239.255.1.1
                mcastport: 5405
                ttl: 1
        }
}

logging {
        fileline: off
        to\_stderr: no
        to\_logfile: yes
        logfile: /var/log/cluster/corosync.log
        to\_syslog: yes
        debug: off
        timestamp: on
        logger\_subsys {
                subsys: AMF
                debug: off
        }
}

Note that if the **ver** in the **service** section of pacemaker is **0**, pacemaker will be loaded automatically, or else you will start the pacemaker service manually.

Copy this configuration file to the other host and start the service:

\# scp /etc/corosync/corosync.conf root@192.168.0.202:/etc/corosync/corosync.conf

On 192.168.0.201:
# chkconfig corosync on
# service corosync start

On 192.168.0.202:
# chkconfig corosync on
# service corosync start

## 3\. Configure corosync using crmsh

Add virtual IP to your cluster:

\# crm configure
crm(live)configure# primitive vip1 ocf:heartbeat:IPaddr2 params ip=192.168.0.209 cidr\_netmask=24 op monitor interval=10s
crm(live)configure# property stonith-enabled=false # To prevent split-brain
crm(live)configure# property no-quorum-policy=stop # To prevent split-brain
crm(live)configure# commit

Test:

crm(live)configure# migrate vip1
crm(live)configure# unmigrate vip1

You will see 192.168.0.209 migrating between these two nodes.
