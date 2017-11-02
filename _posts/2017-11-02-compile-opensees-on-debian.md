---
layout: post
comments: true
title: "在 Debian/Ubuntu 系统下编译 OpenSees"
date: 2017-11-02 23:57:41 +0800
description: ""
categories: 
tags: [debian, opensees, linux]
---

## 我的系统环境

- 服务器: Linode 的 VPS 服务器
- 系统: Linux (Debian 9)
- OpenSees 版本: 2.5.0 (Release Tag: 6236)

## 1. 获取源代码

### 1.1 安装 subversion
OpenSees 的源码使用 `SVN` 进行版本管理，相关信息可以前往 [OpenSees SVN](http://opensees.berkeley.edu/OpenSees/developer/svn.php) 查看。首先需要安装 `SVN` 工具，以获取用于编译的源代码。

Debian 下可以使用 [subversion](https://packages.debian.org/subversion)。执行下面的代码，安装 subversion。

```shell
$ sudo apt-get install subversion
```

### 1.2 使用 svn 获取源代码

以匿名方式获取最新版本的源码：
```shell
$ svn co svn://peera.berkeley.edu/usr/local/svn/OpenSees/trunk OpenSees
```

如果需要下载特定版本的源码，可以前往 [changeLog](http://peera.berkeley.edu/OpenSees/changeLog.php) 查看各个版本的发行编号 (release tag)，在 `svn co` 命令时添加 `-r` 参数指定发行编号即可，例如 version 2.4.2 版的 release tag 是 5540，则可以使用一下命令获取该版本的源码：
```shell
$ svn co -r 5540 svn://peera.berkeley.edu/usr/local/svn/OpenSees/trunk OpenSees
```

我们将 OpenSees 的源码下载到用户文件夹下，这里指定绝对路径来完成下载：
```shell
$ svn co svn://peera.berkeley.edu/usr/local/svn/OpenSees/trunk ~/OpenSees
```

可以看到获取的是 OpenSees Version 2.5.0 - Release Tag 6236 的版本，所以本文以下部分的描述基于该发行编号。

### 1.3 源码文件初探
之后 `cd ~/OpenSees` 进入文件夹，`ls`  查看一下文件，首先阅读 `README` 文件，这个文件简单介绍了编译的方法，以及源码的框架：
- `MAKES` 文件夹： 包含了 Makefile.def 的示例文件，我们可以从中拷贝一份放在 OpenSees 根目录下，并根据自己系统进行修改，更具体的信息在后面展开说明。
- `EXAMPLES` 文件夹：和开发相关的一些示例文件，一般用户不必深究。
- 其他的文件夹是软件的源码，以及一些相关的依赖库，不涉及开发工作的话，暂时不需要更深入的了解。

所以，需要关注的是 `MAKES` 文件夹下的文件，提供了一系列配置文件，其中有 `Makefile.def.EC2-DEBIAN`，但是该文件目前默认的配置是基于亚马逊 AWS EC2 服务器，用于编译支持并行计算的 OpenSeesMP 二进制文件的，但是我只打算生成普通的单机用的 OpenSees 二进制文件。所以需要对这个文件进行一些修改。后来，修改之后发现这里面一些编译命令还是有错误的，所以最终决定改用另一个配置文件： `Makefile.def.EC2-UBUNTU`。因为 UBUNTU 本来就是基于 Debian 的，而且这个文件明显简单许多，去掉了那些编译并行计算二进制文件的部分，正好符合需求。但是也需要对其中的一些变量进行编辑，下面会具体指出。

## 2. 安装依赖

需要的依赖主要有：
- make
- tcl8.5
- tcl8.5-dev
- gcc
- g++
- gfortran

所以用 Debian 的包管理工具执行：

```shell
$ sudo apt-get install make gcc g++ tcl8.5 tcl8.5-dev gfortran
```

就所有的依赖就安装齐全了，非常简单。对于已经有的包执行 `apt-get install` 也是没有任何问题的。

## 3. 修改配置文件

首先将配置文件复制到 OpenSees 的根目录，同时改名为 `Makefile.def`

```shell
$ cp ~/OpenSees/MAKES/Makefile.def.EC2-UBUNTU ~/OpenSees/Makefile.def
```

然后用编辑器打开文件，修改以下部分：

1. 这个是一个非常重要的变量必须设置，找到 `HOME` 变量，将 `user-name` 的部分改为自己的：
```
HOME = /home/user-name
```
2. 在 `# Msic` 标签下面这里添加一个新的变量，定义 `mkdir` 命令：
```
MKDIR = mkdir
```
3. 找到 `TCL_LIBRARY` 变量，这里需要确认一下 tcl 库文件的路径，可以用下面的命令确认这个路径：
```shell
$ sudo find / -name libtcl*.so
/usr/lib/x86_64-linux-gnu/libtcl8.5.so
```
将找到的路径填到这个变量后面：
```
TCL_LIBRARY = /usr/lib/x86_64-linux-gnu/libtcl8.5.so
```

#### 关于第 2 条的说明：

定义这个 `MKDIR = mkdir` 变量可以在 `make` 的时候自动创建以下两个文件夹：
```
/home/user-name/lib
/home/user-name/bin
```
也可以不定义这个变量，自己手动建立这个文件夹再开始下一步编译。

## 4. 编译

之后就可以编译了，在 OpenSees 源码文件夹的根目录下执行
```shell
$ make && make clean
```
就开始编译了。编译大约需要十来分钟的时间，反正会弹一大堆的 warning，不过没有关系，只要没有没有 error 的话，就可以完成编译的工作，编译好的二进制文件就在 `~/bin` 文件夹中。

这步如果遇到错误也可以尝试依据提示进行修改。

## 5. 之后的操作

现在可以通过：
```shell
$ ~/bin/OpenSees  # 注意：大小写敏感
```
来进入 OpenSees 交互模式的界面。这样过于复杂了，要在任意位置访问，可以将它加入系统 `PATH` 变量中。不过因为 OpenSees 更新也不是很频繁，一般也不存在同时使用多个版本的问题，所以我们可以直接把得到的二进制文件放到 `/usr/local/bin`，另外为了进一步偷懒将其重命名为 `opensees` 避免每次输入还得按 `Shift` 键，执行：
```shell
$ sudo cp ~/bin/OpenSees /usr/local/bin/opensees
```
以后就可以使用 `opensees` 来运行了。大体来说还是很简单的。
