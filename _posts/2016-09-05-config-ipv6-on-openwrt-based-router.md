---
layout: post
comments: true
title: "OpenWrt 路由器 IPv6 设置"
date: 2016-09-05 21:39:48 +0800
description: ""
categories: 
tags: []
---
## 0. 免责声明

* 本设置**非官方推荐**，只是本人的个人经验，希望能给需要的同学一个启发。
* 有一定技术基础，喜欢折腾的可以参考。零基础的，还没掌握官方教程基本操作的**不适用**。
* 本设置适用于原生系统还没折腾过 IPv6 设置的情况。
* 由于使用本文设置失败，导致路由不能上网的，本人**不负技术责任**。（如果发生此类情况请恢复出厂设置，或重新刷系统解决)

## 1. 前言

经常看到群里聊天有人提到 IPv6，而且毕竟学校大部分宿舍都部署了 IPv6，路由器都折腾好了，不设置一下 IPv6 好像也挺说不过去的。在学校使用 IPv6 貌似可以直接使用 Google (?)。如果要享用六维空间之类 PT 站的资源，那也是必须得搞定 IPv6，所以说在学校这东西还是很有用的。

但是，我是跟着群主发的教程来了一遍发现好像还不行。所以，在网上查了其他的一些文章，自己摸索了一下，最后弄好了。感觉可以在这里分享一下自己的经验和一些心得。

## 2. 参考资料

