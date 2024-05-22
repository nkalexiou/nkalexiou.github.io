---
layout: post
title:  "How to choose the right SAST and SCA tools"
date:   2024-05-22 19:00:00 +0300
categories: appsec
description: "Having SAST and SCA tool might not be enough. Choose the tools that work for you. Simplistic guide with important SAST and SCA features."
keywords: "appsec, product security, cybersecurity, sast, sca, security tools"
---

Let's imagine you are in the situation where you have secured budget to buy tools that analyze the security of your code base. Most probably you will look for a Static Application Security Testing (SAST) solution to scan source code and a Software Composition Analysis (SCA) tool, to create your SBOMs and analyze security/licenses of open-source dependencies. 

What are some important features to look for when browsing the market? 

## Features for code security analysis tools

### Analysis speed

The time it takes to analyze your code is of course one of the main attributes to evaluate when picking a SAST tool (SCA is a bit different in this category and it will be discussed later on). There are tools that scan large code bases in 10-15 minutes and others that take 3-4 hours. The main difference affecting analysis speed is how the actual analysis is performed. Usually, tools using regular expressions to scan source code without requiring a build are the winner in this category. It is much faster to analyze code by looking for bad patterns compared to building an abstract syntax tree from scratch with every build. 

Keep in mind that it is really hard to achieve adoption for tools that take more than a few minutes to scan code and produce results. Incremental scans promise faster scan times even if the SAST tool relies on building the software, but in my experience they do not produce results fast and they increase the complexity of the solution. 

Unless you really need deep and detailed analysis of the abstract syntax tree to find edge cases, I would advise to stick to avoid these SAST solutions.

### Code coverage and build configurations

An obvious factor when choosing the right SAST solution for you is to be able to scan all, or most of, the programming languages being used. Have a PoC with a representative code base and make sure that the SAST tool can actually handle the programming language(s) being used and more importantly, the way you are building your software. For example, test both Maven and Gradle for Java projects using your actual build scripts and configurations. 

How you build your code is also important for the SCA tool, so make sure to test all possible build configurations.

### Integration with VCS

Make sure that the tool you are choosing works with the version control system you are using. Consider the type of license you have for tools like Gitlab and Github and check if SAST and SCA tool results can be imported into Github or Gitlab. 

### IDE Plugins

Most SAST tools offer plugins for popular IDEs, which is a must-have so that vulnerabilities are caught early in the process. Finding and remediating vulnerabilities in the IDE is much cheaper than fixing those later in the development process.  

The same principle applies to SCA IDE plugins that can warn you when importing new vulnerable components, thus keeping your code base cleaner.


## Why is this not enough

Unfortunately, buying a solution at this point is not enough in my opinion. There are two important features that your SAST and SCA tools must have. 

### Am I impacted? 

Your SCA tool will be noisy. It will detect many vulnerabilities because that is the state of open-source security today. If you can get development teams to fix all those then congratulations, you belong to <1% of appsec people leaving a happy life. The rest of us have to go to battle to get stuff fixed and knowing whether your code base is actually affected by an open-source component vulnerability is gold. Not every SCA tool offers that, but I would never choose one that doesn't. Spend your money wisely. 

### CI ok, but plugin? 

All SAST tools will offer docker images that you can pull at build time from your CI pipeline and scan your code. But do you have the alternative to use a plugin within e.g., Github or Gitlab to scan any project you wish without even interacting with developer teams? This feature is extremely important in order to move fast and start analyzing projects in zero time. The alternative of integrating with CI has multiple benefits, but it will take months in medium-sized companies, in a best case scenario. Wouldn't you value the ability to scan from day-1 as you wish and use those results to motivate development teams to integrate SAST in the CI? 

What about SCA and plugins then? 

Well unfortunately in order to do a proper SCA analysis you need to build your code. But again, having a, SCA plugin in Github or Gitlab that produces results without the feedback on whether you are actually affected, can motivate teams to do the right thing and integrate SCA scans in their CI pipelines. 


## Interviews

Don't forget that your audience/clients are platform teams, developers, testers, product owners. Don't choose a solution without consulting with them. Do you have a security champions team? Use them to get feedback about the final candidate tools you are considering. This not only helps you make the right choise, but it also increases the chances of adoption of the security tool you pick. 