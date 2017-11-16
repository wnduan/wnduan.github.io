---
layout: post
comments: true
title: "mint 包及 TeXShop 的相关设置"
date: 2017-06-22 14:25:25 +0800
description: ""
categories: 
tags: [latex,texshop,代码高亮]
---

使用 LaTeX 写作时，常常遇到的一个问题就是代码部分的格式化和语法高亮。最原始的做法是使用 `verbatim` 抄录环境，更加正式的方式是使用 `listings` 环境，在导言区使用 `usepackage{listings}`，然后经过设置就可以使用该环境来格式化和高亮代码，如：

```latex
\begin{lstlisting}
Put your code here.
\end{lstlisting}
```

或者可以直接导入整个代码文件，例如源代码保存在 `source_filename.py` 中，就可以用下面的语句导入整个文件：

```latex
\lstinputlisting{source_filename.py}
```

但是要使最终输出效果足够美观，那还是需要花一些功夫具体来设置的。这不是今天想说的主要内容，这个包我也没怎么用过。感觉上好处就是纯原生，功能也足够强大；缺点可能就是每次要设置比较麻烦。如果只是一般记个笔记之类没那么正式的文档，可能还是不需要这么复杂。

## mint 包

今天要介绍的是 mint 包，一般 TeX Live 的标准安装都自带这个包。这是一个基于 Pyhotn 的 pygments 来进行代码高亮和格式化的。基本不需要预先设置，只要指定语言的类别，就可以产生让人满意的高亮效果。支持的语言种类也相当丰富。一般的使用只要在导言区使用：

```latex
\usepackage{mint}
```

然后在需要代码的地方：

```latex
\begin{minted}[<some options here>]{<language name>}
  code block
\end{minted}
```

其中 `[]` 内填写的是一些选项，最后一个花括号 `{}` 中指定语言，比如我常用的设置，在代码外面加一个框，并且显示行号：

```latex
\begin{minted}[frame=single, linenos]{python}
```

除了上述这些也还有很多设置，具体可以参考官方文档的介绍。

## TeXShop 的相关设置

由于 mint 包要调用外部的 Python 资源，所以使用之前，先要保证电脑上安装了 Python 和 pygments。而在执行编译命令时，需要使用 `--shell-escape` 选项，方可完成，否则会出错。使用 TeX Shop 默认的几个默认编译引擎都会报错。其实可以自己写一个专门编译使用了 mint 包的文档的文件，保存在 `~/Library/TeXShop/Engines` 路径下，然后打开（或重启）TeXShop 就可以在编辑窗口最上面的「排版」按钮旁边的下拉菜单中选择我们自己编写的编译代码。我的代码如下：

```sh
#!/bin/tcsh

set path=($path /Users/dwn/anaconda/bin /usr/texbin /usr/local/bin)
latexmk -xelatex  -file-line-error -synctex=1 --shell-escape "$1"
```

要点是在 `path` 变量中加入 pygmentize 的路径，因为我的是在 anaconda 中，所以添加的是 anaconda 的路径，具体要根据自己的情况进行调整。还有一个就是执行编译的那句话，要添加 `--shell-escape` 选项。我这里是选择用 latexmk 来进行编译的。其实都很简单，以后有其他特殊需求的情况也可以像这样自己写编译的代码。
