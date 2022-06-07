---
title: "use MAXS to control your device via ejabberd(plus ssh, jingle voice talk as a bonus)"
date: "2014-02-26"
categories: 
  - "cloud-infra"
---

Let's see what we have got here: A xmpp server based on ejabberd on my host: lofyer.org. Windows client: [Jitsi(Recommended)](https://download.jitsi.org/jitsi/nightly/), [Pidgin](http://www.pidgin.im/). (Optional)A Android client: [Xabber.](http://www.xabber.org/) [MAXS](http://f-droid.org/wiki/page/org.projectmaxs.main) on my Nexus 5 Android phone.

## 1\. Prepare the server(Debian 7)

\# apt-get install ejabberd
# cd /etc/ejabberd/; wget http://people.collabora.com/~robot101/olpc-ejabberd/ejabberd.cfg

Change **hosts** and **admin** section to your **FQDN**. Here's a example:

{hosts, \["lofyer.org"\]}.
{acl, admin, {user, "mypassword", "lofyer.org"}}.

Then you should restart ejabberd, and maybe a reboot is essential.

\# /etc/init.d/ejabberd restart

### Enable Jingle(voice and video)

You need JingleNodes module on your server.

\# apt-get install erlang-tools
# git clone git://git.process-one.net/exmpp/mainline.git exmpp
# cd exmpp; ./configure; make; make install
# svn checkout http://jinglenodes.googlecode.com/svn/ jinglenodes
# cd jinglenodes; ./configure --prefix=/usr/; make; make install

Add following content to your ejabberd.cfg in the **modules** section.

  {mod\_jinglenodes, \[
		     {host, "jinglenodes.@HOST@"},
		     {public\_ip, "192.168.1.148"},
		     {purge\_period, 5000},
		     {relay\_timeout, 60000}
		    \]},

### Enable web register(optional)

Add to ejabberd.cfg, 'modules' section the basic configuration:

{modules, \[
  ...
  {mod\_register\_web,     \[\]},
  ...
\]}.

In the 'listen' section enable the web page:

{listen, \[
  ...
  {5281, ejabberd\_http, \[
	tls,
    {certfile, "/etc/ejabberd/ejabberd.pem"},
    {request\_handlers, \[
      {\["register"\], mod\_register\_web}
    \]}
  \]},
  ...
\]}.

### Use your own certificate

openssl req -new -x509 -newkey rsa:1024 -days 3650 -keyout privkey.pem -out server.pem
openssl rsa -in privkey.pem -out privkey.pem
cat privkey.pem >> server.pem
rm privkey.pem

The port numbers you should open are: 5281(http://localhost:5281/register/) 5280(http://localhost:5280/admin) and 5222(for c2s).

Register users:

\# ejabberdctl register admin lofyer.org mypassword
# ejabberdctl register myphone lofyer.org mypassword
# ejabberdctl register mypc lofyer.org mypassword

## 2\. Pidgin and MAXS test

Pidgin: admin@lofyer.org MAXS: myphone@lofyer.org By the way, guarantee that there is only one running jabber client on your phone during this period. **Pidgin** Add a friend [![pidgin](/blog/images/pidgin.png)](http://blog.lofyer.org/ejabber-howto/pidgin/) **Shell test** [![shell](/blog/images/shell.png)](http://blog.lofyer.org/ejabber-howto/shell/) **SMS test** [![SMS-SEND](/blog/images/SMS-SEND.png)](http://blog.lofyer.org/ejabber-howto/sms-send/) And a msg to my GF. [![sms-receive](/blog/images/sms-receive.jpg)](http://blog.lofyer.org/ejabber-howto/sms-receive/)
