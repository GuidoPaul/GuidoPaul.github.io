---
layout: post
title: Build Learning Environment of Raspberry Pi
description: 今天从淘宝买来的 Raspberry Pi（树莓派）到手了，买了一块树莓派的板子、一块16G的闪迪TF卡和两块纯铜散热片，先给大家来几张靓照吧！！
---

今天从淘宝买来的 Raspberry Pi（树莓派）到手了，买了一块树莓派的板子、
一块16G的闪迪TF卡和两块纯铜散热片，先给大家来几张靓照吧！！


![Raspberry1](/images/2014-12-21-1.jpg)
![Raspberry2](/images/2014-12-21-2.jpg)
![Raspberry3](/images/2014-12-21-3.jpg)

在拿到树莓派之前，我就给自己制定了三套方案，用于解决没有USB无线网卡和显示器的问题。
主要原因是这样的，由于没有USB无线网卡，那么我就不能在我的树莓派直接上网，
那么更新和下载软件都会成为问题；另一方面，由于没有显示器，
那么我可能就不能了解到我的树莓派是否在正常运作，就像写了程序没有输出一样。
下面我将主要讲述一种对于我个人最实用的方案，该方案同样适用于以Ubuntu或Debian系列的PC机。

## 第一步：简述学习材料及实现原理

### 1、学习材料：
一块树莓派板子（B+）、一张TF卡（有卡套）、一根网线、一台笔记本电脑（带双网卡、Debian系统）、一个5V/1A的充电器

### 2、实现原理：
先将笔记本通过WIFI连接到互联网，然后将无线网卡的互联网资源共享给本地连接，
本地通过网线将资源传给树莓派实现上网功能；通过nmap扫描局域网内所有主机IP地址和MAC地址，
找到树莓派的IP地址；通过ssh访问树莓派，实现对树莓派的控制。


## 第二步：安装Raspbian系统

### 1、下载Raspbian系统镜像

附上树莓派的官方网站的[下载地址](http://www.raspberrypi.org/downloads/)，
去下载一个系统镜像，推荐下载Raspbian。

下载完成后解压，得到一个img镜像

2014-09-09-wheezy-raspbian.img

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
	sudo mount /dev/mmcblk0  //可能会提示原本就未挂载

### 4、写入系统镜像

在执行写入命令前先给大家讲点小内容，这里需要特别注意。
对于 dd 命令，参数 if 是 read from file，即读入的文件；参数 of 则是 write to file，
即写入的文件；参数 bs 是设置读出和写入的缓冲块的大小，即一次性读出和写入的大小（默认为512Byte）。

### *注意：of后面是SD卡整个文件设备的地址，而不是某个分区的地址；

### *如应填写/dev/mmcblk0，而不应填写/dev/mmcblk0p1

执行写入命令

	sudo dd bs=2M if=2014-09-09-wheezy-raspbian.img of=/dev/mmcblk0

这里需要等待几分钟，当然你也可以通过以下操作查看写入的进度。

#### 1)新打开一个终端，输入以下命令

	ps -ef | grep dd

你可以看到关于 dd 的执行进程的ID号，如下：

root     14530 13683  1 23:15 pts/0    00:00:01 dd bs=2M if=2014-09-09-wheezy-raspbian.img of=/dev/mmcblk0

#### 2)输入以下命令可以在之前输入 dd 命令的终端上显示出写入的进度

	sudo kill -USR1 14530

查看之前输入 dd 命令的终端，显示了以下内容

898+0 records in  
898+0 records out  
1883242496 bytes (1.9 GB) copied, 133.217 s, 14.1 MB/s

当然上面有些是题外话了。当自动出现下面的提示，说明SD卡刷好了，也可以说我们已经装好Raspberry系统了。

1562+1 records in  
1562+1 records out  
3276800000 bytes (3.3 GB) copied, 362.106 s, 9.0 MB/s

## 第三步：通过无线网卡的互联网资源共享给本地连接

### 1、将刚制作好的TF卡插入树莓派

### 2、将网线两端分别连接电脑和树莓派

### 3、插上电源，此时红灯常亮，绿灯闪烁，树莓派和笔记本的网线接口灯都是闪烁

### 4、笔记本连接无线WIFI，并且可以正常上网

### 5、在Debian系统上（笔记本上）找到 Network Connections 工具

![Network Connections](/images/2014-12-21-4.png)

打开时显示在 Wired 界面，点击 Add 按钮添加新的 Wired connection，对于
Connection name 可以任取，找到 IPv4 Setting 标签，选择 “Shared to other
computers” 的 Method。

![Network Connections](/images/2014-12-21-5.png)

好了，可以点击保存了。

### 6、连接上刚创建的Wired

细心的朋友可能看到了右上角显示的连接状态依然还是无线状态的，
这是因为我们刚才设置的有线连接是面向本地的接口，而非连接外网的设备接口。

### 7、寻找树莓派IP地址

1) 首先知道 PC 上 eth0 的IP，即刚才连接的Wired的IP

	sudo ifconfig

