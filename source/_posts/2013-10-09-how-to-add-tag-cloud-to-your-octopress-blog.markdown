---
layout: post
title: "How to Add tag cloud to your Octopress blog"
date: 2013-10-09 12:21
comments: true
categories: TechChangeWorld
tags: [octopress,blogger,bug]
---


As same as adding "category list",there are so many ways leading to Roma.


Here is one of the solutions:

###1,git clone two repos

<!-- more -->

git clone https://github.com/robbyedwards/octopress-tag-pages  
git clone https://github.com/robbyedwards/octopress-tag-cloudclone  

The first one is used to generate the tag page. 
The latter one is for tag clound

###2,octopress-tag-pages

cp tag_generator.rb to /plugins  
cp tag_index.html to /source/_layouts  
cp tag_feed.xml to /source/_includes/custom/  

ps:the last one is not included in official document,but it's really needed

###3,octopress-tag-cloud

cp tag_cloud.rb to /plugins

cp those code below to /source/_includes/custom/asides/tags.html。

Actually, the similiar file tags.html is in octopress-tag-cloud

```xml
<section>
  <h1>Tags</h1>
  <ul class="tag-cloud">
    {\[del this \]% tag_cloud font-size: 90-210%, limit: 10, style: para %}
  </ul>
</section>
```

###4,add the block to somewhere

add some code in _config.xml's default_asides,such as:

default_asides: [asides/category_list.html, asides/recent_posts.html, custom/asides/tags.html]

###5,one bug of tag cloud

after doing before, there may be a problem or bug waiting for you.

when rake, the error is:

```
Liquid Exception: comparison of Array with Array failed in page
```

At first, I think there must be something wrong with my setting.

At last, I got some info from this:

> This would occur when every tag is applied for only ONCE. Not sure if this is a bug. 
> Anyway you can simply apply one of the tags on more than one post.

http://jeffli.me/blog/2013/06/17/add-tags-and-tag-cloud-support-to-octopress/

We get the bug away by a trick:

```
add same tag to three post before 'rake generate'
then rake, will be ok
after that, change tag to what supposed to be, little by little
```
ok, there is a use of TDD, lol










