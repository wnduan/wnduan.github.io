---
layout: post
comments: true
title: "Mac 下安装和使用 GnuPG 的实践"
date: 2017-10-17 02:46:22 +0800
description: ""
categories: 
tags: [macOS,GnuPG,gpg]
---

GNU Privacy Guard (GnuPG/GPG) 是 OpenPGP 标准的实现，主要用于在网络通信中保护个人隐私，具体来说包括三个方面的用途：

1. 加密文件，防止其他人获取文件内容，常用于在邮件通信中实现端到端的加密通信。
2. 基于公/私钥的账户认证，例如可以用来登陆服务器，如 Github 等。
3. 文件签名，用私钥对文件签名，确保文件来自可靠的联系人，且未被第三方篡改。例如，许多软件的分发都会提供签名，以用于验证来源的可靠性。
4. 这个类似于第 3 点，密钥除了可以用于进行文件和消息的签名，也可以用来为其他密钥签名。这样的作用是可以用一个主密钥对管理多个子密钥对，或者还可以信任其他人的密钥。

1 安装
-----
最简单的方法还是用 brew  来安装，运行：
```bash
brew install gnupg
```
现在这个命令安装的是 GnuPG v2.2.1。作为安全应用，还是及时使用新版本会比较好。GnuPG 2.1 及以后的版本和之前的版本有些区别，这里的操作基于 2.2.1 版。

### 版本信息
在开始下一步之前，可以查看确认以下 GnuPG 的版本信息：
```bash
$ gpg --version
gpg (GnuPG) 2.2.1
libgcrypt 1.8.1
Copyright (C) 2017 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <https://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Home: /Users/<username>/.gnupg
Supported algorithms:
Pubkey: RSA, ELG, DSA, ECDH, ECDSA, EDDSA
Cipher: IDEA, 3DES, CAST5, BLOWFISH, AES, AES192, AES256, TWOFISH,
        CAMELLIA128, CAMELLIA192, CAMELLIA256
Hash: SHA1, RIPEMD160, SHA256, SHA384, SHA512, SHA224
Compression: Uncompressed, ZIP, ZLIB, BZIP2
```
这里 `Home` 列出了用户文件和配置文件的路径；下面是支持的算法，有些密钥生成算法仅在 `--expert` 选项下可用。

2 生成密钥
---------
已经装好的 GnuPG 可以通过 `gpg` 命令调用。现在就可以创建密钥了，刚接触 GnuPG 的新用户可以用
```bash
gpg --full-generate-key
```
来生成密钥，这个过程需要对一些特性进行选择和设置：

### 选择算法
这里有四个选项，由于目前 RSA 的支持更广泛，因此就选择第一个也是程序默认的：RSA and RSA。

### 选择长度
这是生成的密钥的长度，出于安全优先的考虑，选择最大可用的长度，对于 RSA 算法来说是 4096 bits。

### 有效期
看了网上的一些讨论，更短的有效期也说不上能显著增加安全性，例如 [StackExchange](https://security.stackexchange.com/questions/14718/does-openpgp-key-expiration-add-to-security) 的讨论。所以这里可以按网上的许多教程选择一年，或是不过期都可以。

### 用户信息
设置用户信息，包括姓名和邮箱地址。
