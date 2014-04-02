---
layout: post
title: Install Linux Graphic Guide
description: 使用Linux系统也有一段时间了，今天我来总结一下在已经自带Windous系统的情况下安装 Linux 双系统的两种方法，一种是使用U盘来安装，另一种是使用硬盘来安装。我个人认为使用U盘安装的优势更大一些。而且这种方式适应于几乎任何一种Linux的安装。在本教程中主要以Debian发行版为例进行讲解，教程中还将包括如何完全地卸载Linux。通过多次的安装经验可得，在安装过程中主要解决是安装引导的问题，只要解决了这个大问题，其它的只需要跟着提示做就行了。下面就让我们开始吧！
---

使用Linux系统也有一段时间了，今天我来总结一下在已经自带Windous系统的情况下安装 Linux 双系统的两种方法，
一种是使用U盘来安装，另一种是使用硬盘来安装。我个人认为使用U盘安装的优势更大一些。
而且这种方式适应于几乎任何一种Linux的安装。在本教程中主要以Debian发行版为例进行讲解，教程中还将包括如何完全地卸载Linux。
通过多次的安装经验可得，在安装过程中主要解决是安装引导的问题，只要解决了这个大问题，其它的只需要跟着提示做就行了。
下面就让我们开始吧！

# 一、前期准备

