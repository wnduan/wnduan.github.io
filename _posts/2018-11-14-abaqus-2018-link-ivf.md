---
layout: post
comments: true
title: "Win10 下 Abaqus 链接 Fortran(IVF) 以通过用户子程序验证的设置"
date: 2018-11-14 15:20:43 +0800
description: "A guide on link abaqus 2018 and intel visual fortran under Win10 OS."
categories: 
tags: [abaqus,visual studio,intel parallel studio]
---

- 系统环境：Windows 10
- 软件版本：
    - Abaqus 2018
    - Visual Studio 2017 Community + Visual C++ 14.16.27023.1
    - Intel Parallel Studio XE 2019 + Fortran Compiler 19.0

## 1 选择与 Abaqus 匹配的编译环境
在 Abaqus 官网的 [SIMULIA Platforms & Configuration Support](https://www.3ds.com/support/hardware-and-software/simulia-system-information/) 页面可以查看各个版本的系统需求，和测试结果。官方使用的测试环境可以在相应的 Test Configurations 页面查看，其中就有编译器的相关内容。例如 Abaqus 2018 在 Windows 10 Enterprise 系统下的 [Test Configurations](https://www.3ds.com/fileadmin/PRODUCTS/SIMULIA/PDF/guide/test-configurations-abaqus-2018-windows-10.pdf) 中有如下信息：

```text
C++ Compiler: Microsoft Visual C++ 14.0.24215.1
Linker Version: Microsoft (R) Incremental Linker Version 14.00.24215.1
Fortran Compiler: Intel Fortran Compiler 16.0
```

由此可以确定官方使用的编译环境。去 Visual Studio 和 Intel 的官网确认一下，可以知道 Visual C++ 14.0 对应的是 Visual Studio Community 2015 版；Fortran Compiler 16.0 对应 Intel Parallel Studio XE 2016/2017。还要注意的一个问题就是 Parallel Studio 需要与 Visual Studio 的版本相匹配。

建议使用官方测试通过的环境，这样安装配置之后一般不会出现各种奇怪的问题。我自己是尝试了一个比较新的组合，目前基本的测试和验证是能够通过，不过不知道以后是不是会遇到问题。

### 1.1 安装顺序
Visual Studio -> Intel Parallel Studio -> Abaqus

## 2 安装完成后的配置
安装完成之后可以使用 Abaqus Verification 先测试一下程序中的各个模块是否能正常使用。如果某些模块验证失败，则应查看一下 log 文件，检查失败的原因。为了编译用户子程序，Abaqus 在运行前需要调用 `ifortvars.bat` 设置正确的编译环境变量，通常用户子程序相关模块验证失败就是因为无法正确调用 `ifortvars.bar` 文件造成的。对于，这类问题可以尝试进行以下设置。

### 2.1 在 Windows 10 中设置系统变量
这个步骤是在下面参考的第 [1] 个设置中提到的，但是似乎并不必要，因为下一步的设置中已经直接使用完整路径来调用所需的 `.bat` 文件。所以**建议这一步可以直接跳过**。在正确安装配置的情况下，Intel 提供的用于设置 Fortran 编译环境的 `ifortvars.bat` 文件能够正确调用相应的 `vcvars` 批处理文件。

- 找到 `vcvars64.bat` 文件，Visual Studio 2015 的位于：
  ```text
  C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\bin\amd64
  ```
  Visual Studio 2017 Community 的位于：
  ```text
  C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Auxiliary\Build
  ```
- 找到 `ifortvars.bat` 文件，一般在这个路径下：
  ```text
  C:\Program Files (x86)\IntelSWTools\compilers_and_libraries\windows\bin
  ```
- 将上面两个文件的路径添加到系统（或用户）的 Path 变量中。

### 2.2 Abaqus CAE/Command/Verification 启动时载入 Fortran 编译器
这一步是设置程序的启动方式选项，需要设置 Abaqus CAE, Command, 和 Verification 这三个程序。具体步骤：

- 在开始菜单找到 Abaqus Command 的图标，右击之后选择「更多」>「打开所在文件夹」，这里也有其他程序的图标。或者直接前往：
  ```text
  C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Dassault Systemes SIMULIA Abaqus CAE 2018
  ```
- 首先设置 Abaqus Command 的启动方式，右击 Abaqus Command 的图标，选择「属性」。这里可以看到有一个「目标」的设置，在原有设置 `C:\Windows\system32\cmd.exe /k` 前添加下面一行代码，并保存。
  ```text
  "C:\Program Files (x86)\IntelSWTools\compilers_and_libraries\windows\bin\ifortvars.bat" intel64 vs2017 & 
  ```
  注意：上面这行代码比较长，前面路径就是 `ifortvars.bat` 文件的完整路径，需要使用引号 `"` 括起来，还有最后的 `&` 符号不要漏掉，并且注意空格，`&` 符号前后都有空格。另外 `vs2017` 需要根据自己的 Visual Studio 版本设置相应的值。
- 然后设置 Abaqus CAE 的启动方式，类似地，右击 Abaqus CAE 的图标，选择「属性」在「目标」中的原有设置 `C:\SIMULIA\CAE\2018\win_b64\resources\install\cae\launcher.bat cae || pause` 前面添加同样的代码，并保存。
- 最后 Abaqus Verification 也是一样，在原有设置前面添加上述代码。

### 2.3 检查相关信息
完成上述两步之后，子程序模块应该就可以正确运行了。可以使用 Abaqus Command 检查当前的系统环境和配置，双击打开 Abauqs Command 命令窗口，输入
```cmd
abaqus info=system
```
可以得到与官方 Test Configures 类似的输出，例如我的系统信息：
```text
C++ Compiler:         Microsoft Visual C++ 14.16.27023.1
Linker Version:       Microsoft (R) Incremental Linker Version 14.16.27023.1
Fortran Compiler:     Intel Fortran Compiler 19.0
MPI:                  MS-MPI 7.0.12437.6
```
然后再输入：
```cmd
abaqus verify -user_std
```
就可以看到 Abaqus/Standard 的用户子程序验证 PASS 了。也可以使用 Abaqus Verification 程序运行完整的测试和验证。

## 3 尾巴
由于我使用的不是官方推荐的环境，所以在 Verification 的过程中还是有遇到其他的问题。`C++ make` 模块的验证无法通过。检查 `cpp_make.log` 文件，发现是编译过程中头文件的路径错误：
```
C:\Program Files (x86)\IntelSWTools\compilers_and_libraries\windows\compiler\include\stdint.h(40): error C1803: Cannot open include file: '../../vc/include/stdint.h': No such file or directory
```
可以看到，这里头文件 `stdint.h` 第 40 行引用了另一个 `stdint.h` 头文件，但是路径错误。网上搜索一番，发现是 Visual Studio 在 2017 版中更改了许多文件的路径，而 Intel Parallel 中的某些定义又没有考虑到这些问题，应该说是 Intel Parallel Studio 中的 bug 了。

### 3.1 查找 VC 的路径
Micorsoft 提供了一个命令行工具 `vswhere` 可以帮助查找和判断当前 Visual Studio 2017 的环境。现在需要查找 VC 的安装位置，在 PowerShell 中使用：
```
> vswhere -property installationPath
C:\Program Files (x86)\Microsoft Visual Studio\2017\Community
```
前往给出的路径，可以看到有个 VC 文件夹，这就是 Visual C++ 的位置，上面需要引用的头文件就在这里，当然还是最要寻找一番。实际路径是：
```
.../VC/Tools/MSVC/14.16.27203/include/stdint.h
```

### 3.2 解决问题
也没什么好办法，只能来点 dirty hack，直接修改出错的文件。去 `IntelSWTools\...\include\stdint.h` 文件，找到错误的位置：
```c
#include __TMP_STRING(__TMP_PASTE2(__MS_VC_INSTALL_PATH,/include/stdint.h))
```
这里显然就是需要修改一下 `__MS_VC_INSTALL_PATH` 的定义，向前面找几行可以看到这个宏定义，将这行注释掉，并在下面添加新的定义。如下：
```c
#ifndef __MS_VC_INSTALL_PATH
// #define __MS_VC_INSTALL_PATH ../../vc <- 原有定义前面加 '//' 注释掉，并添加下面一行新的定义
#define __MS_VC_INSTALL_PATH C:/Program Files (x86)/Microsoft Visual Studio/2017/Community/VC/Tools/MSVC/14.16.27023
#endif
```
之后管理员权限保存，在 Abaqus Command 中使用命令：
```
abaqus verify -make
```
可以看到问题解决。至此，Verification 中所有问题解决。

## 参考资料

1. [Linking Abaqus2017 and Intel Parallel Studio XE2016 (Visual Fortran) in Windows 10 X64](https://www.researchgate.net/profile/Petri_Tanska/publication/313924098_Linking_ABAQUS_2017_and_Intel_Parallel_Studio_XE2016_Visual_Fortran_in_Windows_10_x64/links/58b003efaca2725b5411b020/Linking-ABAQUS-2017-and-Intel-Parallel-Studio-XE2016-Visual-Fortran-in-Windows-10-x64.pdf?_sg%5B0%5D=KjIj-PyDmhfyQGQnKwnPSCLKjgVBEsYTA5xTnPcuBNGoIV33gV5hNCcQ-92oN5vForOsoo9DEfXDTy8GJZ3Meg.dwRkdpkT1N8tAROQb8HjyI39OOXnMaAwF_mMmY05pdiTQWGIcs3egoRkB2eR8pfU1IRuD2-za57QD99z557TQA&_sg%5B1%5D=5tfjgGqq-YupCCNJVIJG6o306qTbAD6BIBVn3ghIr5piyuSZYDhtWhbvQ9AggmX15xUCmDLhcvo1CVRj0hUnNbMsTnNKu29hPADSLJPXFZDX.dwRkdpkT1N8tAROQb8HjyI39OOXnMaAwF_mMmY05pdiTQWGIcs3egoRkB2eR8pfU1IRuD2-za57QD99z557TQA&_iepl=)

2. [Configure ABAQUS to run user subroutines](http://www-h.eng.cam.ac.uk/help/programs/fe/abaqus/faq68/UserSubConfigureInfo.html)
