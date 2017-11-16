---
layout: post
comments: true
title: "为 OpenWrt 路由器设置免密码登录的 SSH 连接"
date: 2016-11-04 08:53:53 +0800
description: ""
categories: 
tags: [openwrt,ssh]
---

官方的 OpenWrt 系统可以通过 SSH 连接登录。但是每次登陆都输入一遍密码也很不方便。SSH 其实本来就可以通过在服务器端保存本地的公钥，实现免密码登录。配置好之后还可以在路由器上禁用密码登录选项，有助于提高安全性。

通常 Linux 服务器的话只要在本地用：

```bash
$ ssh-copy-id user@host
```

就可以自动把公钥传输到远程主机上了。该命令实际上就是把公钥文件的内容附加到了远程主机的 `authorized_keys` 文件中，通常该文件路径为：`~/.ssh/authorized_keys`。

但是这个命令不能用来配置 OpenWrt 系统的 SSH 登录。因为 OpenWrt 中使用的 SSH 服务是 Dropbear。其配置文件的路径在 `/etc/dropbear` 下。所以只要把本地的公钥添加到 `/etc/dropbear/authorized_keys` 中就可以了。[官网](https://wiki.openwrt.org/oldwiki/dropbearpublickeyauthenticationhowto)也对此做了详尽的描述。

我也基本参照官网的操作，具体如下：

#### 1. 生成密钥对

本地电脑的密钥对保存在 `~/.ssh` 路径下，可以输入下面的命令：

```bash
$ ls ~/.ssh
```

如果输出的结果里已经有 `id_rsa` 和 `id_rsa.pub` 这对文件，就说明已经有可用的密钥对了。其中以 `.pub` 结尾的就是公钥，另一个是私钥，私钥是非常重要隐私的只能自己拥有的密钥，不可泄露给他人。

如果没有这俩文件，或者想另外生成新的文件专门给连接路由器使用，可以用 SSH 工具生成一个新的密钥对：

```bash
# 按默认设置生成一对 rsa 密钥
$ ssh-keygen   
# 或者是，生成名为 my_rsa 的密钥对
$ ssh-keygen -f ~/.ssh/my_rsa  
```

`ssh-keygen` 更多高级用法还是直接去查 man 页面吧。

#### 2. 把公钥添加到 `authorized_keys` 文件

在本地电脑执行：

```bash
$ cat ~/.ssh/id_rsa.pub | ssh root@192.168.1.1 'cat >> /etc/dropbear/authorized_keys'
```

#### 3. 设置文件权限

```bash
$ ssh root@192.168.1.1 "chmod 0600 /etc/dropbear/authorized_keys"
```

这样以后就可以用 `ssh root@192.168.1.1` 直接登录到路由器而不必输入密码了。

#### 4. 设置 ssh 的 config 文件

每次连接路由器要输入 `ssh root@192.168.1.1` 其实还是挺烦的，可以在本地的 ssh配置文件中设置路由器的 host 信息，以后只要用这个主机名连接就可以了。

用文本编辑器打开 `~/.ssh/config` 文件，如果没有就新建一个，然后添加如下信息：

```bash
Host my_host_name
    HostName 192.168.1.1
    User root
```

其中 `my_host_name` 就是自己为路由器起的一个名字，以后只需要

```bash
$ ssh my_host_name
```

就可以登录了。

`config` 文件还有很多其他设置，可以参考 `man` 页面的介绍。
