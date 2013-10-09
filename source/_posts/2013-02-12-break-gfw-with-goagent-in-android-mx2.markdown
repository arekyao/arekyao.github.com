---
layout: post
title: "Break GFW with Goagent in Android MX2"
date: 2013-02-12 15:36
comments: true
categories: TechChangeWorld
tags: [MX2,DIY]
---

A new phone [MX2](http://www.meizu.com/products/mx2fun.html) goes into my life.

with a problem like before.

Facebook, twitter, Youtube are blocked by fucking GFW.

Here is a solution to kick that out of your way.

1, Root your phone
----------

MX2 have a pretty function, which give you easy way to root.

<!-- more -->

Just go into Account Menu, and click "you wanna root", then reboot your phone.

After the phone restart, it's rooted.

However, it has a bug that will give your alert "will you give the App root Authority" again and again,

unless you are dead or your phone.

2, Install SuperUser
-------------

It is used to get the right of root Authority Publishing from MX2 Flyme system, to solve the problem we referred above.

Better to download the App from Google Play, ( NOT From MX shop, Wandoujia ).

After installing, update the SuperUser in infomation Tab,

it will Make some configuration automatically.


3, Install GoAgent
---------------
[Goagent](https://code.google.com/p/goagent/), a open source sofeware, using Google App id's function to give a tunnel to break GFW.  

Every day ,it can offer 1G per app id, and every Email account can have 10 appids.

Goagent's design is very good, Language used is python, so you can ignore the system, whatever your PC is based on Windows, Linux, Mac, etc..

    apply an account on google appid
    download the goagent
    make some configuration and upload the server part
    make some configuration and start the proxy.py.
    then
    it works in PC, but not in phone.

4, Install GAE-proxy
-----------------

Again better to download the App from Google Play, ( NOT From MX shop, Wandoujia ).

MX shop and other offical shop can give you the App, which https function have some bugs......

Facebook:

    Facebook will identify your ID and phone, by give some Friends' Photo and let you mark it.

    Maybe it's suspicious by Facebook's spam checking program, because so many facebook id from same ip gived by Google. 

Facebook and Twitter can work on Spec App GAE mode, Youtube only works on ALL App GAE mode.

5, Everything goes fine
------------------

That's all.

Any Problem, you can Solve it by Google.





