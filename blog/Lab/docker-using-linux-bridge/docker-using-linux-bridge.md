---
title: "docker using linux bridge"
date: "2018-11-08"
categories: 
  - "cloud-infra"
---

A. Change docker bridge dockerd -H unix:// -b br\_eth0 --fixed-cidr=172.20.230.1/24 --default-gateway 172.20.0.1

B. Create manually, but flexibly Recreate veth pair if your docker instance was restarted or exited.

\[root@172-20-17-247 ~\]# docker run --network none -it tianon/network-toolbox /bin/bash \[root@172-20-17-247 ~\]# docker inspect --format '{{.State.Pid}}' 3 74406 \[root@172-20-17-247 ~\]# ip link add centos3-cont type veth peer name centos3-host \[root@172-20-17-247 ~\]# brctl addif br\_eth0 centos3-host \[root@172-20-17-247 ~\]# ifconfig centos3-host up \[root@172-20-17-247 ~\]# ip link set netns 74406 dev centos3-cont \[root@172-20-17-247 ~\]# nsenter -t 74406 -n ip link set centos3-cont up \[root@172-20-17-247 ~\]# nsenter -t 74406 -n ip addr add 172.20.77.141/16 dev centos3-cont \[root@172-20-17-247 ~\]# nsenter -t 74406 -n ip route add default via 172.20.0.1

\[root@172-20-17-247 ~\]# docker exec -it 3 bash root@39eb1da6e842:/# ping google.com PING google.com (172.217.25.206): 56 data bytes 64 bytes from 172.217.25.206: icmp\_seq=0 ttl=55 time=87.434 ms ^C--- google.com ping statistics --- 1 packets transmitted, 1 packets received, 0% packet loss round-trip min/avg/max/stddev = 87.434/87.434/87.434/0.000 ms

C. With pipework script https://github.com/jpetazzo/pipework \[root@172-20-17-247 ~\]# pipework br\_eth0 -i eth2 CONTAINERID 172.20.100.22/16 \[root@172-20-17-247 ~\]# pipework route CONTAINERID replace default via 172.20.0.1

Ref: \[1\] [https://clcnetwork.wordpress.com/2017/05/31/docker-container-and-vm-networking/](https://clcnetwork.wordpress.com/2017/05/31/docker-container-and-vm-networking/)
