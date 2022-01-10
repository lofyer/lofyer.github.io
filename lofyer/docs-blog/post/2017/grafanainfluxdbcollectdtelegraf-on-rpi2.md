---
title: "Grafana+InfluxDB+Collectd/Telegraf on RPi2"
date: "2017-03-06"
categories: 
  - "linux-admin"
---

Grafana will provide a visual view for the sites, InfluxDB is the data box, and collectd/telegraf is the agent on the server. Here we go.

**Install Grafana:**

\# Download deb from https://github.com/fg2it/grafana-on-raspberry
root@raspberrypi:~# rpm -i grafana.deb
root@raspberrypi:~# service grafana-server start

**Install InfluxDB:** Download from [https://portal.influxdata.com/downloads](https://portal.influxdata.com/downloads)

root@raspberrypi:~# tar xf influxdb-1.2.0\_linux\_armhf.tar.gz
root@raspberrypi:~# cp -a influxdb-1.2.0-1/\* /

vim /etc/influxdb/influxdb.ini:

\[admin\]
enabled=true

\[http\]
enabled=true

\[collectd\]
enabled=true
bind-address=":25826"
database="collectd"

Then run "influxdb &" and check it out in http://localhost:8083, add db named "collectd".

**Install Collectd:**

root@raspberrypi:~# apt-get install collectd

In /etc/collectd/collectd.conf, find :

<Plugin network>
    <Server "localhost" "25826"></Server>
</Plugin>

Then restart collectd service.

Now you can visit http://localhost:3000 to add InfluxDB source and add panel.
