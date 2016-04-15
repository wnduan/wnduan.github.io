---
layout: post
comments: true
title: "利用MathJax在Jekyll站点中显示公式"
description: ""
category:
tags: [jekyll, kramdown, MathJax]
---

在学习 [kramdown](http://kramdown.gettalong.org/index.html) 的过程中，发现他可以支持 LaTeX 数学公式的显示，对于科技写作来说这无疑是一大福音。所以，迫不及待的尝试一下显示效果。可是，按照 kramdown 和 LaTeX 的语法规则编写了数学公式却完全没有显示。

> Update(2016-04)  
> 实际上就是还需要设置 MathJax 的支持。相关设置见下面的内容。

为此，网上搜索一下还是发现了几个相关内容的介绍：

**Yukang's Page** ：[http://cyukang.com/2013/03/03/try-mathjax.html](http://cyukang.com/2013/03/03/try-mathjax.html) （一个中文的站点，方法靠谱）

**stackoverflow.com** ：[http://stackoverflow.com/questions/10987992/using-mathjax-with-jekyll](http://cyukang.com/2013/03/03/try-mathjax.html)  （这个页面上有相关的讨论，其中13楼和4楼都叙述了相同的方法。）

参考上面的内容，其实很好实现，只需要在站点的 ~~`_layouts/default`~~ `_includes/head.html` 文件中添加下面的代码：

{% highlight html %}
<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>
{% endhighlight %}

这样就可以了。比如现在，就可以轻松的实现下面的公式：

$$ e^{\pi i} + 1 = 0 $$

效果不错。

#### Update
一两年前写的东西了，现在回过头来看发现有些错漏。现在都已经更正过了。这里再多写几句，详细解释一下上面的用法。

Jekyll 在生成静态站点的时候，会将 `_layout` 文件夹中相应的布局文件，按照文件头部 YAML 语法的声明套用在页面上。举个例子，比如站点的主页就是 `index.html` 可以看到这个文件头布声明为 `layout: default`，因此在生成主页的时候，就会将 `_layout/default.html` 中的布局套用过来。同样， `_posts` 路径下的文章，一般头部都声明为 `layout: post`，因此在生成文章页面的时候，就会套用 `_layout/post.html`。

Jekyll 的第二个特点是，它支持使用 Liquid 语言。引入 Liquid 语言有几个好处，包括：可以定义和使用变量、可以在文件中包含别的文件、以及可以使用逻辑和循环语法。其实这几个功能都可以大大简化我们对站点的设置过程。这里要说的就是第二点，可以在文件中包含别的文件。

可以看到，在 `default.html` 中有：

{% raw %}
```Liquid
{% include head.html %}
{% include header.html %}
{% include footer.html %}
```
{% endraw %}

这些就是用 Liquid 语言将 `head.html`, `header.html`, `footer.html` 包含到 `default.html` 文件中的用法。Jekyll 生成站点时，{% raw %}`{% include <filename> %}`{% endraw %} 语句会在 `_includes` 文件夹中查找到被包含的文件，然后将其插入的相应的位置。

回到本文讨论的内容，如何为网页添加 MathJax 显示数学公式。[MathJax Getting Start](http://docs.mathjax.org/en/latest/start.html) 告诉我们，只需要将前面提到的那段 html 代码放到需要使用 MathJax 生成公式的页面的 `<head></head>` 标签内，这样在浏览器加载页面时就会通过 MathJax 的 CDN 将公式显示出来。

Jekyll 自动创建的站点的所有页面都是基于 `_layout/default.html` 生成的，包括我们需要显示公式的文章页面。而 `default.html` 会使用 `{% include head.html %}` 引入 `_includes/head.html` 的内容。

因此，基于以上描述，只要将 MathJax 提供的代码片段放在 `_includes/head.html` 中即可。

啰里八嗦了一大堆，不过也算把机理解释了一下。希望描述的还算清楚。
