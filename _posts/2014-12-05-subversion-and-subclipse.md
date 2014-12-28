---
layout: post
title: Set up SVN development environment in Debian
description: 在团队的软件开发过程中，难免会遇到软件开发版本或共享等问题。前些天经学长的推荐，建议我使用SVN或者Git作为版本控制工具。然后我结合我的实际情况，在我的Debian上安装了Subversion服务器端以及在MyEclipse上安装了Subclipse客户端工具。然后通过局域网，让我的另一个团队成员在Windows上使用Tortoise svn做了一些简单的测试。下面是我折腾后的经历总结。
---
在团队的软件开发过程中，难免会遇到软件开发版本或共享等问题。前些天经学长的推荐，建议我使用SVN或者Git
作为版本控制工具。然后我结合我的实际情况，在我的Debian上安装了Subversion服务器端以及在MyEclipse上安装了
Subclipse客户端工具。然后通过局域网，让我的另一个团队成员在Windows上使用Tortoise svn做了一些简单的测试。
下面是我折腾后的经历总结。

## 一、安装服务器端Subversion

首先让我们在安装软件之前先更新一下源

	sudo apt-get update

我们需要安装subversion，subversion-tools，libapache2-svn三个软件

	sudo apt-get install subversion subversion-tools libapache2-svn

## 二、配置服务器端Subversion

现在需要创建一个目录用来储存我们的站点，我选择的目录在/home/svn

	sudo mkdir /home/svn

创建一个名为 repos 的测试用的存储库

	sudo svnadmin create /home/svn/repos

进入我们的测试用的存储库，我们可以看到包含以下文件及目录

conf/ db/ format hooks/ locks/ README.txt

我们需要对 conf/ 下的三个配置文件进行修改，它们分别是

svnserve.conf &emsp;&emsp; passwd &emsp;&emsp; authz

	sudo vim conf/svnserve.conf
	sudo vim conf/passwd
	sudo vim conf/authz

### *需要注意的是：把下面出现的语句前面的'#'去掉，然后左对齐，最左端不能有空格

1) svnserve.conf

[general]  
anon-access = none  
auth-access = write  
password-db = passwd  
authz-db = authz  
realm = /home/svn/repos  

2) passwd  

[users]  
# harry = harryssecret  
# sally = sallyssecret  
bao = bao  
hong = hong  

3) authz  

[groups]  
# harry_and_sally = harry,sally  
# harry_sally_and_joe = harry,sally,&joe  
ggroup = bao,hong  

[/]  
@ggroup = rw  

[repos:/]  
@ggroup = rw  

配置完成之后，我们需要启动svn服务器，3690为svn的默认端口号

	svnserve -d -r /home/svn/repos

### *错误： svnserve: Can't bind server socket: Address already in use

说明svnserve 服务已经启动，可以 ps aux | grep svnserve ，再使用 kill -9
命令杀死进程，重新启动svn服务器

若需要设置开机自启动svn服务器，需要编辑 /etc/rc.local 文件

	sudo vim /etc/rc.local

在文件最后加入 “svnserve -d -r /home/svn/repos” 即可

## 三、配置apache加载subversion模块

若未自动加载subversion模块，可运行下面命令加载

	sudo a2enmod dav_svn

在修改apache2中配置 /etc/apache2/apache2.conf

	sudo vim /etc/apache2/apache2.conf

在 apache2.conf 的最后加上以下内容

<Location "/svn">  
&emsp;&emsp;DAV svn  
&emsp;&emsp;SVNParentPath /home/svn  
&emsp;&emsp;AuthType Basic  
&emsp;&emsp;AuthName "repos"  
&emsp;&emsp;AuthUserFile /etc/svn-auth  
&emsp;&emsp;Require valid-user  
</Location>  

为登录添加帐号及密码，帐号密码同之前在 conf/ 下设置的 passwd 无关

	htpasswd -cm /etc/svn-auth bao
	htpasswd -m /etc/svn-auth hong

其中 -c 参数表示创建 passwdfile，-m 参数表示使用md5摘要保存密码

现在启动apache服务

	sudo service apache2 restart

