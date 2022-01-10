---
title: "OpenLDAP step by step how-to"
date: "2014-04-14"
categories: 
  - "linux-admin"
---

I need an authentication system with compatibility and many extended features(like bio-device). So, I've got AD, IPA and OpenLDAP to choose from. AD comes from MS and it is too "heavy" for the not-very-large system. IPA and OpenLDAP are almost same, but I prefer latter, since it's compatible with oVirt(This why I choose CentOS rather than debian).

# The simplest OpenLDAP server

A basic LDAP without any security or additional features.

# OpenLDAP with SASL

Add SASL to our LDAP.

# OpenLDAP with SAMBA

To add Windows PC to our domain.

# OpenLDAP with Kerberos

This is what we want finally. ============================================================

# 1\. The simplest OpenLDAP server

I've got 2 ways to setup an openldap server: **389-ds script** and **manually configure**.

## 1.1 Using 389-ds script

[Here's the original article.](http://www.unixmen.com/setup-directory-serverldap-in-centos-6-4-rhel-6-4/ "Setup 389-ds")

### Preparation

Before setup, this configuration should be modified. Add following:

192.168.1.80    ldap.lofyer.org

Add following:

net.ipv4.tcp\_keepalive\_time = 30
net.ipv4.ip\_local\_port\_range = 1024 65000
fs.file-max = 64000

Add following:

\*    soft    nofile    8192
\*    hard    nofile    8192

Add following:

session    required    /lib/security/pam\_limits.so

Then **reboot** the machine to make above configurations work.

### Setup 389-ds

\# useradd ldapadmin
# passwd ldapadmin
# yum install -y 389-ds openldap-clients
# setup-ds-admin.pl

Then you'll see some questions like this(sorry for the high-lighting...):

\==============================================================================
This program will set up the 389 Directory and Administration Servers.

It is recommended that you have "root" privilege to set up the software.
Tips for using this program:
  - Press "Enter" to choose the default and go to the next screen
  - Type "Control-B" then "Enter" to go back to the previous screen
  - Type "Control-C" to cancel the setup program

Would you like to continue with set up? \[yes\]:   ## Press Enter ## 

==============================================================================
Your system has been scanned for potential problems, missing patches,
etc.  The following output is a report of the items found that need to
be addressed before running this software in a production
environment.

389 Directory Server system tuning analysis version 23-FEBRUARY-2012.

NOTICE : System is i686-unknown-linux2.6.32-431.el6.i686 (1 processor).

WARNING: 622MB of physical memory is available on the system. 1024MB is recommended for best performance on large production system.

WARNING  : The warning messages above should be reviewed before proceeding.

Would you like to continue? \[no\]: yes  ## Type Yes and Press Enter ##

==============================================================================
Choose a setup type:
   1. Express
       Allows you to quickly set up the servers using the most
       common options and pre-defined defaults. Useful for quick
       evaluation of the products.
   2. Typical
       Allows you to specify common defaults and options.
   3. Custom
       Allows you to specify more advanced options. This is 
       recommended for experienced server administrators only.
To accept the default shown in brackets, press the Enter key.

Choose a setup type \[2\]:  ## Press Enter ##

==============================================================================
Enter the fully qualified domain name of the computer
on which you're setting up server software. Using the form
. Example: eros.example.com.

To accept the default shown in brackets, press the Enter key.

Warning: This step may take a few minutes if your DNS servers
can not be reached or if DNS is not configured correctly.  If
you would rather not wait, hit Ctrl-C and run this program again
with the following command line option to specify the hostname:

    General.FullMachineName=your.hostname.domain.name

Computer name \[ldap.lofyer.org\]:     ## Press Enter ##

==============================================================================
he servers must run as a specific user in a specific group.
It is strongly recommended that this user should have no privileges
on the computer (i.e. a non-root user).  The setup procedure
will give this user/group some permissions in specific paths/files
to perform server-specific operations.

If you have not yet created a user and group for the servers,
create this user and group using your native operating
system utilities.

System User \[nobody\]: ldapadmin  ## Enter LDAP user name created above #
System Group \[nobody\]: ldapadmin

==============================================================================
Server information is stored in the configuration directory server.
This information is used by the console and administration server to
configure and manage your servers.  If you have already set up a
configuration directory server, you should register any servers you
set up or create with the configuration server.  To do so, the
following information about the configuration server is required: the
fully qualified host name of the form
.(e.g. hostname.example.com), the port number
(default 389), the suffix, the DN and password of a user having
permission to write the configuration information, usually the
configuration directory administrator, and if you are using security
(TLS/SSL).  If you are using TLS/SSL, specify the TLS/SSL (LDAPS) port
number (default 636) instead of the regular LDAP port number, and
provide the CA certificate (in PEM/ASCII format).

If you do not yet have a configuration directory server, enter 'No' to
be prompted to set up one.
Do you want to register this software with an existing
configuration directory server? \[no\]:   ## Press Enter ##

==============================================================================
Please enter the administrator ID for the configuration directory
server.  This is the ID typically used to log in to the console.  You
will also be prompted for the password.
Configuration directory server
administrator ID \[admin\]:   ## Press Enter ##
Password:    ## create password ##
Password (confirm):    ## re-type password ##

==============================================================================
The information stored in the configuration directory server can be
separated into different Administration Domains.  If you are managing
multiple software releases at the same time, or managing information
about multiple domains, you may use the Administration Domain to keep
them separate.

If you are not using administrative domains, press Enter to select the
default.  Otherwise, enter some descriptive, unique name for the
administration domain, such as the name of the organization
responsible for managing the domain.

Administration Domain \[lofyer.org\]:   ## Press Enter ##

==============================================================================
The standard directory server network port number is 389. However, if
you are not logged as the superuser, or port 389 is in use, the
default value will be a random unused port number greater than 1024.
If you want to use port 389, make sure that you are logged in as the
superuser, that port 389 is not in use.
Directory server network port \[389\]:   ## Press Enter ##

==============================================================================
Each instance of a directory server requires a unique identifier.
This identifier is used to name the various
instance specific files and directories in the file system,
as well as for other uses as a server instance identifier.

Directory server identifier \[server\]:  ## Press Enter ##

==============================================================================
The suffix is the root of your directory tree.  The suffix must be a valid DN.
It is recommended that you use the dc=domaincomponent suffix convention.
For example, if your domain is example.com,
you should use dc=example,dc=com for your suffix.
Setup will create this initial suffix for you,
but you may have more than one suffix.
Use the directory server utilities to create additional suffixes.

Suffix \[dc=lofyer, dc=org\]:   ## Press Enter ##

=============================================================================

Certain directory server operations require an administrative user.
This user is referred to as the Directory Manager and typically has a
bind Distinguished Name (DN) of cn=Directory Manager.
You will also be prompted for the password for this user.  The password must
be at least 8 characters long, and contain no spaces.
Press Control-B or type the word "back", then Enter to back up and start over.
Directory Manager DN \[cn=Directory Manager\]:   ## Press Enter ##
Password:               ## Enter the password ##
Password (confirm): 

==============================================================================
The Administration Server is separate from any of your web or application
servers since it listens to a different port and access to it is
restricted.

Pick a port number between 1024 and 65535 to run your Administration
Server on. You should NOT use a port number which you plan to
run a web or application server on, rather, select a number which you
will remember and which will not be used for anything else.
Administration port \[9830\]:   ## Press Enter ##

==============================================================================
The interactive phase is complete.  The script will now set up your
servers.  Enter No or go Back if you want to change something.

Are you ready to set up your servers? \[yes\]:  ## Press Enter ##
Creating directory server . . .
Your new DS instance 'server' was successfully created.
Creating the configuration directory server . . .
Beginning Admin Server creation . . .
Creating Admin Server files and directories . . .
Updating adm.conf . . .
Updating admpw . . .
Registering admin server with the configuration directory server . . .
Updating adm.conf with information from configuration directory server . . .
Updating the configuration for the httpd engine . . .
Starting admin server . . .
output: Starting dirsrv-admin: 
output:                                                    \[  OK  \]
The admin server was successfully started.
Admin server was successfully created, configured, and started.
Exiting . . .
Log file is '/tmp/setupo1AlDy.log' 

Then make these two services start on startup.

\# chkconfig dirsrv on
# chkconfig dirsrv-admin on

With 389-ds scripts, you could use 389-console, please refer to the link above.

## 1.2 Manually configure

[Here's the original article.](https://n40lab.wordpress.com/2013/11/22/creating-a-simple-ldap-directory-with-openldap-2-4-in-centos-6-4/ "simple ldap")

### Install the packages

\# yum install openldap{,-clients,-servers}

### Change the configuration

_/etc/openldap/slapd.d/cn\\=config.ldif_ Delete **olcAllows: bind\_v2** if you want only v3. Modify **olcIdleTimeout** from 0 to 30 if you want close the idle connection for more than 30 seconds.

Before next step, run this command to generate a SHA encrypted password.

\# slappasswd
New password:
Re-enter new password:
{SSHA}aW7TYJ3faz13RKsnr3uiCsbgi55RKhW9

Then copy the output to your clipboard.

_/etc/openldap/slapd.d/cn\\=config/olcDatabase\\=\\{2\\}bdb.ldif_ Modify **olcSuffix**, **RootDN**, **olcRootPW** to this:

...
olcSuffix: dc=lofyer, dc=org
olcRootPW: {SSHA}aW7TYJ3faz13RKsnr3uiCsbgi55RKhW9
RootDN: cn=admin, dc=lofyer, dc=org
...

### Start service

\# service slapd start
# chkconfig slpad on

### Add rootdn and groups

dn: dc=lofyer,dc=org
objectclass: dcObject
objectclass: organization
o: Lofyer Org
dc: lofyer

dn: ou=People,dc=lofyer,dc=org
objectClass: organizationalUnit
objectClass: top
ou: People

dn: ou=Groups,dc=lofyer,dc=org
objectClass: organizationalUnit
objectClass: top

ou: Groups
dn: cn=admin,dc=lofyer,dc=org
objectclass: organizationalRole
cn: admin

Import the ldif:

\# ldapadd -x -D "cn=admin,dc=lofyer,dc=org" -W -f /etc/openldap/schema/lofyer.org.ldif
# ldapsearch -x -b 'dc=lofyer,dc=org' '(objectclass=\*)'

### Create a user

Add following content to _user.ldif_

dn: uid=demo,ou=People,dc=lofyer,dc=org
objectclass: top
objectclass: person
objectclass: inetOrgPerson
objectclass: organizationalPerson
uid: demo
cn: demo
sn: demo
givenName: demo

Provide a password:

\# ldapadd -x -W -D "cn=admin,dc=lofyer,dc=org" -f user.ldif
New password:
Re-enter new password:
Enter LDAP Password:

### Add or delete a member from group(myteam)

**Add:** dn: cn=myteam,ou=Groups,dc=lofyer,dc=org changetype: modify add: member member: uid=user1,ou=People,dc=lofyer,dc=org

\# ldapmodify -x -D "cn=admin,dc=lofyer,dc=org" -W -f add.ldif

**Delete:**

dn: cn=myteam,ou=Groups,dc=lofyer,dc=org
changetype: modify
delete: member
member: uid=user1,ou=People,dc=lofyer,dc=org

\# ldapmodify -x -D "cn=admin,dc=lofyer,dc=org" -W -f delete.ldif

## Use TSL

[Here's the original article.](http://www.overclockers.com/forums/showthread.php?t=726947 "Setup ldap")

## (NOT NECESSARY)Generate CA

Follow this script.

#!/bin/bash
#Change to the directory and clear out the old certs
cd /etc/openldap/certs
rm -rf \*
#This echo statement is actually putting the word “password” (without the quotes) in a temporary password file to help
#automate the process. This will be the password for your certificate. Change this as appropriate
echo "mypassword" > /etc/openldap/certs/password
export PATH=/usr/bin/:$PATH
echo falkdjfdajkasdndwndoqndwapqmhfaksj >> noise.txt

#Associate the password with the certificates which will be generated in the current directory
certutil -N -d . -f /etc/openldap/certs/password
certutil -G -d . -z noise.txt -f /etc/openldap/certs/password

#Generate a CA certificate for the 389 server
certutil -S -n "CA certificate" -s "cn=CACert" -x -t "CT,," -m 1000 -v 120 -d . -z /etc/openldap/certs/noise.txt -f /etc/openldap/certs/password

#anwsers are Y, , Y
#This builds the server cert
certutil -S -n "OpenLDAP Server" -s "cn=ldap.lofyer.org" -c "CA certificate" -t "u,u,u" -m 1001 -v 120 -d . -z /etc/openldap/certs/noise.txt -f /etc/openldap/certs/password

#This exports the cacert in case you need it
pk12util -d . -o cacert.p12 -n "CA certificate"

#This exports the server-cert which you will need on the windows AD
pk12util -d . -o servercert.p12 -n "OpenLDAP Server"

#This exports the CA cert for ldap clients
certutil -L -d . -n "CA certificate" -a > /etc/openldap/certs/cacert.pem

#Make the files in here readable
chmod 644 \*

#Set the system to use LDAPS
sed -i 's/SLAPD\_LDAPS=no/SLAPD\_LDAPS=yes/g' /etc/sysconfig/ldap

#Add a firewall exception in case the user has not configured their firewall properly
#iptables -I INPUT -m state --state NEW -p tcp --dport 636 -j ACCEPT
#/etc/init.d/iptables save

#Restart slapd to make the changes take effect
#/etc/init.d/slapd restart

I think you should notice that the private key password is "mypassword". Then you will get three files: **cacert.p12**, **cacert.pem**, **servercert.p12**. And, that's all.

# 2\. Add SASL to OpenLDAP

OKay, we'll add SASL to our ldap connections.

## Install cyrus-sasl package.

\# yum install cyrus-sasl-gssapi
# yum install cyrus-sasl-ldap
