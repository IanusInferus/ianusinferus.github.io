---
layout: default
---

# 使用USB硬盘启动操作系统

Ianus Inferus

2022-08-17

## 简介

最近手头有一些闲置的NVMe的SSD硬盘，就用来装了一些备用系统例如Ubuntu。

由于我的电脑上主板只有两个NVMe接口，而且需要将显卡和芯片组盖子取下才能更换，非常麻烦，就买了一些PCI-E转NVMe转接卡。后来发现打开机箱仍然比较麻烦，就买了USB转NVMe硬盘盒。结果发现无法启动硬盘盒中的操作系统。

## BIOS xHCI设置

从[Making a portable full installation of Ubuntu on a USB HDD](https://www.dionysopoulos.me/making-a-portable-full-installation-of-ubuntu-on-a-usb-hdd.html)这篇文章了解到，默认情况下，BIOS引导时，为了最大的兼容性，默认会使用UHCI (USB 1.1)，然后操作系统启动时会加载xHCI (USB 3)驱动并重新连接对应的USB设备。由于操作系统本身也在USB硬盘上，断开的时候会导致操作系统出错。

解决方法是让BIOS引导的时候就使用xHCI，不需要操作系统再加载xHCI驱动，也就不需要重新连接USB设备。如果不需要运行非常老的不支持xHCI的操作系统（例如Windows 7），这不会导致问题。

在我的华硕主板上，这个设置项为XHCI Hand-off，需要设置为Disabled。

## USB转NVMe硬盘盒选择

市面上主要的USB转NVMe硬盘盒，是传输速度10GBps的。设计上分为滑盖式、掀盖式。滑盖式由于无法贴导热硅胶片，会在使用时导致硬盘温度过高，产生掉速或者掉盘。不要买抽拉式的，尤其是“绿联 M.2 NVMe移动硬盘盒 Type-C3.2接口SSD固态硬盘盒子笔记本电脑M2全铝外置盒”。

可以参考视频[移动硬盘盒选购指南](https://www.bilibili.com/video/BV1mT4y1r7cS)。

## 关闭Windows Fastboot

由于Windows Fastboot会将一些像硬盘索引之类的数据保存到休眠文件中，在多操作系统读写同一硬盘时会造成问题，需要关闭。

关闭方法为在控制面板的电源选项中，点击“选择电源按钮的功能”，点击“更改当前不可用的设置”，然后将取消“启用快速启动”。BIOS中可能也有Fastboot相关的选项，可以也取消掉。

由于现在很少需要用到单独的关机功能，这样造成的启动时间变长几乎没有影响。

## 安装系统时的注意事项

安装操作系统时，最好将系统盘之外的所有硬盘取下，防止EFI引导文件放置到错误的硬盘。如果已经发生了问题而又不想重新安装，可以先将ESP分区复制到合适的硬盘上，然后使用Windows安装镜像引导，按Shift+F10进入命令行，然后使用bcdedit命令进行编辑。不推荐使用任何其他软件编辑。分区的GUID，可以在DiskGenius之类的磁盘管理软件中查看。
