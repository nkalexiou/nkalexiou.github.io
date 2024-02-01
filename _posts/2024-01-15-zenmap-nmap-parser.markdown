---
layout: post
title:  "Zenmap & Nmap Parser"
date:   2024-01-15 13:00:00 +0300
categories: cybersecurity
description: "Open source tool to transform Nmap XML output files to CSV"
keywords: "Cybersecurity"
---

This is goind to be a quick blog post. 

Introducing my new open source tool that parses nmap and zenmap output files from XML and creates CSV reports for further analysis. 
I regularly have to present portscan results in a format that people, even non-technical people, can see and hopefully undestand. With nmap you have the option to out put results in xml format and this tool parses xml output to create:

- a CSV file containing open ports and other info, per XML output file
- a CSV file containing Operating System information per scanned host, per XML output file
- a CSV file containing all XML reports in CSV format.

For more information please visit ![https://github.com/nkalexiou/zenmap-parser](https://github.com/nkalexiou/zenmap-parser)

Cheers!