显示如下内容：

eth0 &emsp; Link encap:Ethernet  HWaddr 20:89:84:22:b7:81  
&emsp;&emsp;&emsp;&emsp;inet addr:10.42.0.1  Bcast:10.42.0.255  Mask:255.255.255.0  
&emsp;&emsp;&emsp;&emsp;inet6 addr: fe80::2289:84ff:fe22:b781/64 Scope:Link  
&emsp;&emsp;&emsp;&emsp;UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1  
&emsp;&emsp;&emsp;&emsp;RX packets:1347 errors:0 dropped:1 overruns:0 frame:0  
&emsp;&emsp;&emsp;&emsp;TX packets:2495 errors:0 dropped:0 overruns:0 carrier:7  
&emsp;&emsp;&emsp;&emsp;collisions:0 txqueuelen:1000   
&emsp;&emsp;&emsp;&emsp;RX bytes:175846 (171.7 KiB)  TX bytes:231269 (225.8 KiB)  
&emsp;&emsp;&emsp;&emsp;Interrupt:16

从以上内容我们可以了解到当前 eth0 的 IP 为 10.42.0.1，那么我们就对 10.42.0.0/24 
的所有主机进行 ping 扫描，执行下面的语句

	nmap -sP 10.42.0.0/24

显示内容如下

Starting Nmap 6.00 ( http://nmap.org  ) at 2014-12-22 12:51 CST  
Nmap scan report for 10.42.0.1  
Host is up (0.00028s latency).  
Nmap scan report for 10.42.0.17  
Host is up (0.0010s latency).  
Nmap done: 256 IP addresses (2 hosts up) scanned in 2.33 seconds


我们可以看到除了 10.42.0.1 的 IP 以外还有一个 10.42.0.17 的 IP 是可以 ping
通的。对的，就是它了。

### 8、通过 ssh 连接树莓派

	ssh pi@10.42.0.17

#### 1)成功登录
如果提示`Are you sure you want to continue connecting (yes/no)?`

输入`yes`，接着会提示输入密码，首次进入的密码默认是 `raspberry` 。

当然你可以通过输入`sudo raspi-config`进行配置，配置中可以修改你的 pi 用户的密码。

#### 2)出现问题
如果你并未得到以上的提示，而是得到以下的提示

@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@  
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @  
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@  
IT IS POSSIBLE THAT SOMEONE IS DOING SOMETHING NASTY!  
Someone could be eavesdropping on you right now (man-in-the-middle attack)!  
It is also possible that a host key has just been changed.  
The fingerprint for the ECDSA key sent by the remote host is  
68:58:4a:90:fc:9a:69:22:76:d6:33:62:52:bd:77:c1.  
Please contact your system administrator.  
Add correct host key in /home/guido/.ssh/known_hosts to get rid of this message.  
Offending ECDSA key in /home/guido/.ssh/known_hosts:14  
ECDSA host key for 10.42.0.17 has changed and you have requested strict checking.  
Host key verification failed.

通过提示，你可以删除 ~/.ssh/known_hosts 下的第 14 行，当然你直接将
~/.ssh/known_hosts 文件删除应该也是可以的。

好了，试着再执行一遍 `ssh pi@10.42.0.17` 的命令。

## 总结

记得前几天不知道什么原因突然想起来想玩单片机，淘宝上找了找树莓派。当天晚上，专业主任又正好提到了 Arduino，
当时兴趣有就来了，没有把时间花在纠结要不要买的问题上。这次得要小小鼓励一下自己，
在网上了好多教程以及国外的论坛上都没有找到关于在Ubuntu和Debian系列上解决在没有无线USB和没有显示器的情况下，
控制树莓派的问题的方法，自己通过慢慢地琢磨终于把它解决了。希望对树莓派爱好者们有一定的帮助。
