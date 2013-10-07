---
layout: post
title: "Hello World!"
date: 2013-01-10 20:59
comments: true
math: true
categories: Github
---

It's my first blog on github.

At the moment,I decide to write my blog,TRY MY BEST,in English.

Maybe it's hard at first.
I believe it will give me a lot beyond my imagination after that.

<!-- more -->

How to build a blog on hithub
=====

1,  apply a account & some repo 
----------

    apply a id on github and a new repo named "yourid.github.com"


2,install ruby,[jekyll][],[markdown][],etc
-----------

    install rvm to manager ruby version,ruby 1.9 needed 
    gem install rdoc
    gem install jekyll
    gem install rdiscount or kramdown
    easy_install Pygments

this is typical structure in [jekyll][] frame

    |-- _config.yml
    |-- _includes
    |-- _layouts
    |   |-- default.html
    |   `-- post.html
    |-- _posts
    |   |-- 2007-10-29-why-every-programmer-should-play-nethack.textile
    |   `-- 2009-04-26-barcamp-boston-4-roundup.textile
    |-- _site
    `-- index.html

3,install [Octopress][]
-----------------

    $ rake new_post["title"]
    $ rake generate     
    $ rake watch       
    $ rake preview    

4, About Math Block
----------

[LaTeX](http://www.ctex.org/LaTeX) + [MathJax](http://www.mathjax.org/)

    LaTex is a document markup language and document preparation system for the TeX typesetting program

LaTex is just a format language, or just a standard. (not a lib or tool, just a language which doesn't need installed)    

    MathJax is a cross-browser JavaScript library that displays mathematical equations in web browser

MathJax is a library, even though, it does not need installed either. 

    The easiest way to use MathJax is to link directly to the MathJax distributed network service (see Using the MathJax CDN)
    http://docs.mathjax.org/en/latest/installation.html

LaTex Tools:
  [Online LaTeX Equation Editor](http://www.codecogs.com/latex/eqneditor.php)

    
ps : I am so stupid, try my best to download a lib, MathJax.... totally useless.. 

5, About Git
----------
  
  [http://rogerdudler.github.io/git-guide/index.zh.html]()
  
  [http://marklodato.github.io/visual-git-guide/index-en.html]()

###Reference:

* [像黑客一样写博客——Jekyll入门](http://www.cnblogs.com/TheGrandDesign/articles/2573282.html)
* [技术博客利器——Octopress](http://fancyoung.com/blog/octopress-study/)
* [公式编辑](http://liuhongjiang.github.com/tech/blog/2012/11/21/math/)


> * Ruby    
>    * [Jekyll][]
>    * [Bonsai](http://tinytree.info/) 一个非常简单（但实用）的小脚本
>    * [Webgen](http://webgen.rubyforge.org/) 一个较复杂的生成器
> * Python
>    * [Hyde](http://ringce.com/hyde) Jekyll的Python语言实现版本
>    * [Cyrax](http://pypi.python.org/pypi/cyrax) 使用Jinja2模板引擎的生成器
> * PHP
>    * [Phrozn](http://www.phrozn.info/) PHP语言实现的静态网站生成器
{: .information}


### My Test Table

|---
| Default aligned | Left aligned | Center aligned | Right aligned
|-|:-|:-:|-:
| First body part | Second cell | Third cell | fourth cell
| Second line |foo | **strong** | baz
| Third line |quux | baz | bar
{: .mytable}

### My Math Test


$$
\begin{align*}
  & \phi(x,y) = \phi \left(\sum_{i=1}^n x_ie_i, \sum_{j=1}^n y_je_j \right)
  = \sum_{i=1}^n \sum_{j=1}^n x_i y_j \phi(e_i, e_j) = \\
  & (x_1, \ldots, x_n) \left( \begin{array}{ccc}
      \phi(e_1, e_1) & \cdots & \phi(e_1, e_n) \\
      \vdots & \ddots & \vdots \\
      \phi(e_n, e_1) & \cdots & \phi(e_n, e_n)
    \end{array} \right)
  \left( \begin{array}{c}
      y_1 \\
      \vdots \\
      y_n
    \end{array} \right)
\end{align*}
$$



[jekyll]:    https://github.com/mreid/jekyll/    "Jekyll"
[markdown]: http://markdown.tw/  
[kramdown]: http://kramdown.rubyforge.org/index.html  
[octopress]: https://github.com/imathis/octopress






