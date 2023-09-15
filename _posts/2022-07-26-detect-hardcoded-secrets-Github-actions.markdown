---
layout: post
title:  "Detect hardcoded secrets using Github actions"
date:   2022-07-26 19:00:00 +0300
categories: appsec
description: "Improve your DevSecOps and detect hardcoded secrets using Github actions"
keywords: "Github, DevSecOps, hardcoded secrets, appsec"
---

Production passwords, tokens, AWS credentials and other types of secrets should be kept confidential and away from preying eyes of hackers or malicious insiders. In simple terms, no one should know production passwords for databases, admin passwords, AWS credentials etc unless there are legitimate business reasons that mandate to do so.  Therefore, keeping these secrets hard-coded in Github repositories must be strictly prohibited.

But as humans we all make mistakes and accidents will happen.  Junior developers might be unaware of secure coding practices and maybe even senior devs may rush code to production being stressed to deliver a new feature. It is therefore important to have controls in place to detect hard-coded secrets that accidentally end up in code.
The detection tool: Whispers

There are many alternatives to detect secrets in code, depending on the source management platform in use. Among the most noteworthy are:

- Github Secret Scanning: free for public repositories but requires a license for Advanced Security for organizations.
- Semgrep secrets ruleset: Semgrep is a static code analyzer that has a free version, which includes rules for finding secrets. More advanced options and customization (which will be required especially in business environments) require a license.
- Gitlab secret detection: basic features enabled regardless of Gitlab tier.

In this blog post we will use Whispers, which is a light-weight code scanner that detects hard-coded secrets.

[Whispers@Github](https://github.com/Skyscanner/whispers?ref=appsecguy.se)

It is customizable and easy to use. In this blog post I will present an example of how to use whispers with Github actions so that it becomes part of your continuous integration pipeline.
Github Action for hard-coded secrets detection

We will install whispers using pip and then provide a configuration file so that we can customize it's behavior.

```
name: Whispers secrets scanner

on:
  push:
    branches: [ "main" ]
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        name: Check out code to be scanned 
      
      - name: install whispers
        run: pip3 install whispers 
      
      - name: run whispers
        run: whispers --config config.yml /src/config

```

Our Github action runs in three steps: first the code to be analyzed is checked out, then whispers is installed using pip and finally the following whispers command performs the scan:

```
whispers --config config.yml /src/config
```

This example assumes that the code you wish to scan resides in /src/config, but you can adjust this to point at any folder, or all you code base ( using ./) . If you omit the config parameter then the default configuration will be used which is good enough. In practice you will need to whitelist findings so a custom config file is needed. Below you see an example of a custom config.yml that excludes all files and folders starting with test.

```
include:
  files:
    - "**/*"

exclude:
  files:
    # exclude any test related file or folder
    - "test*"
    
  keys:
    - .*(public|project).*
    
  values:
    - ^(true|false|yes|no|1|0)$
    - .*_(user|password|token|key|placeholder|name)$
    - ^aws_(access_key_id|secret_access_key|session_token)$
    - ^arn:aws:.*
```

Detailed documentation and more examples can be found on whispers' Github page.

You can see only findings in the logs of Github actions! Happy hacking!
