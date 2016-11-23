---
layout: post
title: LogRotate Configuration
category: Linux
tags: Linux
description: How to set up LogRotate 
---
## 1.LogRotate

logrotate - rotates, compresses, and mails system logs

configuration file is located at /etc/logrotate.conf.
Let's look at what inside of the configuration file

#rotate log files frequency

default is weekly, there is daily and monthly

#keep 4 weeks worth of backlogs

rotate 4 (means keep maximum 4 logs)

#see "man logrotate" for details

#rotate log files weekly

weekly

#keep 4 weeks worth of backlogs
rotate 4

#create new (empty) log files after rotating old ones
create

#use date as a suffix of the rotated file
dateext (The log will be saved as xxx.log-20161122)

#uncomment this if you want your log files compressed
compress

#packages drop log rotation information into this directory
include /etc/logrotate.d

/var/log/wtmp {
    missingok  // means if there is't exist, it is ok
    notifyempty
    sharedscripts // means run the same script under postrotate
    delaycompress
    postrotate // run below script after log rotate
    	/sbin/service httpd reload > /dev/null 2>/dev/null || true
    endscript
    monthly
    create 0664 root utmp
    rotate 1
}

You can use below command force to run the rotate.
logrotate -f /etc/logrotate.conf