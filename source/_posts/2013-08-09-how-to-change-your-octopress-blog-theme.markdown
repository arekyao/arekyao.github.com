---
layout: post
title: "How to change your octopress blog theme"
date: 2013-08-09 22:51
comments: true
categories: TechChangeWorld
tags: [octopress,blogger,theme]
---

###Get the theme

There are 3rd Party Octopress Themes in :  
https://github.com/imathis/octopress/wiki/3rd-Party-Octopress-Themes

I pretty love FoxSlide and Reynard, but the reynard does not work in my octopress with some unknown problme.  
So I forked FoxSlide and make one by myself.

Here is my product:  
https://github.com/arekyao/everyday


###Install the theme

<!-- more -->

```sh
$ cd yourOctopress
$ git clone https://github.com/arekyao/everyday .themes/everyday
$ rake install['everyday']
$ rake generate

```

'rake generate' is very important and you would face lots of problems without it.



