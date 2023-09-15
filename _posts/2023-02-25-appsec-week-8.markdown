---
layout: post
title:  "AppSec week 8 in summary"
date:   2023-02-25 19:00:00 +0300
categories: newsletter
keywords: "newsletter, AppSec, Application Security"
---

This is the first time I do a newsletter. My goal is to summarize the week and the  important news about appsec. I can’t promise it will happen every week, but please reach out if you think it is worth it or if you have feedback.

You might find some older news from previous weeks as well that were deemed important for this first post…Ok, let’s get to it.

<br>

## News

### DoS vulnerability in Apache commons

DoS vulnerability discovered in Apache commons, due to an attacker being able to upload arbitrary number of file parts. Given the popularity of this package it is quite a noteworthy issue. Be sure to always define max size and max number of files that can be uploaded.

[https://github.com/advisories/GHSA-hfrx-6qgj-fp6c](https://github.com/advisories/GHSA-hfrx-6qgj-fp6c)


### NPM repositories containing phishing links

Packages created using automated process with names that resemble real projects. The aim is to get the victim to click on links in the project description.

[https://thehackernews.com/2023/02/attackers-flood-npm-repository-with.html?m=1](https://thehackernews.com/2023/02/attackers-flood-npm-repository-with.html?m=1)


### Increase in attacks targeting developer tools.

Example on how hackers fingerprint Confluence servers to exploit an OGNL injection vulnerability - Johanes Ullrich from SANS

[SANS link](https://isc.sans.edu/diary/Internet%20Wide%20Scan%20Fingerprinting%20Confluence%20Servers/29574)


<br>

## What was interesting this week?


### Geolocation map for Killnet

Daniel Chronlund, a Microsoft MVP and security researcher, made a map of all ip addresses the killnet bot network uses.

You can find it [here](https://www.linkedin.com/posts/daniel-chronlund-0b714a21_killnet-powershell-powerbi-activity-7034059610500009985-7fwr?utm_source=share&utm_medium=member_ios) 


### Home security guide by the NSA

NSA created a guide about how to secure home networks aiming at helping regular people improve their security.

[link](https://media.defense.gov/2023/Feb/22/2003165170/-1/-1/0/CSI_BEST_PRACTICES_FOR_SECURING_YOUR_HOME_NETWORK.PDF)


### Twitter made sms-based two-factor authentication available for verified accounts only.

SMS is the weakest type of 2FA someone can use. Given the fact that a lot of “blue ribbon” accounts are valuable targets for hackers it makes absolutely no sense to enable sms only for them.


### OWASP released an AI Security and Privacy project.

You read more about the project here: [https://owasp.org/www-project-ai-security-and-privacy-guide/](https://owasp.org/www-project-ai-security-and-privacy-guide/)
