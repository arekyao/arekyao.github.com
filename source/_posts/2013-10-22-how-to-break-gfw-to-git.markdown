---
layout: post
title: "How to break gfw to git"
date: 2013-10-22 14:39
comments: true
categories: TechChangeWorld
tags: [DIY,GFW,git,github,proxy,VPN]
---

Recently, github is blocked by Somebody.  
git clone,git push ... no way.  
So how to break the wall?

Here is a way.


Git Protocol
--------

Git has three protocol working:

* git://  
* ssh://  
* http(s)://  

and Proxy has two protocol mainly:

* http(s)://  
* sock  

I git successfully https:// with http-proxyer (mine is goAgent)

The way is so easy:

```
export http_proxy="http://127.0.0.1:8087"
export https_proxy="http://127.0.0.1:8087" 
```

However, i did not succ with ssh:// or git:// with http-proxyer.and still dunno why

VPN
----------

The other way is easier, VPN.

There are lots of vpn server, free and not free.

Here is a free vpn,enjoy it 

[http://www.pptpvpn.org/](http://www.pptpvpn.org/)





