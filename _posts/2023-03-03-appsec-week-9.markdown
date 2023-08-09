---
layout: post
title:  "AppSec week 9 in summary"
date:   2023-03-03 19:00:00 +0300
categories: newsletter
---

LastPass hacked again, Github secret scanner, booking.com account takeover and OWASP CycloneX. AppSec Week 9 in summary, Friday March 3 edition.

<br>

## News

### Lastpass hacked through compromized dev machine

LastPass says employee’s home computer was hacked and corporate vault taken. Plex software hacked on DevOps engineer's computer giving threat actor access to the computer. The master password to the LastPass corporate vault was then exposed to the hackers who obtained decryption keys to LastPass production backups. Already smarting from a breach that stole customer vaults, LastPass has more bad news.

[arstechnica news article](https://arstechnica.com/information-technology/2023/02/lastpass-hackers-infected-employees-home-computer-and-stole-corporate-vault/?ref=appsecguy.se)


### Github Secret Scanning is now availble for all public repositories

Github announced that Secret Scanning is now available for free for all public repositories. This is great news as a new powerfull tool is added to the arsenal of defenders.

[blog article](https://github.blog/2023-02-28-secret-scanning-alerts-are-now-available-and-free-for-all-public-repositories/?ref=appsecguy.se)


### Explaining the account takeover flow in booking.com

Booking.com recently suffered an account takeover vulnerability due to a buggy implementation of OAuth protocols that allowed the attackers just enough freedom to manipulate protocol behavior. The following link provides an excellent writeup of the vulnerability.

[salt.security post](https://salt.security/blog/traveling-with-oauth-account-takeover-on-booking-com?ref=appsecguy.se)


### IBM contributes Supply Chain Security tools to OWASP

Since the log4j vulnerability the discussion regarding Software Bill of Materials (SBOMs) has heated up. OWASP has an active standardization project (CycloneX Software Bill of Materials) to promote the generation of SBOMs and IBM contributed to that project with two great tools.

[Dark reading article](https://www.darkreading.com/dr-tech/ibm-contributes-supply-chain-security-tools-to-owasp?ref=appsecguy.se)

<br>

## What was interesting this week?


### How to prevent broken authentication flaws webinar

An interesting webinar by Jim Manico and Clint Gibler about preventing broken access control announced for the 15th of March.

[https://get.semgrep.dev/clint-collabs-jim-manico.html](https://get.semgrep.dev/clint-collabs-jim-manico.html?ref=appsecguy.se)