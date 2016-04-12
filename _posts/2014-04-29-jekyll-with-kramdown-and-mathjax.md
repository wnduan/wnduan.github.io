---
layout: post
title: "利用MathJax和kramdown在Jekyll站点中显示公式"
description: ""
category:
tags: [jekyll, kramdown, MathJax]
---

在学习kramdown的过程中，很高兴的看到他可以支持LaTeX数学公式的显示，对于科技写作来说这无疑是一大福音。所以，迫不及待的尝试一下显示效果。可是，按照kramdown和LaTeX的语法规则编写了数学公式却完全没有显示。这真是一个巨大的打击。

为此，网上搜索一下还是发现了几个相关内容的介绍：

**Yukang's Page** ：[http://cyukang.com/2013/03/03/try-mathjax.html](http://cyukang.com/2013/03/03/try-mathjax.html) （一个中文的站点，方法靠谱）

**stackoverflow.com** ：[http://stackoverflow.com/questions/10987992/using-mathjax-with-jekyll](http://cyukang.com/2013/03/03/try-mathjax.html)  （这个页面上有相关的讨论，其中13楼和4楼都叙述了相同的方法。）

参考上面的内容，其实，很好实现，只需要在站点的 `_layouts/default.html` 文件中添加下面的代码：

{% highlight html %}
<script type="text/javascript"
  src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
{% endhighlight %}

这样就可以了。解决了这个问题觉得很开心，所以赶快写个post分享一下。

比如现在，就可以轻松的实现下面的公式：

$$ e^{\pi i} + 1 = 0 $$

效果不错。
