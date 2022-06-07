---
title: "Using X server in Windows Linux Subsystem"
date: "2016-10-09"
categories: 
  - "linux-admin"
---

1\. Turn on "Developer Mode" in Control panel. [![developer-mode](/blog/images/developer-mode.png)](http://blog.lofyer.org/wp-content/uploads/developer-mode.png) [![sss](/blog/images/sss.png)](http://blog.lofyer.org/wp-content/uploads/sss.png)

2\. Run "bash" [![bash](/blog/images/bash.png)](http://blog.lofyer.org/wp-content/uploads/bash.png)

3\. Install Xming(Xserver for Windows) [Download](https://sourceforge.net/projects/xming/)

4\. Launch your app

\# export DISPLAY=:0
# firefox

[![launch](/blog/images/launch.png)](http://blog.lofyer.org/wp-content/uploads/launch.png)

5\. You can create a link on your desktop like this [![aaa](/blog/images/aaa.png)](http://blog.lofyer.org/wp-content/uploads/aaa.png)

and ~/.bashrc

alias home='cd /mnt/c/Users/rex/Desktop'
home
export DISPLAY=:0

Tips:

Use "powershell bash" instead of "bash", you can access your service in this way.