* [**在OpenWrt上配置原生IPv6 NAT**](http://blog.csdn.net/cod1ng/article/details/45421025#) ——我目前主要是参考这篇博客设置的。下面留言区还有群主大大的留言，大家可以去围观一下。
* 「群文件」>「新人必点」>「在路由器上部署IPV6专题」。
* 还有群文件里其他几个和 IPv6 设置有关的教程。

## 3. 我的基本情况

* 路由器：Netgear 4300 V1
* 固    件：OpenWrt Chaos Calmer (15.05.1, r48532) （官方 CC固件）
* 宿    舍：北校 研六

## 4. 设置前的准备

### 4.1 查询自己的 IPv6 网关

这一步请自己参考「群文件」>「新人必点」>「在路由器上部署IPV6专题」里相关章节的描述。简要说一下就是：

1. **单机**连网线上网，能在网络属性中获取 IPv6 地址的话。
2. 还是**单机**上网在 CMD 命令行窗口输入：`tracert -6 2404:6800:4005:80a::200e` 下面第一跳显示的就是你的 IPv6 网关。华工的基本都是 `2001:250:3000:xxxx::1` 这样的。
3. 得到这个网关地址记下来，后面设置要用。

后面操作都是连接路由器完成了。

### 4.2 确认路由器 IPv6 WAN 的状态

从 OpenWrt 官网的 [OpenWrt native IPv6-stack](https://wiki.openwrt.org/doc/uci/network6) 这个描述来看，BB 和 CC 固件都已经提供了原生的 IPv6 支持(?)。所以，路由器设置好之后，在 Luci 界面的 「Status」->「Overview」页面，可以看到，`Network` 下有个 `IPv6 WAN Status` 的状态，其实已经可以自动获取 IPv6 地址了（如图所示）。(我这里获取的地址和单机连接获取的地址还不太一样，这里获取的好像是单机连接时候的第二个地址，这里面原理也不是很懂。)

![IPv6-WAN-Status](/images/openwrt-ipv6-config/ipv6-wan-status.png)

**如果这里是类似于`"Not connected"` 之类的状态，那下面的方法可能并不适用**。

### 4.3 必要的 ipk 包

需要的软件包有两个：

* `ip6tables.ipk` 
* `kmod-ipt-nat6.ipk` 

**群文件提供了这两个安装文件，但仅适用于群里推荐的路由器和固件版本。`kmod-ipt-nat6` 在内核版本不兼容的情况下是无法正常使用的。所以，建议使用我下面的安装方法安装这两个软件包，可有效避免不兼容的情况发生，而且操作步骤更简单一些。**

#### 安装方法（推荐）：

因为路由器设置好之后已经可以正常上网了，所以可以直接用 `opkg` 命令从网上安装，在 PuTTY 中执行：

```bash
opkg update
opkg install ip6tables
opkg install kmod-ipt-nat6
```

提示都是能看懂的人话，看提示就知道安装的结果了。

#### 使用群文件里的安装包安装（不推荐）：

可以按照群教程我们已经很熟悉的方法，WinSCP 把 `.ipk` 文件传到路由器的 `/tmp` 文件夹下，然后 PuTTY 登录执行 `opkg install /tmp/*.ipk`。

安装完成后就可以开始设置路由器了。

## 5. 设置路由器

这里是用前面提到的那篇博客的设置方法，直接修改路由器的配置文件。（**提醒新手不要乱改，否则会上不了网的。改了哪些文件，最好都能做个备份，或者对改动的部分做个注释。**）可以用 WinSCP 找到相应路径下的文件打开修改，或者你会用 vi 的话也可以直接在 PuTTY 中用 vi 编辑相应的文件。**如果你不知道我在说什么请不要乱来。**

### 5.1 修改 `/etc/config/network` 文件

1. 把 `config globals 'golbals'` 下面这行注释掉，前面加个 `#` 号：
   ```bash
   # option ula_prefix 'fdd5:4309:1074::/48'
   ```
2. 在 `config interface 'lan'` 的下面添加一行：
   ```bash
   option ip6addr 'fc00:100:100:1::1/64'
   ```
3. 把 `config interface 'lan'` 下面 `option ip6assign '60'` 这行注释掉（这个我是参考群里官方教程的，博客那篇里面没有这一步，这个差别体现在哪我也不清楚）：
   ```bash
   # option ip6assign '60'
   ```

我的修改完成之后大概是这个样子：

![Set-network](/images/openwrt-ipv6-config/set-network.png)

### 5.2 修改 `/etc/config/dhcp` 文件

在 `config dhcp 'lan'` 设置下，对照下面的设置添加你没有的部分，好像是只用添加最后两行还是三行我也记不清了：

```bash
config dhcp 'lan'
        option interface 'lan'
        option start '100'
        option limit '150'
        option leasetime '12h'
        option dhcpv6 'server'
        option ra 'server'
        option ra_management '1'
        option ra_default '1'
```

这里 `ra_management` 和 `ra_default` 我看群里官方的是设置成 `'2'` 的，我目前用的是那篇博客的设置。这个区别在哪我也还不知道。

### 5.3 修改 `/etc/firewall.user` 文件

这个和后面的步骤都改用 PuTTY 输命令，PuTTY 登录路由器之后输，（注意这里是一行命令，中间没有回车，由于页面宽度的问题 ，这个显示有点让人迷惑，`-j` 是连着的没有空格。）： 

```bash
echo ip6tables -t nat -A POSTROUTING -o $(uci get network.wan.ifname) -j MASQUERADE>/etc/firewall.user
```

### 5.4 在 `/etc/hotplug.d/iface/` 文件夹内新建文件

1. 这个也是用 PuTTY 输命令，按顺序输入下面三行命令:
   * **注意：第二行单引号和双引号不要搞错**
   * **注意：这里第 3 行，`gw` 后面跟的是你前面查到的你的 IPv6 网关**
   ```bash
   echo "#!/bin/sh">/etc/hotplug.d/iface/90-ipv6
   echo '[ "$ACTION" = ifup ] || exit 0'>>/etc/hotplug.d/iface/90-ipv6
   echo "route -A inet6 add default gw 2001:250:3000:5a06::1">>/etc/hotplug.d/iface/90-ipv6
   ```
2. 输入下面的命令，设置这个文件的权限：

   ```bash
   chmod 777 /etc/hotplug.d/iface/90-ipv6
   ```

完成以上步骤之后**重启路由器**，过个几分钟，在 Luci 界面的 「Status」->「Overview」页面，`Network` 下的 `IPv6 WAN Status` 显示获取了 IPv6 地址的时候就可以使用 IPv6 上网了。

## 6. 测试

可以尝试登陆 http://test-ipv6.com 这个网站测试你的 IPv6 连接是否正确。我现在的结果如下图：

![IPv6-test](/images/openwrt-ipv6-config/ipv6-test.png)

也可登陆学校网络中心建议的 http://test.ipv6daohang.com 测试。结果类似：

![IPv6-test-2](/images/openwrt-ipv6-config/ipv6-test-2.png)

虽然测试通过了，可是尝试打开 Google 还是会失败，可能需要通过添加 host 方法解决，没有测试过。[Google Scholar](https://scholar.google.com) 可以打开。
