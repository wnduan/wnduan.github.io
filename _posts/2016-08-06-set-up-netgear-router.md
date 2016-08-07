---
layout: post
comment: true
title: "网件 Netgear WNDR4300 V1 刷机和设置"
date: 2016-08-06 00:35:00 +0800
description: ""
categories: 
tags: []
---

开始之前先介绍一下背景，只看设置过程的话可以跳过下面这段废话。

出于对宿舍网络监管的种种需求，学校的网络中心不允许在学生宿舍使用无线路由器。在技术上的实现就是：必须使用网络中心提供的拨号客户端验证上网。该客户端需要验证硬件的 MAC 地址，并且会禁用电脑的网卡，这样一来，用电脑做热点发射 WIFI 都变的不可能了。当然，凡事总是相生相克，毕竟是工科学校，什么电信、自动化、计算机学院都齐全，技术的问题，自然有技术达人可以解决。在这样的背景下，学校的牛人们组了一个群，提供基于 [OpenWrt](https://www.openwrt.org) 的解决方案。7 月初，学校改用了一个新的客户端，大概也就十来天的时间群里就推出了新的路由器拨号程序，效率相当之高。当然，这种技术细节的东西我是不懂，也就是按照教程自己打打命令折腾一下就好的事情。

在关注群里信息的时候，看有人提到网件 Netgear 的路由器很好用。想想我现在用的还是淘宝上淘的二手 TP-Link，就觉的是不是可以考虑升级一下也弄个 Netgear 的试试。于是决定买个路由器自己来刷 OpenWrt 官方固件。

群里当前的技术支持，主要针对 AR7x/AR9x 构架的 CPU，以及 OpenWrt BarrierBreaker 版本固件。参考了[官方固件支持设备](https://wiki.openwrt.org/toh/start)的介绍，最后选定了 Netgear WNDR4300 V1 。现在国内基本没有这型号的行货了，都是 WNDR4300 V2 的，而官方是不支持这个 V2 版的。最后，不得已也就买了所谓的”淘宝洋垃圾“，其实性价比还真是挺高的。

接下来就是刷机和配置的过程。

## 1. Netgear WNDR 4300 V1 刷机

### 准备工作

1. 首先，从[官网下载](https://downloads.openwrt.org/barrier_breaker/14.07/ar71xx/nand/)固件：`openwrt-ar71xx-nand-wndr4300-ubi-factory.img` 和升级文件：`openwrt-ar71xx-nand-wndr4300-squashfs-sysupgrade.tar`。另外可以把 `md5sums` 这个文本文件也下载下来，检查校验 MD5 值。
2. 请确认路由器当前使用的是网件 Netgear 官方的固件。在本地连接的属性中将电脑的 IP 地址设为 `192.168.1.10`，子网掩码 `255.255.255.0`。这时候可以打开 CMD 命令行窗口输入 `ping 192.168.1.1` 看看能不能 ping 通，一般也都没问题，就可以接着进行下面的步骤。
3. 电脑把 WIFI 连接禁用，只用网线连接路由器。
4. Win 7 系统是有 tftp 工具的，在「控制面板」->「程序」->「打开或关闭 Windows 功能」设置中勾选「TFTP 客户端」，这样就可以使用 tftp 命令行工具了。或者也可以去网上下载其他带用户界面的 tftp 工具来用。
5. 由于不支持太长的文件名，这里需要把下载下来的 `.img` 镜像文件重命名一个短名字，例如：`openwrt.img`。然后，为了使用命令行 tftp 工具方便，可以把这个文件放在 C 盘根目录：`C:\` 下。 

### 安装 OpenWrt 官方固件

1. 把 WNDR4300 路由器插电，但是电源开关关闭。网线连好，注意是要连到路由器的 LAN 口。

2. 在 Windows 中打开一个 CMD 命令行窗口，输入：`ping 192.168.1.1 -t`，这个窗口就一直开着，可以用于直观的看到路由器的连通状态。

3. 用一个回形针或牙签之类的东西按住路由器背面那个红圈里面的 'System restore' 按键。这是用于恢复出厂设置的按钮。要保持按住不放。然后打开电源开关。直到路由器前面电源指示灯变绿，并持续闪烁的时候才松开背面的 'System Restore' 按键。

4. 这一步就是真的要复制并安装镜像文件了。这里我是用命令行完成的，如果使用其他 tftp 工具也是类似的。再打开一个 CMD 命令行窗口，输入以下命令并回车：

   ```bash
   tftp -i 192.168.1.1 PUT C:/openwrt.img
   ```

   等待上传完成，然后就会自动开始固件安装过程，这一过程中路由器会自动重启若干遍。这时可以看着前面我们在 `ping` 的窗口，直到可以稳定的连通 10s 以上时，就表明固件安装已经成功了。

5. 这时，还有一件事要做，直接拔掉电源线断电，然后再关掉路由器的电源开关。这个是因为，如果不直接断电可能会导致 5G 频段的 WIFI 不能使用。总之，就按照这个流程来是没问题的。

### 安装 OpenWrt 升级包

1. 这个很容易了，打开一个命令行 CMD 窗口，运行 `ping 192.168.1.1 -t` 一直开着。
2. 然后就打开电脑的浏览器应用，比如谷歌 Chrome。地址栏输 `192.168.1.1`。打开控制面板的页面，进入之前有个登陆页面，实际上我们现在这个密码是空的，所以可以随便输入一个密码 `admin` 点登陆就可以 (或者留空直接登陆也行)。
3. 进入控制面板之后，在页面最上方「System」下菜单中选「Backup/Flash Firmware」。
4. 在打开的页面可以看到下面是 `Flash new firmware image`。我们在这里可以取消勾线 `Keep settings`。在下一行，先选择文件，选择前面下载的 `.tar` 文件。上传完成后点 `Flash image...` 然后确认，路由器也是和前面一样自动重启并安装升级。
5. 看到 `ping` 窗口已经显示稳定连通了，就算完成了。保险起见，还是直接拔电源，再关路由器开关。

到这里，全部的安装过程就完成了。但如果你现在就直接使用，会发现根本没有 WIFI 信号。这是因为默认设置是关闭了 WIFI 的。所以，其实正式使用前还需要一番配置。

## 2. 基本设置

OpenWrt 官方固件是默认设置是没有打开 WIFI 的。这时可以在浏览器打开 `192.168.1.1` 页面设置打开 2.4G 和 5G 频段的无线网络，并为其设置相应的 ssid (WIFI 名称) 和密码。

不过我这里要介绍的是通过命令行工具 Putty 来完成上述设置的方法。

### 为路由器设置密码

刷完 OpenWrt 固件后，系统 root 账号的密码还是空的，所以首先要为 root 账号设置一个密码，这样之后才能用 Putty 通过 ssh 访问。在浏览器中打开 `192.168.1.1` 在「System」下拉菜单打开「Administration」页面。上面的 'Router Password' 可以用 预设置管理员密码。确认下面的 'SSH Access' 处于打开的状态。这样就可以通过 SSH 访问路由器了。

### 使用 SSH 方式登陆路由器

Windows 系统可以使用 Putty 进行 SSH 登陆，打开 Putty，在 'Host Name (or IP address)' 栏填写 `192.168.1.1`，'Port' 是 `22`，'Connection Type' 选 `SSH`。然后点击 'Open' 在打开的窗口中的 'login as' 后填写 `root`，然后需要输入前面设置的密码，输入的过程中，密码是不可见的，也不会显示星号，输入好回车就行。

macOS 系统则自带了 SSH 工具，可以直接从 Terminal 中访问。在 Terminal 中输入：

```bash
$ ssh root@192.168.1.1
```

就可以打开登陆窗口，提示输入密码。正确输入密码之后就登陆到了路由器。

### 配置 WAN 口信息

如果是自动获取 IP 地址就不需要这些设置了，对于运营商的拨号登陆也是有其他的设置，这里的设置仅针对学校或单位等需要使用静态地址甚至需要绑定 MAC 地址连网的情形。

设置路由器使用静态 IP：

```bash
uci set network.wan.proto=static
```

设置路由器 IP 地址：

```bash
uci set network.wan.ipaddr=xxx.xxx.xxx.xxx
```

设置子网掩码：

```bash
uci set network.wan.netmask=255.255.255.0
```

设置默认网关：

```bash
uci set network.wan.gateway=xxx.xxx.xxx.xxx
```

设置首选和备用 DNS：

```bash
uci set network.wan.dns='xxx.xxx.xxx.xxx xxx.xxx.xxx.xxx'
```

设置路由器的 MAC 地址：

```bash
uci set network.wan.macaddr=11:22:33:44:55:66
```

### 配置无线网络

打开无线网络 (2.4G 和 5G)，并设置 ssid (WIFI 名称) 和密码。

打开无线网络并设置无线模式为 ap：

```bash
$ uci set wireless.radio0.disabled=0 # 2.4G
$ uci set wireless.raido1.disabled=0 # 5 G
$ uci set wireless.@wifi-iface[0].mode=ap
$ uci set wireless.@wifi-iface[1].mode=ap
```

设置无线名称和密码：

```bash
$ uci set wireless.@wifi-iface[0].ssid=xxxx # 设置 2.4G 无线网络名称
$ uci set wireless.@wifi-iface[1].ssid=xxxx # 设置 5G 无线网络名称
$ uci set wireless.@wifi-iface[0].encryption=psk2
$ uci set wireless.@wifi-iface[1].encryption=psk2
$ uci set wireless.@wifi-iface[0].key=****** # 设置 2.4G 无线网络密码
$ uci set wireless.@wifi-iface[1].key=****** # 设置 5G 无线网络密码
```

使上述设置生效并重启路由器：

```bash
$ uci commit
$ ifup wan
$ /etc/init.d/network restart
```

到这里基本的设置就完成了。学校的路由器还需要安装相应的拨号软件，并设置登录帐号和密码才能正常上网。具体设置参考路由器群里的教程就可以，这里就不介绍了。
