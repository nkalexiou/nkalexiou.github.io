---
layout: post
title:  "Security? I develop an internal application!"
date:   2022-06-22 19:00:00 +0300
categories: cybersecurity
---

More often that I wish, I need to talk about application security with people who are really sceptical about my role as an application security professional. They behave like it is a waste of time to talk security with me and they would rather go about their regular business. But the most usual and frustrating argument I get is that their application is intended for internal use only, or even worse "trusted parties identified by strong authentication" will be using it. In a way they politely ask me to do business elsewhere where more critical bugs might exist in their opinion. I was suprised to hear this argument even from people working with systems managing sensitive or financial information. These scepticals as I have decided to call them say: "Why would anyone bother hack the application if they already are authorized to move money or see sensitive information? They can simply steal it!"

For us working in security it can be frustrating, or even infuriating, to work with people unwilling to make security a priority. But the reason behind reluclantness is not laziness, but a rather simpler reason: they are missing a big chunk of the picture that you as a security specialist see. Or maybe even fear that security will totally hijack their plan to release a new feature. So instead of being upset I decided to create a list of arguments:


## #1 The average time to identify a breach in 2020 was 228 days

That means hackers have much more time than the usual security tester to find bugs and holes in critical systems or any other system that might serve as a stepping stone. So they have the way to act as if they were legitimate system users, learn about your application, read the documentation, play around in the test environment and possibly utilize that bug noone knew about to go around authorization controls and move money to an offshore account.


## #2 The argument is actually broken

If a system user or admin can move customer money or copy huge amounts of sensitive data, then something is not designed properly. Without going into details: two pairs of eyes rule should be applied to financial transactions. Additionally, no single employee should have access to mass sensitive data, but only to what is needed to do work, after the authorization and usually even consent is given. So if a system user can somehow steal money or read sensitive data without authorization, then you as a security professional have a lot to talk about!


## #3 Security is everybody's job

Most applications in a corporate environment are operating in coordination with other applications and systems. Any application's security relies heavily on how secure are other applications of the corporate ecosystem. Besides that it is pretty sure that any applicable laws and requirements also apply to the scepticals.


## #4 I know what overloading feels like

Talk with the scepticals and reassure them that your job as a security professional is not to overload people. You know yourself how bad that is because security teams are outnumbered. Explain that this is why you have a list of activities, such as threat modelling and a SAST scanner, that apply to all development teams and that by following these activities you do not only minimize the risk of a real incident, but also the cost of incidents and bugs found late in the process.

Try to win people by arguments and explain how important security is for everyone. It is OK that they do not understand what to do initially. It is part of our job to make them realize that a change in behaviour is for the best.