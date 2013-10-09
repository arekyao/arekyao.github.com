---
layout: post
title: "How to Add Category list to you octopress blog"
date: 2013-09-09 22:22
comments: true
categories: TechChangeWorld
tags: [octopress,blogger]
---


###1,generate category list

save code to the file plugins/category_list_tag.rb

{% highlight ruby %}

module Jekyll
  class CategoryListTag < Liquid::Tag
    def render(context)
      html = ""
      categories = context.registers[:site].categories.keys
      categories.sort.each do |category|
        posts_in_category = context.registers[:site].categories[category].size
        category_dir = context.registers[:site].config['category_dir']
        category_url = File.join(category_dir, category.gsub(/_|\P{Word}/, '-').gsub(/-{2,}/, '-').downcase)
        html << "<li class='category'><a href='/#{category_url}/'>#{category} (#{posts_in_category})</a></li>\n"
      end
      html
    end
  end
end

Liquid::Template.register_tag('category_list', Jekyll::CategoryListTag)

{% endhighlight %}

This plugin will register a tag『in xml 』 called catetory_list in liquid.  
All your category in you blog, will be organised in li.  


###2,add aside

copy those code to source/_includes/asides/category_list.html

del the '\\'

```xml
<section>
  <h1>Categories</h1>
  <ul id="categories">
    {\% category_list %}
  </ul>
</section>
```

then modify config file _config.yml:

```
default_asides: [asides/category_list.html, asides/recent_posts.html]

```

or include the category_list.html in some file




