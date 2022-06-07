---
title: "use Foreman/Nagios/Icinga to make life easy..."
date: "2013-09-24"
categories: 
  - "linux-admin"
---

# Install nagios in Gentoo/CentOS

## Gentoo

\# emerge nagios

### Option: recompile apache for php support

add use flag "apache2" to /etc/portage/make.conf

\# emerge --ask --changed-use --deep @world

### Copy following content to /etc/apache2/vhosts.d/

ScriptAlias /nagios/cgi-bin "/usr/lib64/nagios/cgi-bin"
<Directory "/usr/lib64/nagios/cgi-bin">
#  SSLRequireSSL
    Options ExecCGI
    AllowOverride None
    Order allow,deny
    Allow from all
#  Order deny,allow
#  Deny from all
#  Allow from 127.0.0.1
    AuthName "Nagios Access"
    AuthType Basic
    AuthUserFile /etc/nagios/auth.users
    Require valid-user

Alias /nagios "/usr/share/nagios/htdocs"
<Directory "/usr/share/nagios/htdocs">
#  SSLRequireSSL
    Options None
    AllowOverride None
    Order allow,deny
    Allow from all
#  Order deny,allow
#  Deny from all
#  Allow from 127.0.0.1
    AuthName "Nagios Access"
    AuthType Basic
    AuthUserFile /etc/nagios/auth.users
    Require nagiosadmin

### Create password for nagiosadmin

\# htpasswd2 -c /etc/nagios/auth.users nagiosadmin

### Add NAGIOS to apache config

/etc/conf.d/apache

APACHE2\_OPTS="... -D NAGIOS -D PHP5"

Add user nagios to apache group

\# usermod -a -G nagios apache

Start service

\# rc-service nagios restart
# rc-service apache2 restart

## CentOS

\# yum install "nagios\*"
# htpasswd -c /etc/nagios/passwd admin
# chkconfig nagios on
# chkconfig httpd on
# service nagios start
# service httpd start

## Add routers/hosts, add service, add hooks

## Intergrate with oVirt

# using Foreman

## Install

## USE

## Intergrate with oVirt

TBD
