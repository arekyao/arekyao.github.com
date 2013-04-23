---
layout: post
title: "How to export video from IP4s"
date: 2013-01-20 18:39
comments: true
categories: TechChangeWorld 
---


Recently, a Mac Book Air comes to my life.
As the same as before, a problem comes with it.

First, pls let me introduce some backgrounds.

The Mission:
========

There are so many Videos, taken by IP4s, which cost too much space on phone.
So I decide to copy them from my phone.

That needs these steps:

1, Copy them from phone
---------

Option:

    ifuncbox, interface good,but it lost the info including "modify time,create time,etc."
    phoneview, works good, but it need money, $30 , too much for me. 
    itools,  can not work.
    ...

    at last the simple application, iphoto, works!

2, Rename them
----------

The PM give a MRD, that asks for the name should contain video's taken time.
Here is the script:


``` 
#!/bin/bash

for i in `ls *.MOV`;
do
    echo $i;
    stat $i;
    sec=`stat -t "%s" $i | awk  '{print $12}' | sed 's/"//g'`
    name_2=`date -r "$sec" "+%Y%m%d_%H%M%S"`
    name_1=`echo $i | sed 's/.MOV//'`
    newname=`echo $name_1"_"$name_2".MOV"`
    cp $i output/$newname
done
```

3, Convert and Compress
-----------------

Option

    Xilisoft Video Converter Ultimate.

    Change some args, like Bitrate,Frame Rate,Video codec, etc.

    

That's all, Everything works good!
-----------------
    





