---
layout: post
title:  "Cheatips for Sysadmins maintenances"
date:   2019-10-17 21:23:00
author: Anthony
categories: Development
tags:	sysadmin automation
---

# List of usefull scripts as a sysadmin boy

## Monitoring

Easy way to monitor servers connectivity during large maintenance

{% highlight powershell %}
"[MIGRATION] Alive Network Configuration Check"
ForEach ($targetSrv in (Get-Content .\serverlist.txt)) {
    if (Test-Connection -ComputerName $targetSrv -Count 1 -Quiet) {
        "[+] $targetSrv - Conn OK"
    } else {
        "[-] $targetSrv - Conn FAIL"
    }
}
{% endhighlight %}
