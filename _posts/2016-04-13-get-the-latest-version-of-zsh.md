---
layout: post
title: "Mac 中使用最新版的 zsh"
date: 2016-04-13 15:17:11 +0800
description: ""
categories:
tags: [Mac, OS X, zsh, brew]
---

在使用了一段时间系统自带的 Terminal+bash 之后，打算换成 iTerm2+zsh+oh my sh 的组合试试看。Mac 的 OS X 系统自带了 zsh 命令行工具，但不是最新版本的。为了能使用更新的版本，可以用 Homebrew 的 brew 命令来安装。然后还需要做一些必要的配置。本文记录一下安装的过程。

## 确认基本信息
在正式安装之前，先确认一下版本信息。在 shell 中运行下面的命令：

```shell
# 查看系统自带 zsh 的版本
$ zsh --version
zsh 5.0.8 (x86_64-apple-darwin15.0)
# 查看系统 zsh 的位置
$ which zsh
/bin/zsh
# 查看当前使用的 shell
$ echo $SHELL
/bin/bash
# 查看 Homebrew 的 zsh 版本
$ brew info zsh
zsh: stable 5.2 (bottled), HEAD
```

可以看到，我现在电脑上的版本是 `5.0.8`，最新的稳定发行版是 `5.2`。系统的 zsh 安装路径为 `/bin/zsh`。我现在使用的 shell 还是系统默认的 bash。（马上就可以用到功能更丰富的 zsh 了）

## 安装
用 brew 安装和管理软件非常方便。zsh 的安装如下:

```shell
$ brew install zsh
```

然后，brew 就会开始安装过程。首先，会安装依赖文件，然后安装 zsh。具体过程截图记录如下：

![Install zsh](/images/zsh-install.png)

brew 会自动完成一些必要的设置。比如现在我们用

```shell
$ which zsh
/usr/local/bin/zsh
```
可以看到，路径已经从 `/bin/zsh` 变为了 `/usr/local/bin/zsh`。`/usr/local` 是苹果提供给 OS X 用户安装软件包的路径。

### 安装路径
上面已经显示了 brew 的路径。我们还可以查看更详细的路径信息。列出路径下所有 `zs` 开头的文件：

```shell
$ ls -al /usr/local/bin/zs*
```

然后，也可以用 brew 确认安装包的细节：

```shell
$ brew list zsh
```
这个命令会返回所有相关的路径。

## 配置
不过现在，zsh 还不是系统默认使用的 shell。这个可以用命令进行如下设置：

```shell
$ sudo dscl . -creat /Users/$USER UserShell /usr/local/bin/zsh
```
然后输入密码，完成设置。也可以在系统的“Preference -> Users & Groups -> Advanced Options”(按住 `contrlo` 点击 Users & Groups 就可以打开 Advanced Options 选项）里面指定用户使用的 shell。完成以上操作之后，重启一下 Terminal 软件，以使设置生效。

现在，如果在执行前面的命令：

```shell
$ which zsh
/usr/local/bin/zsh
$ zsh --version
zsh 5.2 (x86_64-apple-darwin15.4.0)
```

可以看到，我们的设置都已经生效了。安装好之后第一次启动会弹出一个界面提示用户进行配置。zsh 的用户设置，都保存在 `~/.zshrc` 的文件中。在第一次启动的时候这个文件还不存在。如果按照提示进行了设置，就会生成这个文件。但是设置过程还是比较复杂的，为了简化这个过程，已经有人做好了基本的设置工作，这就是我们下面要介绍的 Oh My Zsh。

## Oh My Zsh
[Oh My Zsh](http://ohmyz.sh) 就是方便用户管理 Zsh 设置的一个框架。包含了各种有用的功能、帮助、插件、主题等。也可以直接访问他们的 Github 页面：[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)。

### 安装 Oh My Zsh
安装非常容易，基本上按照页面的安装过程来就可以了。首先，如果电脑上已经安装了 curl 或 wget，那么就非常简单，一切都自动搞定，只需要：

#### curl


```shell
$ sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```


#### wget


```shell
$ sh -c "$(wget https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh -O -)"
```

如果没有上述工具也没问题，可以用 Git 或直接下载获取，这里以 Git 为例：

```shell
# 从GitHub下载文件仓库至本地的 ~/.oh-my-zsh 路径下
$ git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
# 拷贝 Oh My Zsh 的配置文件 .zshrc 到用户文件夹下
# 注意，拷贝之前，可能需要先备份原有的（如果有的话）.zshrc 设置文件
$ cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```

### 必要的设置
Oh My Zsh 已经帮我们完成了大部分设置。但是，我们还是可以根据自己的需要，添加插件或使用的主题风格。以下设置通过修改 `~/.zshrc` 文件来实现。

#### 添加插件
这部分我还没进行设置。不过官方给出了最基本的方法。非常简单，在 `~/.zshrc` 文件中，找到 `plugins=` 的字段，在其后括号中添上要启动的插件名即可，如：

```
plugins=(git bundler osx rake ruby)
```
各个插件的作用可以去 GitHub 的项目页面上去查看。

#### 设置主题
主题就是 theme，控制页面的显示风格。主题的设置在 `~/.zshrc` 文件中的 `ZSH_THEME=` 字段下设置。默认的主题是 `ZSH_THEME="robbyrussell"`。不过现在比较流行的是 `agnoster` 主题。要使用这个主题只需要将 `~/.zshrc` 改为：

```
ZSH_THEME="agnoster"
```
我改过之后，发现基本都 ok 了，但有些字符还显示不出来。看了一下 [agnoster](https://gist.github.com/agnoster/3712874) 的说明，是需要 [Powerline fonts](https://github.com/powerline/fonts#powerline-fonts) 的支持。

#### 使用 Powerline fonts
这个字体集合中的字体可以帮助我们显示出一些特殊效果的符号。获取和安装只需下面两步：

```shell
# 下载含有字体文件和安装脚本的文件夹
$ git clone https://github.com/powerline/fonts#powerline-fonts
# 进入下载到本地的文件夹
$ cd fonts
# 运行脚本文件，安装字体
$ ./install.sh
```

之后在 iTerm2 中，“Preference -> Profiles -> Text” 标签中，将两个字体都设置为 [Powerline fonts](https://github.com/powerline/fonts#powerline-fonts) 列表中的一种字体。我现在用的是 Roboto Mono for Powerline，效果还不错。

#### 最后一点修饰
现在的显示和 GitHub 上的图片的效果基本一致了，不过还有一个小问题，就是提示符的最前面还有 `user@host` 的字符，比如我的电脑就是 `dwn@dwn`。这个还是挺影响美观的，而且分散注意力。可以通过简单的设置，使其不显示。只要在 `~/.zshrc` 文件中添加 `DEFAULT_USER=` 字段，将该字段设为用户名即可：

```
DEFAULT_USER="user"
```

可以在 shell 中用 `whoami` 命令，来查看需要填写的用户名。

另外，agnoster 主题的下面，建议使用 Solarize Dark 主题风格。所以应该为 iTerm2 配置建议的主题。

以上就是一个很粗略的配置过程。简单记录一下。以后有什么新的相关内容再来更新和完善。
