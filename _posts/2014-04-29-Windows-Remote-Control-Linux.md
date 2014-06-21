---
layout: post
title: Windows下远程访问Linux
description: 在日常生活中，我们可能会遇到这样的情况，我们在外面学习或者工作，但却有访问自己机子的需求。这时，我们就可以使用SSH服务实现远程访问Linux服务器。在默认情况下，SSH的服务端口号为22。
---

在日常生活中，我们可能会遇到这样的情况，我们在外面学习或者工作，但却有访问自己机子的需求。
这时，我们就可以使用SSH服务实现远程访问Linux服务器。在默认情况下，SSH的服务端口号为22。

下面我将介绍两款简易的跨平台远程访问的工具，它们分别是Putty、SecureCRT。

## 一、使用工具的前提
在使用工具前，我们需要确保我们的Linux系统上开启了ssh-server服务。这也是一开始折腾我的地方。
一开始在我的Debian上并没有安装ssh-server服务，导致我使用工具时，总是无法成功访问。
按着下面的步骤开启ssh-server服务：

		apt-get install openssh-server(root)

由于Debian、Ubuntu上缺省安装了openssh-client，所以这里我不需要再安装了。
然后确认ssh-server是否启动：

		ps -e | grep ssh

如果看到"sshd"那说明ssh-server已经启动了。但如果只看到ssh-agent，你就需要手动开启服务了，如下：

		/etc/init.d/ssh start

## 二、使用Putty访问Linux系统
### 1.Download
我们可以到Putty的官网上下载[Putty](http://www.putty.org/)软件。
下载后就可以使用该软件，它是免安装的。打开后如下图所示：

![Putty login](/images/2014-04-29-1.png)

### 2.How to use
这里我们只需要填入你的主机名（IP地址）即可，完成后点击"Open"。
接着就是输入我们Linux系统的用户名和密码。o(∩∩)o...哈哈，怎么样，可以远程访问你的Linux系统啦！

### 3.How to configure
如果在你的终端上对中文并支持，并显示出乱码。那么你可以通过右击标题栏，然后选择"Change Settings"。在Window下的Translation的右边框选择UTF-8编码。

![Putty configure](/images/2014-04-29-2.png)

若不喜欢这里的字体，你也可以通过Window下的Appearance的右边框选择你喜欢的字体，这里推荐使用Courier New字体。

## 三、使用SecureCRT访问Linux系统
### 1.SecureCRT的文件传输功能
SecureCRT除了具备了终端仿真器的功能，还提供了文件传输功能。
我们只需要在终端输入"sz filename"或者"rz"就可以下载和上传文件了。
不过在这之前我们仍需要一些准备，就是下载相应的软件，如下：

		apt-get install lrzsz(root)

一切准备就绪后，我们就可以去官网下载并安装[SecureCRT](http://www.vandyke.com/download/securecrt/download.html/)。

### 2.How to use
这里我们需要填入我们的主机名和用户名，完成后点击"Connect"。
当你第一次登录的时候，他会弹出一个框框，问你是否保存该主机的信息。
选择Accept Once或者Accept & Save，接着会让我们输入密码，之后我们就可以顺利地访问Linux系统了。

![SecureCRT login](/images/2014-04-29-3.png)

### 3.How to configure
这里我们需要修改SecureCRT的全局设置，通过"Options -> Global Options -> General -> Default Session -> Edit Default Settings"。
#### 1)显示高亮
若需要高亮显示的话，你需要先进入全局设置，再通过"Terminal -> Emulation"的右边框。
其中Terminal选择为Xterm，并将"ANSI Color"、"Use color scheme"、"Select an alternate keyboard emulation"打钩。

![SecureCRT configure](/images/2014-04-29-4.png)

#### 2)设置字体及字符编码
若需要的话，你需要先进入全局设置，通过"Terminal -> Appearance"的右边框。
下面是我个人的配置，其中将"Current color scheme"设置为"White / Black"，"Normal font"设置为"Courier New 12pt"。对于字符编码的修改，一般地将"Character encoding"设置为"UTF-8"即可。

![SecureCRT configure](/images/2014-04-29-5.png)

#### 3)上传和下载文件
在上传和下载文件之前我们最好先修改一下默认的文件路径，在"Options -> Session Options -> Terminal -> Appearance -> X/Y/Zomdemm"的右边框。
修改Directories下的Upload和Download即可。

对于上传文件，我们只需要将上传的文件放到我们所设置的"Upload"文件夹下，在终端输入"rz"。
通过选择文件并点击"Add"和"OK"，就可以将文件传送到Linux系统该用户的根目录下。

对于下载文件，我们只需要输入"sz filename"，就可以在当前Linux下的目录的文件下载到我们所设置的"Download"文件夹下。

## Other
Putty为一开放源代码软件，主要由Simon Tatham维护，使用MIT licence。
Putty是一个免费的、Windows 32平台下的telnet、rlogin和ssh客户端。

SecureCRT是一款支持SSH（SSH1和SSH2）的终端仿真程序，简单的说是Windows下登录UNIX或Linux服务器主机的软件。
SecureCRT支持SSH，同时支持Telnet和rlogin协议。SecureCRT是一款用于连接运行包括Windows、UNIX和VMS的理想工具。
