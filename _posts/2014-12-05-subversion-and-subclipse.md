---
layout: post
title: Set up SVN development environment in Debian
description: 在团队的软件开发过程中，难免会遇到软件开发版本或共享等问题。前些天经学长的推荐，建议我使用SVN或者Git作为版本控制工具。然后我结合我的实际情况，在我的Debian上安装了Subversion服务器端以及在MyEclipse上安装了Subclipse客户端工具。然后通过局域网，让我的另一个团队成员在Windows上使用Tortoise svn做了一些简单的测试。下面是我的折腾后经历总结。
---
在团队的软件开发过程中，难免会遇到软件开发版本或共享等问题。前些天经学长的推荐，建议我使用SVN或者Git
作为版本控制工具。然后我结合我的实际情况，在我的Debian上安装了Subversion服务器端以及在MyEclipse上安装了
Subclipse客户端工具。然后通过局域网，让我的另一个团队成员在Windows上使用Tortoise svn做了一些简单的测试。
下面是我的折腾后经历总结。

首先让我们在安装软件之前先更新一下源
sudo apt-get update
我们需要安装subversion,subversion-tools,libapache2-svn三个软件
sudo apt-get install subversion subversion-tools libapache2-svn
现在需要创建一个目录用来储存我们的站点,我选择的目录在/home/svn.
sudo mkdir /home/svn
创建一个测试用的存储库
sudo svnadmin create /home/svn/repos
进入我们的测试用的存储库，我们可以看到包含以下文件
conf db format hooks locks README.txt
我们需要对conf下的三个文件进行修改，它们分别是
authz passwd svnserve.conf
sudo vim conf/svnserve.conf
sudo vim conf/passwd
sudo vim conf/authz
需要注意的是：把下面出现的语句前面的'#'去掉，然后左对齐，左边不能有空格

svnserve.conf
[general]
anon-access = read
auth-access = write
password-db = passwd
authz-db = authz
realm = /home/svn/repos

passwd
[users]
# harry = harryssecret
# sally = sallyssecret
bao = bao
hong = hong

authz
[groups]
# harry_and_sally = harry,sally
# harry_sally_and_joe = harry,sally,&joe
ggroup = bao,hong

[/]
@ggroup = rw

[repos:/]
@ggroup = rw

使svn服务启动起来
svnserve -d -r /home/svn/repos

若出现 svnserve: Can't bind server socket: Address already in use
说明svnserve 服务已经启动，可以ps -ef | grep
svnserve,再使用kill命令杀死进程,重新启动

开机启动
编辑 vi /etc/rc.local 文件
在文件最后加入 svnserve -d -r /home/svn/repos即可


Configuring Apache
sudo a2enmod dav_svn
cd /etc/apache2
sudo vim apache2.conf
<Location "/svn">
    DAV svn
    SVNParentPath /home/svn
    AuthType Basic
    AuthName "GongXue"
    AuthUserFile /etc/svn-auth
    Require valid-user
</Location>
htpasswd -cm /etc/svn-auth bao
htpasswd -m /etc/svn-auth hong
sudo service apache2 restart
http://127.0.0.1/svn/GongXue/


cd ~
mkdir test
cd test
mkdir src
cd src
touch abc
svn import -m "init" ~/test file:///home/svn/GongXue

如果遇到
svn: 不能打开文件“/home/svn/repos/db/txn-current-lock”：权限不够
主要我们在创建时使用的是root用户，且当前db的权限不高造成的。
我们的解决方案是修改用户和用户组，修改储存库权限
sudo chown -R www-data:www-data /home/svn/repos
sudo chmod -R 777 /home/svn/repos/db

cd ~
svn checkout svn://127.0.0.1 ~/test2
svn checkout svn://127.0.0.1 ~/test3   #checkout 可缩写为 co
需要输入之前在passwd填写的用户名和密码

现在对test2/src里的abc添加内容,进行提交
svn commit -m "note"

进入test3目录，对项目进行更新
svn update

实现了以上的基本操作，我们主要目的是在MyEclipse能对项目直接进行版本控制
MyEclipse 里安装 svn 插件，我选择的是Subclipse

由于当前Debian上的Subversion的版本是1.6的，所以最好是选择1.6.x版本的Subclipse进行安装。
当然本人已经试过当前的最新版本1.10.x，以下是我出现的错误
打开SVN Repositories
Window -> show View -> SVN Repositories
打开SVN Repositorie时会出现以下的错误
Failed to load JavaHL Library.
These are the errors that were encountered:
no libsvnjavahl-1 in java.library.path
no svnjavahl-1 in java.library.path
no svnjavahl in java.library.path
java.library.path = /usr/lib/jni

这个问题比较明显，只要安装下JavaHL库就行了

安装命令为：sudo apt-get install libsvn-java

之后我们需要找到libsvnjavahl-1.so

sudo find /usr -name libsvnjavahl-1.so
我们会的到一个<path>
在MyEclipse的安装目录下找到myeclipse.ini, 或者是eclipse安装目录下的eclipse.ini
在-vmargs下添加以下内容：
-Djava.library.path=<path>
例如：
-Djava.library.path=/usr/lib/jni
或者
-Djava.library.path=/usr/lib/x86_64-linux-gnu/jni/ 
-Djava.library.path=/usr/lib/i386-linux-gnu/jni/ 
修改完成之后重启MyEclipse，但是还是出现了一些问题
Incompatible JavaHL library loaded.  1.8.x or later required.
所以我建议大家还是安装Subclipse1.6.x
下载地址为
http://subclipse.tigris.org/servlets/ProjectDocumentList?folderID=2240

大家选择site-1.6.18.zip，然后在MyEclipse里的Install from Site中安装这个插件

之后大家就可以慢慢摸索在MyEclise使用svn插件了。

