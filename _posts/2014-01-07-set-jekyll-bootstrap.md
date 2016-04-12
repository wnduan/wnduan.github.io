---
layout: post
title: "配置 Jekyll Bootstrap"
description: "建立站点的过程"
category:
tags: [jekyll-bootstrap, jekyll, github]
---

几经周折，总算有个雏形了，也走了不少弯路，在Windows下配置 Jekyll Bootstrap 的博客内容似乎不多。在这里记录一下我碰到的部分问题，希望可以帮到有需要的人，当然，也作为一个备忘，避免以后再犯同样的错误。

## 建立 ## {#h1}

关于Jekyll的部分这里先略过。用 [GitHub](https://github.com/) 建立自己的页面十分方便，日后也很好打理。而 [Jekyll Bootstrap](http://jekyllbootstrap.com/) 恰好提供了在GitHub上创建站点的便利。
根据 Jekyll Bootstrap (JB) 官网的步骤即可（当然这之前需要先拥有一个GitHub帐号）：

**第一步**，在GitHub上创建一个Repository，命名为：**用户名.github.com**

**第二步**，在本地安装 Jekyll Bootstrap，实际就是利用GitHub将远程的项目克隆（复制）到本地，再从本地同步到你自己的Repository，定位到相应的本地文件夹，执行如下命令：

```bash
# 1.从GitHub将项目复制到本地
$ git clone https://github.com/plusjade/jekyll-bootstrap.git USERNAME.github.com

# 2.进入USERNAME.github.com文件夹
$ cd USERNAME.github.com

# 3.定位origin的地址到用户的Repository
$ git remote set-url origin git@github.com:USERNAME/USERNAME.github.com.git

# 4.将本地的文件推送到GitHub上
$ git push origin master  
```

我个人按照上面的步骤执行到第4步时会报错：

> fatal: Could not read from remote repository.

网上查了一下解决方法可以执行 `$ git remote add origin git@github.com:UserName/UserName.github.com.git`

但执行上面这句又碰到问题：

> fatal: remote origin already exists.

这时候需用语句 `$ git remote rm origin ` 来解决。然后再输入上面的`$ git remote add origin git@github.com:UserName/UserName.github.com.git` 之后执行最后一步 `git push origin master` 就没问题了。

---

### 关于上面出错的重新认识

按照[Jekyll Bootstrap](http://jekyllbootstrap.com/)网站的指南，我是用 GitBash 来进行以上设置的。但是，我最开始使用 GitHub 时，按照官网的意见，使用了 *GitHub for Windows* 来管理我的仓库。所以，实际上我一直以来并没有设置 GitBash 的相关`config`内容，也没有为 GitBash 设置相应的 `SSH-key` 以及 `SSH-agent` 。因此，在我的这种状况下，只能从 *GitHub for Windows* 客户端来启动 GitBush，这样客户端会提供相应的 SSH 代理，就不会发生上述的问题了。

(2014-05-07 修订)

---

## 写文章 ## {#h2}

还是定位在 UserName.github.com 文件夹中，执行：

```bash
$ rake post title="Hello World"
```

就会自动在 UserName.github.com/\_post 中建一个按照正确命名规则命名，并且设置好 YAML 文件头的文件，剩下的事情就是用 Markdown 语言写文章就ok了。

## 创建页面 ## {#h3}

同上，执行下面的命令，即可创建一个about页面

{% highlight bash %}
$ rake page name="about.md"
{% endhighlight %}

创建子页面：

{% highlight bash %}
$ rake page name="pages/about.md"
{% endhighlight %}

创建一个拥有好看的路径的页面：

{% highlight bash %}
$ rake page name="pages/about"
# 这种方式将会建立 ./pages/about/index.html 文件
{% endhighlight %}

## 发布 ## {#h4}

只需执行下述命令，将新的内容推送到GitHub的Repository中

{% highlight bash %}
$ git add .
$ git commit -m "Add new content"
$ git push origin master
{% endhighlight %}


## 初始页面的修改  ## {#h5}

这是我碰到的最棘手的问题，折腾了很久。开始还跑去_site文件夹里修改index.html文件。这是十分愚蠢的做法。实际上应该修改 UserName.github.com 中的index.md文件的相关内容即可，非常的简单！

---

经过上面的步骤，就可以在GitHub Page建立一个自己的Jekyll站点了，以后写写文章应该还是不错的，备份也比Wordpress方便。当然还有什么模板设置之类的内容还有待日后进一步的挖掘。