启动成功后我们可以使用常用浏览器，在本地登录 http://127.0.0.1/svn/repos/
查看我们的项目（需要使用帐号密码），也可更换本机IP地址登录。


## 四、模拟开发环境

整体的规划是将 test 项目导入到存储库中，然后从存储库中导入项目到 test2 和 test3 中，
然后对 test2 中的项目内容进行修改，提交到存储库中，然后在 test3 中更新存储库的内容。

我们先创建一个名为 test 的测试用的项目

	cd ~
	mkdir test

进入 test 项目目录，我们可以将一些原有的项目解压到该目录下，
下面只做了简单的模拟开发环境

	cd test
	mkdir src
	cd src
	touch abc

现在比较关键一步就是将我们的 test 项目导入到之前创建的 repos 存储库

	svn import -m "init" ~/test file:///home/svn/repos

其中 -m 参数表示在后面的双引号内添加注释，这是一个开发时的良好的习惯。

### *错误：svn: 不能打开文件“/home/svn/repos/db/txn-current-lock”：权限不够

原因是我们在创建存储库时使用的是root用户，造成访问 /home/svn/repos/db 的权限不够。

我们的解决方案是修改存储库的用户和用户组，并且修改存储库 /home/svn/repos/db 权限

	sudo chown -R www-data:www-data /home/svn/repos
	sudo chmod -R 777 /home/svn/repos/db

接着我们要将存储库里的项目导出来，test2 和 test3
文件夹不需要事先创建好，即会自动创建。

	cd ~
	svn checkout svn://127.0.0.1 ~/test2
	svn checkout svn://127.0.0.1 ~/test3   #checkout 可缩写为 co

需要输入之前在 passwd 中填写的用户名和密码

在 Linux 下可直接使用命令 svn checkout ，在Windows下可安装svn客户端软件 Tortoise svn ，
通过右键菜单中的 checkout 做到相同的效果。

现在对 test2/src/ 里的 abc 添加内容，再进行提交

	vim ~/test2/abc
	svn commit -m "modifier abc"

进入 test3 目录，对项目进行更新

	cd ~/test3/
	svn update

实现了以上的基本操作，那么说明我们的 Subversion 服务器端已经没有没什么问题了。

## 五、安装 Subclipse 客户端插件

现在我们的主要目的是在MyEclipse能对项目直接进行版本控制，
我选择的 MyEclipse 里的svn插件是Subclipse

### *注意：由于当前Debian上的Subversion的版本是1.6的，所以建议最好是选择1.6.x版本的Subclipse进行安装。

当然本人已经试过当前的最新版本1.10.x，以下是我出现的错误

打开 MyEclipse 中的 SVN Repositories

Window -> show View -> SVN Repositories

打开SVN Repositorie时会出现以下的错误

Failed to load JavaHL Library.  
These are the errors that were encountered:  
no libsvnjavahl-1 in java.library.path  
no svnjavahl-1 in java.library.path  
no svnjavahl in java.library.path  
java.library.path = /usr/lib/jni  

这个错误告诉我们需要安装 JavaHL 库，执行安装命令

	sudo apt-get install libsvn-java

之后我们需要找到 libsvnjavahl-1.so ，执行查找命令

	sudo find /usr -name libsvnjavahl-1.so

我们会的到一个 \<path\> ，在MyEclipse的安装目录下找到 myeclipse.ini ，
或者是eclipse安装目录下的 eclipse.ini

### *注意：在-vmargs下添加以下内容：

	-Djava.library.path=<path>

例如：

-Djava.library.path=/usr/lib/jni

或者

-Djava.library.path=/usr/lib/x86_64-linux-gnu/jni/ 

-Djava.library.path=/usr/lib/i386-linux-gnu/jni/ 

修改完成之后重启MyEclipse，但是还是出现了一些问题

### *错误：Incompatible JavaHL library loaded.  1.8.x or later required.

所以我建议大家还是安装 Subclipse1.6.x ，下载地址为
http://subclipse.tigris.org/servlets/ProjectDocumentList?folderID=2240

大家应选择site-1.6.18.zip，然后在 MyEclipse 里的 Install from Site 中安装这个插件

好了，接下来大家就可以尝试着对新的项目进行版本控制开发了。祝大家成功！

