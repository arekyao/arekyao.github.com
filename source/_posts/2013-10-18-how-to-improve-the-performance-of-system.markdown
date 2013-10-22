---
layout: post
title: "How to improve the Performance of System"
date: 2013-10-18 18:04
comments: true
categories: Architecture
tags: [performance, system, webserver]
---

1,Preface
-------

Happy families are all alike; every unhappy family is unhappy in its own way.  
So does System.

Here is just a simple way to find out what's 'unhappy' in your system.


2,Think as time goes
-------------

There are lots of ways to thinking and find something.  
Thinking as time goes is an easy one.

However, what we are going to talking, in fact, is thinking as data flows.

<!-- more -->

Refer to your system, the data will be transferred like this:

UserRequest -> HttpServer -> UIServer -> DataServer -> Database  
UserReceiver <- HttpServer <- UIServer <- DataServer <- Database


3,UserRequest & UserReceiver
------------

Those belong to User Client.

User's Client depends on Environment, where user run his program, like Operating System, like web browser if the program is an webapp, etc.

If you are doing a stress testing on your system, your stress test tools is a factor ,too.

4,Server Machine
-------------

Others like httpserver,uiserver, database, etc, are running on your server, your machine.
Server Machine can have four kind of bottleneck.

* CPU (see top)
* Swap (see free)
* Network (see iftop)
* Disk (see iotop)

> Where is the bottleneck?" is a good question, so I'll ask it back to you in different words: 
> is the CPU maxed out (see top)? 
> Are the disks struggling (iotop)? 
> Are you hitting swap (free)? 
> Are you filling the network pipe (iftop)? 
>                           –  Shish Nov 23 '11 at 11:21


The posts, which will give the detail on User & Machine, is on the way.












