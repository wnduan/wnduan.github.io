---
layout: post
comments: true
title: "在 Jekyll 中使用 MathJax 显示数学公式"
date: 2017-10-19 04:42:14 +0800
description: ""
categories: 
tags: [jekyll,mathjax,latex,math]
---

在 2014 年写过一篇文章：[利用 MathJax 在 Jekyll 站点中显示公式]({{site.baseurl}}{% post_url 2014-04-29-jekyll-with-kramdown-and-mathjax %})，当时就是记录了配置过程中遇到一些问题，实现了在 Jekyll 站点中显示数学公式的目的。结果弄好之后就再也没有正经写过技术类的文章，连一般的笔记类博文都很少写。

最近，发布新文章的时候发现了一个警告信息，说 [MathJax](https://www.mathjax.org/) 停止了 CDN 服务。于是去官网看，果然如此。不过对于对于用户来说影响不大，官网推荐了其他可用的 CDN 服务商，只要按照文档中新的配置信息修改一下就可以了。

这篇文章，就是按照新的官方文档把配置的方法再重写一遍。另外，增加一点关于 MathJax 的配置文件的内容。

### 在页面中使用 MathJax
直接在网页内链接至内容分发网络 (CDN) 就可以使用 MathJax 了，CDN 会安排用户的浏览器从最近的服务器下载 MathJax 的文件。MathJax 推荐的 CDN 服务是：[cdnjs.com](https://cdnjs.com/)。

将下面这段代码（注意，第二行很长）：
```html
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.2/MathJax.js?config=TeX-MML-AM_CHTML">
</script>
```
放在 HTML 文件的 `<head>` 块内即可，这样就可以通过 *cdnjx* 载入最新版本的 MathJax，从而支持识别 TeX, MathML, 及 AsciiMath 的数学标记，并最终生成可显示在网页页面中的数学公式和符号等。

对于一般的 Jekyll 站点来说，找到博客站点根目录下的 `_includes/head.html` 文件。文本编辑器打开之后把上面的代码添加到 `<head>` 和 `<\head>` 标签之间就可以了。比如下面 10--12 行这样：

{% highlight html linenos %}
<head>
  <meta charset="utf-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1">

  ...

  <!-- MathJax Support -->
  <!-- Use cdnjs as CDN providor -->
  <script type="text/javascript" async
    src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.2/MathJax.js?config=TeX-MML-AM_CHTML">
  </script>
</head>
{% endhighlight %}
上面这段代码中第 11 行中的 `config=TeX-MML-AM_CHTML`，指定了要加载的配置文件，这个配置文件可以支持 TeX, MathML, AsciiMath 语法的输入，并进行显示。关于配置文件的更多内容可以查看：[Combined Configurations](http://docs.mathjax.org/en/latest/config-files.html#common-configurations) 和 [Loading and Configuring MathJax](http://docs.mathjax.org/en/latest/configuration.html#loading)。

### 更完善的配置
现在就可以使用 TeX, MathML 或 AsciiMath 语法来编辑并在网页中显示数学公式了。一般我们会使用 TeX 语法来输入数学公式。在 TeX/LaTeX 中，有两种数学模式用于进行数学公式的输入和编辑：
- 第一种是「行内公式」(inline math)，也就是在夹杂在正文中的数学变量、公式等元素，在 TeX 中大部分情况下用 `$...$` 表示行内公式环境。
- 第二种是「陈列公式」(display math) 用于占据整行，并居中展示的数学公式。通常用 `\[...\]` 或 `$$...$$` 表示陈列公式，一般建议使用 `\[...\]` 的形式来编辑陈列公式。

MathJax 的「行内公式」用 `\(...\)` 表示，默认设置并不支持 `$...$` 这种形式。这是因为考虑到 `$` 符号在一般正文书写中使用频次较高。如果希望像在 TeX 中那样用 `$...$` 表示行内公式，可以在 `<head>` 标签内加入以下配置内容，第 3--7 行：
{% highlight html linenos %}
  <!-- MathJax Support -->
  <!-- Use '$' for inline math mode -->
  <script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    tex2jax: { 
      inlineMath: [ ['$','$'], ['\\(','\\)'] ],
      processEscapes: true
    }
  });
  </script>
  <!-- Use cdnjs as CDN provider -->
  <script type="text/javascript" async
    src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.2/MathJax.js?config=TeX-MML-AM_CHTML">
  </script>
</head>
{% endhighlight %}

陈列模式的公式则可以像 TeX 一样，既支持 `\[...\]` 的形式，也支持 `$$...$$` 的形式。

另外要**注意**的一点是，因为 `\` 符号在 Markdown 中，是转义符号，所以在 Markdown 中编辑数学公式时，应该用 `\\(...\\)` 和 `\\[...\\]` 分别表示行内公式和陈列公式。

### 使用示例
若随机变量 $X$ 服从一个位置参数为 $\mu$，尺度参数为 $\sigma$ 的概率分布，记为：
\\[
    X \sim N(\mu,\sigma^2),
\\]
则其概率密度函数为
\\[ 
    \int_{-\infty}^{\infty} \frac{1}{\sqrt{2\pi \sigma}} 
    \mathrm{e}^{-\frac{(x-\mu)^2}{2\sigma^2}} \,\mathrm{d}x = 1 
\\]

上面这段来自 Wikipedia 中文版中「[正态分布](https://zh.wikipedia.org/wiki/正态分布)」的词条，可以看到 TeX 的数学语法可以正确的处理积分符号、积分界限、上下标、根式、分式以及希腊字母等元素，并将其组织为复杂的数学结构表现出来。这段示例包括了行内公式和陈列公式。

#### 行内公式
上面示例中，行内公式只有几个变量名，其对应的 Markdown 写法如下：
```
若随机变量 $X$ 服从一个位置参数为 $\mu$，尺度参数为 $\sigma$ 的概率分布...
```
在写作的过程中，要注意数学变量都应该放在 `$...$` 或 `\\(...\\)` 内，常用的变量元素 $x$, $y$ 这些都应该注意正确的处理，不要偷懒写成 x, y。希腊字母都是用其英文名称表示，由第一个字母的大小写得到对应的大小写字母，如 $\delta$ (`$\delta$`)  和 $\Delta$ (`$\Delta$`)

#### 陈列公式
上面有两个陈列公式，$X \sim N(\mu, \sigma^2)$ 的代码如下：
```latex
\\[
    X \sim N(\mu,\sigma^2),
\\]
```
这里 `\sim` 是特殊符号 $\sim$，上标用 `^` 符号表示。这种简单的上标可以直接放在 `^` 符号后面，如果是比较复杂的上标，就需要用 `{}` 花括号进行分组，例如正态分布的概率密度函数：
\\[ 
    \int_{-\infty}^{\infty} \frac{1}{\sqrt{2\pi \sigma}} 
    \mathrm{e}^{-\frac{(x-\mu)^2}{2\sigma^2}} \,\mathrm{d}x = 1 
\\]
```latex
\\[ 
    \int_{-\infty}^{\infty} \frac{1}{\sqrt{2\pi \sigma}} 
    \mathrm{e}^{-\frac{(x-\mu)^2}{2\sigma^2}} \,\mathrm{d}x = 1 
\\]
```
这里 $\mathrm{e}$ 的指数部分 $-\frac{(x-\mu)^2}{2\sigma^2}$，整个是一个上标元素，因此用花括号将其作为一个整体放在上标符号 `^` 的后面。

- 积分符号 $\int$，用 `\int` 命令表示，其上下标也就是积分界限。下标用 `_` 表示，对于同时有上下标的元素一般都是先写下标再写上标，即 `\int_{下标}^{上标}`的形式。无穷符号的命令是 `\infty`；
- 分式结构的命令是 `\frac{分子}{分母}`；
- 根式结构的命令是 `\sqrt{}`，如果是多次根式还可以额外加一个参数 $n$，表示开方次数：`\sqrt[n]{}`；
- 对于需要使用正体的字母或文字，可以用 `\mathrm{}` 命令，比如这里的自然数 $\mathrm{e}$ 和微分符号 $\mathrm{d}$；
- 另外，这里用 `\,` 在 $\mathrm{d}x$ 和前面的部分之间增加了一点距离，使得公式的效果更加美观。

可以看出，通过各种基本元素和结构的组合及嵌套，就可以写出复杂的数学表达式。更多关于 LaTeX 数学语法和符号的内容可以参考更专门的文章。另外 MathJax 还提供了数学公式编号及引用等实用的功能，可以参考 [MathJax TeX and LaTeX Support](http://docs.mathjax.org/en/latest/tex.html)。

### MathJax 的配置文件
#### 预定义配置文件
前面已经提到，可以在加载 MathJax 时，用 `config=` 指定要加载的配置文件。MathJax 预定义的配置文件包括：

- `TeX-MML-AM_CHTML`
- `TeX-MML-AM_HTMLorMML`
- `TeX-MML-AM_SVG`
- `TeX-AMS-MML_HTMLorMML`
- `TeX-AMS_CHTML`
- `TeX-AMS_HTML`
- `TeX-AMS_SVG`

等。更多预定义的配置文件的可以在 [Combined Configurations](http://docs.mathjax.org/en/latest/config-files.html) 查看。

上面这些预定义文件命名规则是，`_` 之前的是支持的输入引擎，如 TeX, MML, AM 等；而 `_` 之后表示输出方式，如 CHTML (Common html), HTMLorMML, SVG, HTML 等。

如果只打算使用 TeX 语法输入数学公式，则输入引擎可以选择 `TeX-AMS` 系列的；输出方面，[`HTMLorMML`](http://docs.mathjax.org/en/latest/options/output-processors/MMLorHTML.html#configure-mmlorhtml) 从 MathJax 2.6 版本开始，已经不推荐使用。`CHTML` 不支持 `HTML-CSS` 选项，似乎无法自定义数学字体。所以，我目前使用的配置文件是：
```
TeX-AMS_HTML
```

也可以根据需要创建自定义的配置文件，本文不涉及此部分内容，可以参考 [Using a local configuration file with a CDN](http://docs.mathjax.org/en/latest/configuration.html#using-a-local-configuration-file-with-a-cdn)。

#### 配置选项
除了使用配置文件，也可以在网页内直接定义配置选项，例如前面定义 `$` 为行内公式标识符号那样。下面通过配置项，看看数学字体的定义。

**注意：**这些定义必须放在 MathJax 加载之前。

### 数学字体的选择
MathJax 可以加载网络字体，可以使用的字体包括：
- MathJax TeX (default): `TeX`
- STIX General: `STIX-Web`
- Asana Math: `Asana-Math`
- Neo Euler: `Neo-Euler`
- Gyre Pagella: `Gyre-Pagella`
- Gyre Termes: `Gyre-Termes`
- Latin Modern: `Latin-Modern`

这部分内容，我只做了简单的测试，但是是否是最优的方式还不确定。

在网页中要指定使用的字体，完整的配置形式如下：
{% highlight html linenos %}
<!-- MathJax Support -->
<!-- Personal Configuration -->
<!-- Aviable math fonts: "TeX","STIX","Asana-Math", "Gyre-Pagella", 
   "Gyre-Termes", "Latin-Modern", "Neo-Euler" -->
<script type="text/x-mathjax-config">
MathJax.Hub.Config({
  tex2jax: {
    inlineMath: [['$','$'], ['\\(', '\\)']],
    processEscapes: true
  },
  "HTML-CSS": {
    availableFonts: [],
    preferredFont: null,
    webFont: "STIX-Web",
    imageFont: null
  }
});
</script>
<!-- Use cdnjs as CDN providor -->
<script type="text/javascript" async
  src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.2/MathJax.js?config=TeX-AMS_HTML">
</script>
{% endhighlight %}

在 `webFont: ` 中指定字体名即可，可用的字体名见本节开头的列表，注意要加上引号。个人最喜欢还是 `STIX` 系列的，这个字体似乎是 `TeX-AMS_HTML` 配置文件默认采用的，所以其实不额外设置字体也是可以的，如果更偏好其他字体可以根据需要进行修改。关于字体设置的更多信息可参考：[MathJax Font Support](http://docs.mathjax.org/en/latest/font-support.html) 和 [The HTML-CSS output processor](http://docs.mathjax.org/en/latest/options/output-processors/HTML-CSS.html#the-html-css-output-processor)。

