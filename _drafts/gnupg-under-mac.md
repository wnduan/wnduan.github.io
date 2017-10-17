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
$ brew install gnupg
```
现在这个命令安装的是 GnuPG v2.2.1。作为安全应用，还是及时使用新版本会比较好。GnuPG 2.1 及以后的版本和之前的版本有些区别，这里的操作基于 2.2.1 版。

### 1.1 版本信息
在已经装好的 GnuPG 可以通过 `gpg` 命令调用。开始下一步之前，可以查看确认以下 GnuPG 的版本信息：
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
现在就可以创建密钥了，刚接触 GnuPG 的新用户可以用
```bash
$ gpg --full-generate-key
```
生成密钥，这个过程需要对一些选项进行选择和设置。

### 2.1 密钥算法
```bash
$ gpg --full-generate-key
gpg (GnuPG) 2.2.1; Copyright (C) 2017 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
Your selection? 1
```
密钥的算法有四个选项，默认的是第 (1) 个，用于签名和加密的密钥均使用 RSA 算法生成；第 (2) 个选项是使用 DSA 算法生成用于签名的密钥，使用 Elgamal 算法生成用于加密文件的密钥；后两个则仅用对应的算法生成用于签名的密钥。网上对于算法的选择也有一些讨论，由于目前大多应用和服务对 RSA 的支持更好，因此选择第一个。

### 2.2 密钥长度
```bash
RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 4096
Requested keysize is 4096 bits
```
出于安全优先的考虑，密钥的长度选择最大值，对于 RSA 算法来说是 4096 bits。或者至少也应该选择默认的 2048 bits 长度，否则可能不安全。

### 2.3 密钥有效期
```bash
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 3y
Key expires at Fri Oct 16 16:37:28 2020 CST
Is this correct? (y/N) y
```
看了网上的一些讨论，更短的有效期也说不上能显著增加安全性，例如 [StackExchange](https://security.stackexchange.com/questions/14718/does-openpgp-key-expiration-add-to-security) 的讨论。所以这里可以按网上的许多教程选择一年，或是不过期都可以。上面的例子选择的是 3 年，所以输入 `3y`。输入 `0` 则表示永久有效。

### 2.4 帐户信息 uid
```
GnuPG needs to construct a user ID to identify your key.

Real name: John Doe
Email address: john.doe@example.com
Comment: test user
You selected this USER-ID:
    "John Doe (test user) <john.doe@example.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? o
```
设置帐户信息，包括姓名和邮箱地址。如果有多组在不同场景或组织使用的密钥，有必要附加一个注释，对密钥进行补充说明。

### 2.5 密码 (passphrase)
```
You need a Passphrase to protect your secret key.
Enter passphrase:
```

在下面这个界面中输入一句话作为密码，用于保护个人密钥，万一私钥泄露被他人获取，如果没有密码仍然无法使用。所以这里要设置一个安全性足够高的密码句。应该杜绝使用单词、姓名、生日等弱密码。另一方面，应该是自己容易记忆的，如果忘记密码句子，也是比较麻烦的。
```
            │ Please enter the passphrase to                       │
            │ protect your new key                                 │
            │                                                      │
            │ Passphrase: ________________________________________ │
            │                                                      │
            │       <OK>                              <Cancel>     │
```

### 2.6 生成密钥
此时程序将自动生成密钥，这个过程需要生成一系列的随机字节，如果程序提示 `Not enough random bytes available.`，可以通过移动鼠标，键盘输入，读写磁盘等操作来提供随机字节。不过在 Mac 上这个过程一下就完成了，基本不需要什么额外的操作。

至此，就获得了可以使用的密钥，整个过程非常简单。

3 查看生成的密钥
----------------
可以

4 更安全的实践
---------------

5 文件的加密和签名
------------------

6 公钥服务器 (keyserver)
------------------------

7 密钥的管理
-------------

