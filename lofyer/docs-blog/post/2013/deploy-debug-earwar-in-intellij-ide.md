---
title: "deploy & debug ovirt in the intellij ide"
date: "2013-04-05"
categories: 
  - "cloud"
---

Before we start, we need intellij enterprise version and a register tool.. \[url\]

\[Summary\] 1. We should open the pom.xml file instead of the project directory. 2. Modify the extension of the directory in the project properties. 3. Modify the .m2 file in your personal directory to make jboss know where to deploy the project. 4. We need seperate run/debug config to make it clearer.

\[Steps\] _OVIRT\_HOME=/home/demo JBOSS\_HOME=/usr/jboss-as JDK=/usr/java/jdk1.7.0\_17 $ cmd as demo # cmd as root_ **1.Compile the ovirt from src** in $OVIRT\_HOME

$ mvn clean install -Pgwt-admin,gwt-user -DskipTests=true

**2.Deploy ovirt** You need ~/.m2/settings.xml file in /root/ and /home/demo directory.

 oVirtEnvSettings 

        oVirtEnvSettings
                        /usr/jboss-as
                           /usr/java/jdk1.7.0\_17 

in $OVIRT\_HOME/ear

\# mvn clean install -Pdep,setup

**3.Run jboss** You should export an environment variant and a runtime configuration file.

$ echo -e "ENGINE\_USR=admin111nENGINE\_ETC=/etc/ovirt-engine" > $OVIRT\_HOME/backend/manager/conf/engine.conf.defaults
# export ENGINE\_DEFAULTS=$OVIRT\_HOME/backend/manager/conf/engine.conf.defaults
# /usr/jboss-as/bin/standalone.sh

UPDATE: Now there is README.developer in the source dir, just follow it.
