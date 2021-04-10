---
layout: default
---

# 手工下载器(ManualDownloader)

Ianus Inferus(地狱门神, F.R.C.)

[ManualDownloader.rar](ManualDownloader.rar)

## 1 概论

有一个困扰我很久的问题，就是老是有一些网站下载某个东西要验证，导致支持断点续传的下载软件失效，使得一些文件一旦断掉，很难重新连上。

同时，目前的浏览器或者下载软件，一般都没有办法将一个下载的一半的文件手动更换为另一个链接。
为了对付这些问题，我制作了这个程序。

本程序用于

1)作为HTTP代理服务器拦截服务器二进制文件响应对应的请求

2)通过HTTP报文手工下载

3)下载到已有文件

## 2 使用说明

最简单的场景：下载一个文件。

### 1)捕捉请求

在浏览器中按照浏览到下载文件之前的最后一步。

点Listen，并将浏览器的HTTP代理服务器设置为127.0.0.1，端口为界面上显示的端口。

点Capture。

在浏览器中点击下载文件。

此时应该在Header框中出现对应的HTTP请求头，如果没有出现，多试几次。如果还不出现，请修改代码解决。

出现之后，可以停止Listen。

### 2)选择文件和下载范围

文件在File框中选择，之后修改Header中的Range字段。

如果文件已经下载到一半，可以按FromFile自动修改Range字段为从文件结束位置开始。

### 3)下载
点击Download。等待进度结束。如果中间出现错误，需要重新修改Range。

### 4)检验

点击Verify，软件会自动选取1024个Range，从每个Range的头部取1024字节和文件对比。如果出错，会在最后显示出错的范围。

还可在其他场景中使用，用得来人自然用得来。

## 3 环境要求

本程序使用 Visual Basic 10.0 编写，开发时需要 Microsoft .Net Framework 4.0 编译器 或 Visual Studio 2010 支持。

本程序运行时需要 Microsoft .Net Framework 4 或 Microsoft .Net Framework 4 Client Profile 运行库支持。

Microsoft .Net Framework 4 (x86/x64，48.1MB)

<http://download.microsoft.com/download/9/5/A/95A9616B-7A37-4AF6-BC36-D6EA96C8DAAE/dotNetFx40_Full_x86_x64.exe>

Microsoft .NET Framework 4 Client Profile (x86，28.8MB)

<http://download.microsoft.com/download/3/1/8/318161B8-9874-48E4-BB38-9EB82C5D6358/dotNetFx40_Client_x86.exe>

## 4 用户使用协议

本程序是免费自由软件，所有源代码和可执行程序按照BSD许可证授权，详见License.zh.txt。

本程序的所有文档不按照BSD许可证授权，你可以不经修改的复制、传播这些文档，你还可以引用、翻译这些文档，其他一切权利保留。

本程序使用的Firefly.Core.dll和Firefly.GUI.dll是萤火虫框架库的一部分，按照Firefly-License.zh.txt授权。

## 5 备注

如果发现了BUG，或者有什么意见或建议，请直接跟贴。

[back](../../)
