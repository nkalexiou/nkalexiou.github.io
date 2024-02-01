---
layout: post
title:  "Basic email security analysis"
date:   2024-02-01 14:00:00 +0300
categories: cybersecurity
description: "How to perform a basic security analusis of an email"
keywords: "Phishing, Email, Security Analysis"
---

I haven't written a blog post for some time now, so here we go: Security analysis of emails!

## Why security analysis of emails

This blog post is an introduction to performing analysis of emails, proposing tools and methodologies. When working in cybersecurity we constantly come across emails that look suspicious, have phishy links, contain malware, want to harvest credentials, want to scam people etc. You should definetely shield your company against what I will now call "email attacks" by using specialized software that neutralizes these attacks. Microsoft and many other security vendors offer such solutions. 

However, even when using these solutions a phishing email will occasionally succeed to make it to the victim's inbox, or you might find a particularly interesting case you would like to analyze. This is why having the basic skills to analyze emails is important. 

## Email Headers Analysis

Email headers will help you understand how the email made it to your tenant. You can analyze headers using one of these tools:

- ![Google](https://toolbox.googleapps.com/apps/messageheader/)
- ![mha](https://mha.azurewebsites.net/)

Of course there are plenty more options out there. 

What this will tell you is the source IP, Destination IP but also whether SPF and DKIM checks succeeded. 

## Hash and IP checks

You can check whether IPs are known to deliver malware or phishing emails using many services. 

Two of my favourites are:

- ![VirusTotal](https://www.virustotal.com/gui/home/upload)
- ![AbuseIPDB](https://www.abuseipdb.com/)

In particular, using virus total you can check file hashes that might be contained in the suspected email. On top of that, I like the VT graph capabilities that help you undestand how one of the data you are analysing, such as IPs and subdomains, are related to other components (certificates, domains, malware, IPs etc). Here is an example for one IP address I investigated which clearly shows me that the IP was used in the past to deliver malware:

![image]({{site.baseurl}}/docs/assets/images/2024/vtgraph.png "Virus Total map")

## Other sources of information

Besides IP and hash values you might want to check details of the domain the email is linking to. You have a few options at this point, one of which is to analyze DNS records. A valuable tool for this is ![DNSDumpster](https://dnsdumpster.com/). Besides DNS information you can also check certificates by using ![Crtsh](https://crt.sh/). A common scenario is that phishing websites will use Let's Encrypt certificates that are issued very recently in relation to the time of the investigation. 

## Qualitative analysis

Besides the technical details, analysing the quality of text and email intend will help you figure out whether a case is a false positive or an actual true positive case. 
