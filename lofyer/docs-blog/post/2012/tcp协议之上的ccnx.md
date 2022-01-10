---
title: "tcp协议之上的CCNx"
date: "2012-10-26"
categories: 
  - "linux-admin"
---

刚在lwn上看到了个有意思的东西，ccnx，主要作用于媒体中心传播数据（视频）。 在06年于google的演讲中，Jacobson说设计了一种新的媒体传输协议，举了奥运会期间NBC传播同一赛事，大家都卡卡的例子。 类似p2p或者bt吧，几千用户请求同一视频，对于已经从server中出去的数据，就让用户互传，当然，数据包里带有签名，防止新用户的重复请求。 [目前最新的是0.62版本](http://www.ccnx.org/software-download-information-request/download-releases/ "CCNx")，用于linux发行版下载效果应该不错，目测缺点是路由负载可能会高那么点。