## DVD的镜像文件
1. 64位镜像文件地址:
`http://cdimage.debian.org/debian-cd/7.4.0/amd64/iso-dvd/`
2. 32位镜像文件地址:
`http://cdimage.debian.org/debian-cd/7.4.0/i386/iso-dvd/`
## U盘安装所需工具：
[UNetbootin](http://unetbootin.sourceforge.net/)与一个够装下3.7G内容的U盘。
## 硬盘安装所需工具：
EasyBCD以及vmlinuz和initrd.gz，后两个工具的链接：

1. 64位工具地址：
http://ftp.cn.debian.org/debian/dists/Debian7.4/main/installer-amd64/current/images/hd-media/
2. 32位工具地址：
http://ftp.cn.debian.org/debian/dists/Debian7.4/main/installer-i386/current/images/hd-media/

要注意的是版本号的问题，该网址只会提供最新的工具，所以当Debian7的版本变为7.5或7.6的时候，
你只需要稍微改以下网址上的版本号就可以找到了。

## 磁盘分区
我们需要在Windows系统环境下进行磁盘分区操作，目的是给我们的Linux系统一定的空间。
一般情况下，我们默认的分区有C、D、E、F等盘，而且默认的磁盘的格式都是NTFS。
我机器是现有的是Windows7，在Window7下有自带的磁盘管理工具。
通过控制面板 -> 系统和安全 -> 管理工具 -> 计算机管理 -> 磁盘管理就可以找到，如下：

![cipan](/images/2014-04-02-1.jpg)

我们只需要使用磁盘管理为Linux腾出一块大于30G的空间即可。具体大小视个人情况而定。
需要注意的是我们在格式化或删除卷的同时，相应盘的内容会被清空，所以我们要提前做好备份的工作。

# 二、U盘安装Debian7
在做好磁盘分区后，我们需要使用我们的刚下载的UNetbootin工具将镜像文件释放到你的U盘上，
注意一定要通过该工具，不是普通的粘贴复制，也不是使用压缩工具解压。

![UNetbootin](/images/2014-04-02-2.jpg)

在UNetbootin工具里也可以下载所需的镜像文件，由于我们事先下载了镜像文件，
所以我们只需要选择光盘镜像即可。然后选择相应的镜像文件路径，驱动器就是选择我们的U盘。
再提醒一遍镜像文件别放在U盘里，是放在U盘外的，我们通过UNetbootin工具导入进去的。

一切选择完毕，点击确定吧。

## 修改默认启动项
默认情况下我们是直接硬盘启动的，但现在的情况是我们的安装文件全部在U盘上，所以我们需要修改启动项，使用U盘启动。
我们重新开机，然后点击键盘上的F2（不同机器是不同的，你应该根据机器开机时下方的提示），进入BIOS设置

![qidong](/images/2014-04-02-4.jpg)

我们需要调整一下优先启动项，根据下方的提示，我们通过按键F6将 USB HDD 调整到最上面，F10保存退出。
接下来就是重启电脑，它就会自动进入安装界面。

![Install](/images/2014-04-02-5.jpg)

# 三、硬盘安装 Debian7
这里我们将通过硬盘安装Linux，我们需要在磁盘分区后再让磁盘给我们腾出4G左右的磁盘空间存放镜像文件。
腾出空间是有一定的好处的，当我们一不小心系统崩溃的时候，并且需要重装系统时，我们可以免去一定量的操作。
注意腾出的空间格式化的同时，要将磁盘格式修改为FAT32(这和U盘的磁盘格式是相同的)，
然后需要将我们之前下载的debian-7.4.0-amd64-DVD-1.iso、vmlinuz和initrd.gz放到改盘符內。
当完成对磁盘的操作后，我们需要做的是打开EasyBCD，如下

![EasyBCD](/images/2014-04-02-3.jpg)

在添加新条录下的NeoGrub中，点击配置
然后填入以下内容，记得保存

	# NeoSmart NeoGrub Bootloader Configuration File
	#
	# This is the NeoGrub configuration file, and should be located at C:\NST\menu.lst
	# Please see the EasyBCD Documentation for information on how to create/modify entries:
	# http://neosmart.net/wiki/display/EBCD/
	title install debian7--wheezy!
	root (hd0,6)
	kernel /vmlinuz  root=casper iso-scan/filename=/debian-7.4.0-amd64-DVD-1.iso
	initrd /initrd.gz
	boot

注意：这里的(hd0, 6)是根据你所放的三个文件的盘符位置确定的，一般地从C盘开始分别是
(hd0,0)、(hd0,1)、(hd0,2)、(hd0,3)、(hd0,5)，你需要根据实际情况修改后一位，
直到你找到自己的正确的盘符为止。

建议：我们在完成安装后可以不必删除4G盘符和EasyBCD里的内容，当我们遇到大麻烦需要重新安装系统的时候，
我们可以免去以上的操作。通过下面的完全卸载Linux的内容后，就可以直接进入Linux安装界面。

完成后就可以重新启动了，启动后就会进入安装界面。

# 四、安装界面
当你进入安装界面后，你就可以根据相应的安装提示，比如对语言、时间等做出正确的选择。
需要注意的是，当要进行磁盘分区时，你需要选择手动分区。
这里提供一个本人的分区方案(以磁盘大小为100G为例)：

	/ 30G
	/boot 400M
	swap 8G
	/home 62G

1. 安装中如遇到域名设置或IP设置等可以略过。
2. 安装中如遇到缺少某驱动文件时，记得将它记录下来，我遇到的是缺少wifi驱动，
然后我安装完成后通过刚才记录的内容下载了firmware-iwlwifi_0.36+wheezy.1_all.deb，并安装上去就没有问题了。
3. 一个容易出错的地方，当我们完成分区后，有提示我们 EFI分区引导未找到，这时我们就不要再继续进行了，
而是需要退出并重启电脑，然后进入BIOS设置(修改启动项页面)中找到并关闭UEFI。重启后我们就可以继续完成安装了。

# 五、完全卸载Linux
这里是要完全卸载我们的Linux系统，在卸载之前我们一定要先将我们所需要的、存放在Linux系统里的文件备份好。
我们现在进入Windows系统，下载 mbrfix ，进行解压等操作后，我们将MbrFix.exe复制到C盘下，然后打开运行，
输入 cmd ，在窗口內输入以下内容：

	cd \
	MbrFix /drive 0 fixmbr /yes

以上操作的意思分别是进入C盘，然后使用 MbrFix 删除grub引导。接下来我们就可以进入磁盘管理对Linux分区进行删除卷了。

注意删除grub引导这一步是很重要的，要是你直接对Linux分区进行删除卷而忘了删除引导，你重启后就会进入出现 grub> 的界面。
这时候你就不得不需要使用 PE 系统，然后进入PE系统后再进行删除grub引导操作。

# Other

以上内容主要包括使用U盘和硬盘两种方式安装Linux系统，以及如何卸载Linux系统。
通过以上的内容，希望对大家有一定的帮助，大家赶快加入Linux的大家庭吧。

