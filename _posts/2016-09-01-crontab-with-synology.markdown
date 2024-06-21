---
layout: post
title:  "Crontab with Synology"
date:   2016-09-01 09:23:00
author: Anthony
categories: Synology
tags:	crond Synology Backup-and-Recovery
cover:  "/assets/banner/synology.png"
---

# How to use crontab with Synology


During my internship, I needed to back up files to external hard drives. The organization had a Synology NAS running DSM 5. However, the Synology team decided to upgrade to DSM 6.0. With this major upgrade, they completely overhauled their backup system.

It was no longer possible to perform a simple file-to-file backup. Their new system seemed confusing, so I decided to create a Python script to back up files to an external drive connected via USB.

**And to do that, what is better than crontab ?**

Cron is a daemon used to execute scheduled tasks (in the background) at designated times.

In my case, I needed to run my Python script every hour. To achieve this, I had to modify the /etc/crontab file and restart the crond daemon.

```
crond stop
crond start
```

Everything was working fine until a power outage occurred in the building. After the outage, there were no backups. To confirm my suspicion, I checked my crontab file and found that my scheduled script execution was missing.

### Why ?

Synology can be quite complex. For example, after each reboot (this applies to DSM 4, 5, and 6), the /etc/crontab file is regenerated. I don't know why it does this.

### What I did ?

To preserve my crond tasks, I had to modify the /etc.defaults/crontab file. Upon each reboot, Synology copies this file into /etc/crontab.

However, be careful. You must adhere to their syntax.

```
#minute	hour	mday	month	wday	who	command
0	*/1	*	*	*	root	python /volume1/Scripts/backup_hdd.py
```

Every field have to be separeted by a tabulation. To write my task, I had to do:

0 **TAB** */1 **TAB** * **TAB** * **TAB** * **TAB** root **TAB** python /volume1/Scripts/backup_hdd.py


I hope that this little tips will save your time.


