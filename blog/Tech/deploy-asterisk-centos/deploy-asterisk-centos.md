---
title: "Deploy Asterisk on CentOS"
date: "2014-02-10"
categories: 
  - "cloud-infra"
---

Get the latest packages. **up to 2014-02-10**

\# rpm -Uvh http://packages.asterisk.org/centos/6/current/x86\_64/RPMS/asterisknow-version-3.0.1-2\_centos6.noarch.rpm
# yum install asterisk asterisk-configs --enablerepo=asterisk-12
# yum install dahdi-linux dahdi-tools libpri
# chkconfig dahdi on
# chkconfig asterisk on
# service dahdi start
# service asterisk start

You can use freepbx on http://localhost .

\# yum install freepbx
