---
layout: post
title:  "AppSec week 9 in summary"
date:   2023-03-03 19:00:00 +0300
categories: newsletter
---

LastPass hacked again, Github secret scanner, booking.com account takeover and OWASP CycloneX. AppSec Week 9 in summary, Friday March 3 edition.

News


Lastpass hacked through compromized dev machine

Plex software hacked on DevOps engineer's computer giving threat actor access to the computer. The master password to the LastPass corporate vault was then exposed to the hackers who obtained decryption keys to LastPass production backups.
LastPass says employee’s home computer was hacked and corporate vault taken
Already smarting from a breach that stole customer vaults, LastPass has more bad news.
Ars TechnicaDan Goodin

Github Secret Scanning is now availble for all public repositories

Github announced that Secret Scanning is now available for free for all public repositories. This is great news as a new powerfull tool is added to the arsenal of defenders.
Secret scanning alerts are now available (and free) for all public repositories | The GitHub Blog
Secret scanning alerts are now generally available for all public repositories. Admins can now turn on the alert experience with one click.
The GitHub BlogZain Malik

Explaining the account takeover flow in booking.com

Booking.com recently suffered an account takeover vulnerability due to a buggy implementation of OAuth protocols that allowed the attackers just enough freedom to manipulate protocol behavior. The following link provides an excellent writeup of the vulnerability.
Salt Labs | Traveling with OAuth - Account Takeover on Booking.com
Given the widespread usage of OAuth, any vulnerabilities found in its components or their implementations may lead to considerable security impact in the applications and services using them.
Account Takeover on Booking.comAviad Carmel

IBM contributes Supply Chain Security tools to OWASP

Since the log4j vulnerability the discussion regarding Software Bill of Materials (SBOMs) has heated up. OWASP has an active standardization project (CycloneX Software Bill of Materials) to promote the generation of SBOMs and IBM contributed to that project with two great tools.
IBM Contributes Supply Chain Security Tools to OWASP
License Scanner and SBOM Utility will boost the capabilities of OWASP’s CycloneDX Software Bill of Materials standard.
Dark ReadingJeffrey Schwartz

What was interesting this week?


How to prevent broken authentication flaws webinar

An interesting webinar by Jim Manico and Clint Gibler about preventing broken access control announced for the 15th of March.
Semgrep | Broken Access Controls Webinar
Join Clint and Jim on March 15 to learn why
Gitlab logo