---
layout: post
title:  "使用Github Pages搭建Blog"
categories: Tools
tags:  github blog
author: sunlei
---

* content
{:toc}


一个简单的方式来搭建个人博客，作为个人笔记类应用。github pages的使用非常简单，不需要太多html基础。



## Github Pages 

这里默认你有一个github账号，例如lei。在github上新建一个respository, 命名方式必须为username.github.io，这里我们即使用lei.github.io，搭建完成后，https://username.github.io 就是你的个人博客地址。

可以通过很多种方式来建立自己的博客，这里推荐使用Jekyll+markdown。

## Jekyll的安装和使用

~~~bash
gem install jekyll 
gem install jekyll-sitemap
gem install jekyll-paginate
gem install rouge
~~~

对于只想搭一个简单的博客自己做笔记的人来说，其实只需要一个能够比较方便的写的地方，而不用管html，css等样式设计等。Github中有很多使用Jekyll+markdown实现的方案，可以直接fork复用，对于毫无html基础的人来说，基本的使用不难。以[Lei's Blog](https://github.com/allyLei/allyLei.github.io)为例子，直接fork。

想在本地看到效果，可以直接运行`jekyll serve`，然后打开 http://localhost:4000 即可。也可以访问 https://username.github.io 看到github pages的效果。

## Markdown + Latex

对于做机器学习的人来说，latex公式基本不可避免，为了更好地兼容latex公式，需要添加如下行到head.html中：

~~~html
<script type="text/x-mathjax-config">
    MathJax.Hub.Config({ TeX: { equationNumbers: { autoNumber: "all" } } });
</script>

<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript">
</script>
~~~

注意，在markdown中写latex公式需要以两个美元符号起始和结束。


## Markdown + Highlight

实现代码高亮，首先在_config.yml中添加：

~~~yaml
highlighter: rouge
markdown: kramdown
kramdown: 
    input: GFM
    syntax_highlighter: rouge
~~~

Github一直比较推荐Backtick风格的代码块高亮，可以通过rouge附带的rougify工具生成不同高亮主题的css，然后添加到html中即可。

首先安装rouge，然后生成对应主题的css文件。可以在[官网](http://rouge.jneen.net/)上选择一个适合的配色方案。

~~~shell
gem install rouge
rougify style monokai.sublime > css/syntax.css
~~~

将这个css文件放到html中即可。

~~~html
<link rel="stylesheet" href="/css/syntax.css">
~~~

如果需要修改代码块的背景颜色的话，修改syntax.css中的背景配色即可。Sublime的背景色为#272822，Atom的背景色为#282C34.

~~~html
pre[class='highlight'] {background-color:#272822;}
~~~

在markdown中编写代码块时候，需要以三个~为开头+language, 再以三个~为结尾。中间为代码块。language对照表可以参见[这里](https://github.com/jneen/rouge/wiki/List-of-supported-languages-and-lexers)。


## Github pages中添加Comments

添加Comments可以通过disqus来实现，参考[这篇](http://www.forestofhorizon.com/notesofstudy/2015/12/01/adding-disqus-to-github-pages/)来实现。但是貌似国内访问有一些问题，并且这个blog是为了个人笔记用途，故没有加入这个功能。




