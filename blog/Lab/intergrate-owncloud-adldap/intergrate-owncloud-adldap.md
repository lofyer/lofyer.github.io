---
title: "Intergrate owncloud with AD(LDAP)"
date: "2014-04-24"
categories: 
  - "linux-admin"
---

Windows 2008R2 server with AD role built. User group: **owncloudgrp** User in **owncloudgrp**: aaa, beta Users must have **logon name, first name, last name** set.

## Configure the owncloud:

Server:

[![oc1](/blog/images/oc1.png)](http://blog.lofyer.org/intergrate-owncloud-adldap/oc1/)

User Filter:

[![oc2](/blog/images/oc2.png)](http://blog.lofyer.org/intergrate-owncloud-adldap/oc2/)

Login Filter:

[![oc3](/blog/images/oc3.png)](http://blog.lofyer.org/intergrate-owncloud-adldap/oc3/)

Group Filter: Every time you change these two sections, **wait for a few seconds** until more than zero users discovered.

[![oc4](/blog/images/oc4.png)](http://blog.lofyer.org/intergrate-owncloud-adldap/oc4/)

Advanced - Connection Settings:

[![oc5](/blog/images/oc5.png)](http://blog.lofyer.org/intergrate-owncloud-adldap/oc5/)

Advanced - Directory Settings:

[![oc6](/blog/images/oc6.png)](http://blog.lofyer.org/intergrate-owncloud-adldap/oc6/)

Expert: Add internal username: sAMAccountName

[![oc7](/blog/images/oc7.png)](http://blog.lofyer.org/intergrate-owncloud-adldap/oc7/)
