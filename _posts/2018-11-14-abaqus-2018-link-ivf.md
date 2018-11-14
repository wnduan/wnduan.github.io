---
layout: post
comments: true
title: "Win10 下 Abaqus 链接 Fortran(IVF) 以通过用户子程序验证的设置"
date: 2018-11-14 15:20:43 +0800
description: "A guide on link abaqus 2018 and intel visual fortran under Win10 OS."
categories: 
tags: []
---

- 系统环境：Windows 10
- 软件版本：
    - Abaqus 2018
    - Visual Studio 2017 Community + Visual C++ 14.16.27023.1
    - Intel Parallel Studio XE 2018 + Fortran Compiler 19.0

## 选择与 Abaqus 匹配的编译环境
在 Abaqus 官网的 [SIMULIA Platforms & Configuration Support](https://www.3ds.com/support/hardware-and-software/simulia-system-information/) 页面可以查看各个版本的系统需求，和测试信息。官方使用的测试环境可以在相应的 Test Configurations 页面查看，其中就有编译器的相关信息。例如 Abaqus 2018 在 Windows 10 Enterprise 系统下的 [Test Configurations](https://www.3ds.com/fileadmin/PRODUCTS/SIMULIA/PDF/guide/test-configurations-abaqus-2018-windows-10.pdf) 中有如下信息：

```text
C++ Compiler: Microsoft Visual C++ 14.0.24215.1
Linker Version: Microsoft (R) Incremental Linker Version 14.00.24215.1
Fortran Compiler: Intel Fortran Compiler 16.0
```

由此可以确定官方使用的编译环境。去 Visual Studio 和 Intel 的官网查看一下相关信息，可以知道 Visual C++ 14.0 对应的是 Visual Studio Community 2015 版；Fortran Compiler 16.0 对应 Intel Parallel Studio XE 2016/2017。还需要注意的一个问题就是 Parallel Studio 需要与 Visual Studio 的版本相匹配。

建议使用官方测试通过的环境，这样安装配置之后一般不会出现各种奇怪的问题。我自己是尝试了一个比较新的组合，目前基本的测试和验证是能够通过，不过不知道以后是不是会遇到问题。

### 安装顺序
Visual Studio -> Intel Parallel Studio -> Abaqus

## 安装完成后的配置
安装完成之后可以使用 Abaqus Verification 先测试一下程序中的各个模块是否能正常使用。如果某些模块验证失败，则应查看一下 log 文件，检查失败的原因。为了编译用户子程序，Abaqus 在运行前需要调用 `ifortvars.bat` 设置正确的编译环境变量，通常用户子程序相关模块验证失败就是因为无法正确调用 `ifortvars.bar` 文件造成的。对于，这类问题可以尝试进行以下设置。

### 在 Windows 10 中设置系统变量
- 找到 `vcvars64.bat` 文件，一般位于：
  ```text
  C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\bin\amd64
  ```
- 找到 `ifortvars.bat` 文件，一般位于：
  ```text
  C:\Program Files (x86)\IntelSWTools\compilers_and_libraries\windows\bin
  ```
- 将上面两个文件的路径添加到系统（或用户）的 Path 变量中。

### Abaqus CAE/Command/Verification 启动时载入 Fortran 编译器
这一步是设置程序的启动方式选项，需要设置 Abaqus CAE, Command, 和 Verification 这三个程序。具体步骤：

- 在开始菜单找到 Abaqus Command 的图标，右击之后选择「更多」>「打开所在文件夹」，这里也有其他程序的图标。或者直接前往：
  ```text
  C:\ProgramData\Microsoft\Windows\Start Menu\Programs\Dassault Systemes SIMULIA Abaqus CAE 2018
  ```
- 首先设置 Abaqus Command 的启动方式，右击 Abaqus Command 的图标，选择「属性」。这里可以看到有一个「目标」的设置，在原有设置 `C:\Windows\system32\cmd.exe /k` 前添加下面一行代码，并保存。
  ```text
  "C:\Program Files (x86)\IntelSWTools\compilers_and_libraries\windows\bin\ifortvars.bat" intel64 vs2017 &
  ```
  注意：上面这行代码比较长，前面路径就是 `ifortvars.bat` 文件的完整路径，需要使用引号 `"` 括起来，还有最后的 `&` 符号不要漏掉。另外 `vs2017` 这个需要根据自己的 Visual Studio 版本设置正确的值。
- 然后设置 Abaqus CAE 的启动方式，类似地，右击 Abaqus CAE 的图标，选择「属性」在「目标」中，在原有设置 `C:\SIMULIA\CAE\2018\win_b64\resources\install\cae\launcher.bat cae || pause` 前面添加同样的代码，并保存。
- 最后 Abaqus Verification 也是一样，在原有设置前面添加上述代码。

### 检查相关信息
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

## 参考资料

[Linking Abaqus2017 and Intel Parallel Studio XE2016 (Visual Fortran) in Windows 10 X64](https://www.researchgate.net/profile/Petri_Tanska/publication/313924098_Linking_ABAQUS_2017_and_Intel_Parallel_Studio_XE2016_Visual_Fortran_in_Windows_10_x64/links/58b003efaca2725b5411b020/Linking-ABAQUS-2017-and-Intel-Parallel-Studio-XE2016-Visual-Fortran-in-Windows-10-x64.pdf?_sg%5B0%5D=KjIj-PyDmhfyQGQnKwnPSCLKjgVBEsYTA5xTnPcuBNGoIV33gV5hNCcQ-92oN5vForOsoo9DEfXDTy8GJZ3Meg.dwRkdpkT1N8tAROQb8HjyI39OOXnMaAwF_mMmY05pdiTQWGIcs3egoRkB2eR8pfU1IRuD2-za57QD99z557TQA&_sg%5B1%5D=5tfjgGqq-YupCCNJVIJG6o306qTbAD6BIBVn3ghIr5piyuSZYDhtWhbvQ9AggmX15xUCmDLhcvo1CVRj0hUnNbMsTnNKu29hPADSLJPXFZDX.dwRkdpkT1N8tAROQb8HjyI39OOXnMaAwF_mMmY05pdiTQWGIcs3egoRkB2eR8pfU1IRuD2-za57QD99z557TQA&_iepl=)

[Configure ABAQUS to run user subroutines](http://www-h.eng.cam.ac.uk/help/programs/fe/abaqus/faq68/UserSubConfigureInfo.html)
