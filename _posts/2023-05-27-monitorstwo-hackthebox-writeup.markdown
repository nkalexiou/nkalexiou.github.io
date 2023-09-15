---
layout: post
title:  "Hackthebox - MonitorsTwo writeup"
date:   2023-05-27 19:00:00 +0300
categories: HTB write-up
description: "Hackthebox - HTB MonitorsTwo writeup - How to solve this box"
keywords: "Hack the box, HTB, writeup, MonitorsTwo"
---

## Recon

Portscanning of the target reveals two ports, 80 and 22.

<br>

![image]({{site.baseurl}}/docs/assets/images/2023/htb-monitorstwo-nmap.png "HTB MonitorsTwo nmap")

<br>

*Observations*

Port 80 hosts software called cacti, which is an RDP solution. The software version is revealed in the comments, version 1.2.22.

A google search shows that this version is vulnerable to remote code execution.

<br>

## Initial foothold

There are several Github repositories and scripts but not all will work out of the box. I ended up using the exploit [here](https://github.com/MarkStrendin/CVE-2022-46169/blob/main/exploit.py?ref=appsecguy.se). Unfortunately I wasn't able to get a fully interactive shell.


<br>

## User flag

I run linpeas on the target to find more information. I see this is a docker container and there is interesting network configuration revealing more reachable ips. For example check /etc/hosts.

Linpeas revealed some interesting password related findings.

<br>

![image]({{site.baseurl}}/docs/assets/images/2023/htb-monitorstwo-linpeas.png "HTB MonitorsTwo nmap")

<br>

A search in /var/www/html for php files containing "password", "root" or "cactiuser" reveals the root password, which is root. I then try to connect to the db but I cannot get it to work for some reason. It is at this point I notice the "entrypoint.sh" shell script in root which contains some interesting info.

<br>

![image]({{site.baseurl}}/docs/assets/images/2023/htb-monitorstwo-entrypoint.png "HTB MonitorsTwo entrypoint")

<br>

So there is an ip that resolves to hostname db that we can connect to with mysql.

<br>

![image]({{site.baseurl}}/docs/assets/images/2023/htb-monitorstwo-host.png "HTB MonitorsTwo db host")

<br>


I interact with the db using the following command and I extract the hashed passwords from the auth table. I then feed these into john and within a few seconds I get a hint.

```
john passwords.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

The password that john returns corresponds to a user called marcus which can be used to ssh in the target machine. The user flag is in the home folder of the user.

<br>

## Root flag

Linpeas reveals a few extra findings, one of which is particularly intersting.

<br>

![image]({{site.baseurl}}/docs/assets/images/2023/htb-monitorstwo-cve.png "HTB MonitorsTwo CVE")

<br>

This email lists a few vulnerabilities present on the target host machine. The XSS one is not interesting and I couldn't get any exploit code examples for CVE-2021-33033. But googling for CVE-2021-41091 reveals some exploit code examples and could be a good fit for privilege escalation.

According the following Github PoC and other websites discussing the vulnerability, I will need root privileges on the docker container to make this work.

[GitHub - UncleJ4ck/CVE-2021-41091: POC for CVE-2021-41091](https://github.com/UncleJ4ck/CVE-2021-41091?ref=appsecguy.se)

Back in the container I check the linpeas report again and I see a highlighted finding.

Gtfofbins shows an example of how to get root from capsh. I used the following command to get root:

```
capsh --gid=0 --uid=0 --
```

I then follow the steps as detailed in the Github page above and they work out of the box.

<br>

![image]({{site.baseurl}}/docs/assets/images/2023/htb-monitorstwo-exploitation.png "HTB MonitorsTwo exploitation")

<br>

Hmmm.. I see I am back in the container as root again and not in the host machine. Instead of navigating into the directory that the script point you to I had to run the following command as is:

```
/var/lib/docker/overlay2/c41d5854e43bd996e128d647cb526b73d04c9ad6325201c85f73fdba372cb2f1/merged/bin/bash -p
```

That's it! I am root on the host target machine and I got the root flag as well. 