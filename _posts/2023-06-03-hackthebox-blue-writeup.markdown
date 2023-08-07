---
layout: post
title:  "Hackthebox - Blue writeup"
date:   2023-06-03 19:00:00 +0300
categories: HTB write-up
---



Blue is an easy retired hackthebox machine that show cases the eternal blue vulnerability. Eternal blue, or ms17_010 is a critical vulnerability affecting earlier versions of Windows using SMBv1.
Recon

Firstly, let's run an nmap scan on 10.10.10.40 that is the ip adress of the machine.

nmap -sS -sV -sC 10.10.10.40

nmap results
nmap results

Metasploit has a module to exploit ms17_010. First, let's use a Metasploit scanner module that will determine whether the target is vulnerable.
metasploit scanner for ms17_010
metasploit scanner for ms17_010

Great, it looks like the machine is vulnerable. Now it is time to run the exploit module.

Alternative:

We could have verified the vulnerability with nmap using the following command:

nmap -p 135,139,445 --script vuln 10.10.10.40

Exploitation

We will use exploit/windows/smb/ms17_010_eternalblue and the default reverse_tcp meterpreter payload.

We can now find the user and the machine flag.

Failed attempt:

The following Github repository provides an alternative to exploit ms17_010 outside metasploit.
GitHub - 3ndG4me/AutoBlue-MS17-010: This is just an semi-automated fully working, no-bs, non-metasploit version of the public exploit code for MS17-010
This is just an semi-automated fully working, no-bs, non-metasploit version of the public exploit code for MS17-010 - GitHub - 3ndG4me/AutoBlue-MS17-010: This is just an semi-automated fully workin…
GitHub3ndG4me

However, when I tried to follow the steps detailed in the repo I caused a blue screen of death at the victim. I guess that means the name eternal blue is justified!
