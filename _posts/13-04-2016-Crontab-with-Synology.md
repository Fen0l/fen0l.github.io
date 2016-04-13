---
layout: post
description: Synology has recently release a new verison of his DSM. But unfortunatly, his backup system has totaly changed. In this article, I will explain how crond works and why I needed it.
category: synology
author: Anthony Pradal
tags: crond Synology Backup-and-Recovery
---

# How to use crontab with Synology


During my internship, I had to backup files onto external hard drives. They owned a Synology NAS running under DSM 5. Nevertheless, The Synology Team had decided to push the new DSM version, DSM 6.0. With this major upgrade, they have fully revisited their backup system.

Its no longer possible to do a simple file to file backup. Their new system is strange. . So, I decided to create a Python script to backup this files onto an external drives connected by USB.

**And to do that, what is better than crontab ?**

Cron is a daemon system used to execute desired tasks (in the background) at designated times.

In my situation, I had to execute every hour my python script. To do that, I had to modify my /etc/crontab file and to restart the crond daemon.

```
crond stop
crond start
```

Everything was good, but after a power crash on the building, There wasn't any backup. And to confirm my idea, I check my crontab file. Yes, my script execution didn't exist anymore.

### Why ?

Synology is very complicated. For example, after each reboot **(for DSM 4, 5 and 6)**, the /etc/crontab file is regenerated. I don't know why it doing this.

### What I did ?

To keep my crond tasks, I had to modify the /etc.defaults/crontab file. On each reboot, Synology have to make a copy of this file into /etc/crontab.

However, be careful. You **must** respect their syntax. 

```
#minute	hour	mday	month	wday	who	command
0	*/1	*	*	*	root	python /volume1/Scripts/backup_hdd.py
```

Every field have to be separeted by a tabulation. To write my task, I had to do:

0 **TAB** */1 **TAB** * **TAB** * **TAB** * **TAB** root **TAB** python /volume1/Scripts/backup_hdd.py


I hope that this little tips will save your time.



