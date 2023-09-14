---
layout: post
title:  "Hackthebox - Blue writeup"
date:   2023-06-03 19:00:00 +0300
categories: HTB write-up
description: "Hackthebox - HTB Blue writeup - How to solve this box"
---


Blue is an easy retired hackthebox machine that show cases the eternal blue vulnerability. Eternal blue, or ms17_010 is a critical vulnerability affecting earlier versions of Windows using SMBv1.

<br>

## Recon

Firstly, let's run an nmap scan on 10.10.10.40 that is the ip adress of the machine.

```
nmap -sS -sV -sC 10.10.10.40
```

<br>

![image]({{site.baseurl}}/docs/assets/images/2023/htb-blue-nmap.png "HTB Blue nmap")

<br>

Metasploit has a module to exploit ms17_010. First, let's use a Metasploit scanner module that will determine whether the target is vulnerable.


<br>

![image]({{site.baseurl}}/docs/assets/images/2023/htb-blue-metasploit.png "HTB Blue Metasploit")

<br>

Great, it looks like the machine is vulnerable. Now it is time to run the exploit module.


**Alternative:**

We could have verified the vulnerability with nmap using the following command:

```
nmap -p 135,139,445 --script vuln 10.10.10.40
```

<br>

![image]({{site.baseurl}}/docs/assets/images/2023/htb-blue-nmap-alternative.png "HTB Blue nmap alternative")

<br>

## Exploitation

We will use exploit/windows/smb/ms17_010_eternalblue and the default reverse_tcp meterpreter payload.


<br>

![image]({{site.baseurl}}/docs/assets/images/2023/htb-blue-metasploit-exploitation-1.png "HTB Blue nmap Metasploit command")

<br>


<br>

![image]({{site.baseurl}}/docs/assets/images/2023/htb-blue-metasploit-exploitation-2.png "HTB Blue nmap Metasploit result")

<br>


<br>

![image]({{site.baseurl}}/docs/assets/images/2023/htb-blue-metasploit-exploitation-3.png "HTB Blue nmap Metasploit System")

<br>

We can now find the user and the machine flag.

<br>

**Failed attempt:**

The following Github repository provides an alternative to exploit ms17_010 outside metasploit.

[GitHub - 3ndG4me/AutoBlue-MS17-010](https://github.com/3ndG4me/AutoBlue-MS17-010?ref=appsecguy.se)

However, when I tried to follow the steps detailed in the repo I caused a blue screen of death at the victim. I guess that means the name eternal blue is justified!
