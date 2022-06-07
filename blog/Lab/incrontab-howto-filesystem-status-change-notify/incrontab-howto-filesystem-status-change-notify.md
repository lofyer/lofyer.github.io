---
title: "incrontab howto - filesystem status change notify"
date: "2015-03-31"
categories: 
  - "linux-admin"
---

**1\. Install incrontab.**

**2\. Edit /etc/incron.allow**

root

**3\. Use incrontab command**

<path> <mask> <command>
IN\_ACCESS File was accessed (read) (\*)
IN\_ATTRIB Metadata changed (permissions, timestamps, extended attributes, etc.) (\*)
IN\_CLOSE\_WRITE File opened for writing was closed (\*)
IN\_CLOSE\_NOWRITE File not opened for writing was closed (\*)
IN\_CREATE File/directory created in watched directory (\*)
IN\_DELETE File/directory deleted from watched directory (\*)
IN\_DELETE\_SELF Watched file/directory was itself deleted
IN\_MODIFY File was modified (\*)
IN\_MOVE\_SELF Watched file/directory was itself moved
IN\_MOVED\_FROM File moved out of watched directory (\*)
IN\_MOVED\_TO File moved into watched directory (\*)
IN\_OPEN File was opened (\*)

When monitoring a directory, the events marked with an asterisk (\*) above can occur for files in the directory, in which case the name field in the returned event data identifies the name of the file within the directory.

The IN\_ALL\_EVENTS symbol is defined as a bit mask of all of the above events. Two additional convenience symbols are IN\_MOVE, which is a combination of IN\_MOVED\_FROM and IN\_MOVED\_TO, and IN\_CLOSE which combines IN\_CLOSE\_WRITE and IN\_CLOSE\_NOWRITE.

The following further symbols can be specified in the mask:
IN\_DONT\_FOLLOW Don't dereference pathname if it is a symbolic link
IN\_ONESHOT Monitor pathname for only one event

IN\_ONLYDIR Only watch pathname if it is a directory

Additionaly, there is a symbol which doesn't appear in the inotify symbol set. It it IN\_NO\_LOOP. This symbol disables monitoring events until the current one is completely handled (until its child process exits). 

Command translation

$$   dollar sign
$@   watched filesystem path (see above)
$#   event-related file name
$%   event flags (textually)
$&   event flags (numerically)

\# incrontab -l
# incrontab -e
/var/www IN\_ACCESS echo "/var/www/ wass accessed at $$date"

Access your web server in a browser and watch the status change.
# tail /var/log/syslog
