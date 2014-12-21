---
layout: post
title: Build Learning Environment of Raspberry Pi
description: 
---

今天从淘宝买来的 Raspberry Pi
（树莓派）到手了，买了一块树莓派的板子、一块16G的闪迪TF卡和两块纯铜散热片，
先给大家来几张靓照吧！！


![Raspberry1](/images/2014-12-21-1.jpg)
![Raspberry2](/images/2014-12-21-2.jpg)
![Raspberry3](/images/2014-12-21-3.jpg)

在拿到树莓派之前，我就给自己制定了三套方案，用于解决没有USB无线网卡和显示器的问题。
主要原因是这样的，由于没有USB无线网卡，那么我就不能在我的树莓派直接上网，那么更新和下载软件都会成为一定的问题；另一方面，由于没有显示器，那么我可能就不能了解到我的树莓派是否在正常运作，就像写了程序没有输出一样。
下面我将主要讲述一种对于我个人最实用的方案，该方案同样适用于以Ubuntu或Debian系列的PC机。

## 第一步：简述学习材料及实现原理

### 1、学习材料：
一块树莓派板子、一块SD卡、一根网线、一台带双网卡的笔记本电脑、一个5V/1A的充电器

### 2、实现原理：
先将笔记本通过WIFI连接到互联网，然后将无线网卡的互联网资源共享给本地连接。
本地通过网线将资源传给树莓派实现上网功能。通过nmap扫描局域网内所有主机IP地址和MAC地址，
找到树莓派的IP地址；通过ssh访问树莓派，实现对树莓派的控制。


## 第二步：安装Raspbian系统

### 1、下载Raspbian系统镜像

附上树莓派的官方网站的[下载地址](http://www.raspberrypi.org/downloads/)，
去下载一个系统镜像，推荐下载Raspbian。

下载完成后解压，得到一个img镜像

	guido@debian:~/pi$ ls
	2014-09-09-wheezy-raspbian.img  2014-09-09-wheezy-raspbian.zip

### 2、插入SD卡，查看当前已挂载的卷

输入以下命令查看当前已挂载的卷

	sudo fdisk -l

以下是硬盘和SD卡的文件系统的内容

Disk /dev/sda: 1000.2 GB, 1000204886016 bytes  
255 heads, 63 sectors/track, 121601 cylinders, total 1953525168 sectors  
Units = sectors of 1 * 512 = 512 bytes  
Sector size (logical/physical): 512 bytes / 4096 bytes  
I/O size (minimum/optimal): 4096 bytes / 4096 bytes  
Disk identifier: 0x9c0c0766

Disk /dev/mmcblk0: 15.9 GB, 15931539456 bytes  
4 heads, 16 sectors/track, 486192 cylinders, total 31116288 sectors  
Units = sectors of 1 * 512 = 512 bytes  
Sector size (logical/physical): 512 bytes / 512 bytes  
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x00000000

这里可以明显的看到“/dev/sda: 1000.2 GB” 是我的硬盘，而“/dev/mmcblk0: 15.9 GB”是SD卡。

### 3、卸载SD卡对应的分区

这里我们要先对SD卡的分区进行卸载，如果有多个分区都是要卸载的

	sudo mount /dev/mmcblk0p1
	sudo mount /dev/mmcblk0p2
	sudo mount /dev/mmcblk0

### 4、写入系统镜像

在执行写入命令前先给大家讲点小内容，这里需要特别注意。
对于 dd 命令，参数 if 是 read from file ，即读入的文件， of 则是 write to file ，
即写入的文件。参数 bs 是设置读写入的缓冲块的大小，即一次性读出和写入的大小（默认为512Byte）。

### *注意：of后面是SD卡整个文件设备的地址，而不是某个分区的地址，如应填写/dev/mmcblk0，而不应填写/dev/mmcblk0p1

	dd bs=2M if=2014-09-09-wheezy-raspbian.img of=/dev/mmcblk0

这里需要等待几分钟，当然你也可以通过以下命令查看写入的进度。

#### 1)新打开一个终端，输入以下命令

	ps -ef | grep dd

你可以看到关于 dd 的执行进程的ID号，如下：

	root     14530 13683  1 23:15 pts/0    00:00:01 dd bs=2M 
	if=2014-09-09-wheezy-raspbian.img of=/dev/mmcblk0

#### 2)输入以下命令，可以在之前的终端上显示出写入的进度

	kill -USR1 14530

在之前输入 dd 命令的终端上显示了以下内容

	898+0 records in
	898+0 records out
	1883242496 bytes (1.9 GB) copied, 133.217 s, 14.1 MB/s

当然上面有些是题外话了。当出现下面的提示，说明SD卡刷好了，也可以说我们已经装好Raspberry系统了。

	1562+1 records in
	1562+1 records out
	3276800000 bytes (3.3 GB) copied, 362.106 s, 9.0 MB/s

## 第三步：通过无线网卡的互联网资源共享给本地连接


### 1、将刚制作好的TF卡插入树莓派
### 2、将网线两端分别连接电脑和树莓派
### 3、插上电源，此时红灯亮，绿灯闪烁，树莓派和笔记本的网线接口灯都是闪烁
### 4、笔记本连接无线WIFI，并且可以正常上网
### 5、在Debian系统上（笔记本上）找到 Network Connections 工具
打开时显示在 Wired 界面，点击 Add 按钮添加新的 Wired connection，对于
Connection name 可以任取，找到 IPv4 Setting 标签，选择 “Shared to other
computers” 的 Method。好了，可以点击保存了。
### 6、


我在网上找了好久关

这次得要小小鼓励一下自己，当时
