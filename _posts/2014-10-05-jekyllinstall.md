---
layout: post
title: Jekyll Install in Debian7
description: 今天讲一下我在Debian7上如何安装Jekyll以及在安装过程中发生的一些问题。首先我为什么要安装Jekyll，由于之前在发布Github站点时，没有注意语法细节，使得站点内容会发生一些错误，这时Github就会通过邮箱通知你，告诉你的站点存在一些问题。所以安装Jekyll主要用于调式我的Github站点。这里由于Debian7上的很多依赖包比较旧，所以在安装过程中也会出现一些问题。
---

今天讲一下我在Debian7上如何安装Jekyll以及在安装过程中发生的一些问题。
首先我为什么要安装Jekyll，由于之前在发布Github站点时，没有注意语法细节，
使得站点内容会发生一些错误，这时Github就会通过邮箱通知你，告诉你的站点存在一些问题。
所以安装Jekyll主要用于调式我的Github站点。这里由于Debian7上的很多依赖包比较旧，
所以在安装过程中也会出现一些问题。

## 一、安装前提

1、Ruby

2、RubyGems （便于安装Jekyll包）

3、NodeJS （JavaScript 运行时平台）

## 二、安装Ruby

通过命令行的方式安装 Ruby

	$ sudo apt-get install ruby1.9.1-dev

通过命令 `ruby -v` 检验是否安装成功 Ruby ，
我当前显示的是：ruby 1.9.3p194

## 三、安装RubyGems

RubyGems 是一个用于对 Ruby组件进行打包的 Ruby 打包系统，可以下载包到本地。

点击进入 [RubyGems](http://rubygems.org/pages/download) 下载页面，
下载 RubyGems 安装包，我当前下载的安装版本为 rubygems-2.4.1 ，解压安装包，
进入后可以看到一个名为 set.rb 的文件，执行命令安装 rubygems

	$ ruby setup.rb

更新到最新版本，执行以下命令

	$ sudo gem update --system

可能会发生如下错误:

ERROR:  While executing gem ... (Gem::RemoteFetcher::FetchError)
	Errno::ETIMEDOUT: Connection timed out - connect(2)
(https://api.rubygems.org/specs.4.8.gz)

如果需要更新可以通过VPN翻一下墙，当然不是最新版本也不是太大的问题

检查当前版本，可以看到版本升级到了 2.4.2

	$ gem -v

## 四、安装 Jekyll

官方推荐的安装方法是通过 RubyGems 来安装，刚刚我们已经安装好了 RubyGems ，
现在可以直接执行以下命令进行安装

	$ sudo gem install jekyll

安装完了之后，查看版本号，我当前的版本是 jekyll 2.4.0

	$ jekyll -v

在实际安装完成后，当我运行 `jekyll -v` 时会有报错，如下错误:

/usr/lib/ruby/gems/1.9.1/gems/execjs-2.2.1/lib/execjs/runtimes.rb:51:in `autodetect': Could not find a JavaScript runtime. See https://github.com/sstephenson/execjs for a list of available runtimes.

它告诉我没有安装 node.js （JavaScript 运行时平台），
如果你也遇到这样的问题可以继续往下看第五点，若没有这样的情况，则跳过。

## 五、安装 Node.js

方式一：通过命令行安装（我当时遇到的问题是不可以通过这种方式安装，
但我写博客时源中已经有了nodejs包）

	$ sudo apt-get install nodejs

方式二：通过下载源码安装
点击进入[nodejs](http://nodejs.org/download/)下载页面，
下载源码包后解压，在解压后的文件夹内执行三条命令

	$ ./configure
	$ make
	$ sudo make install

## 六、运行Jekyll
进入Github站点文件夹，执行以下命令

	$ jekyll server

如果你的站点有错误或者警告，就可以通过提示对站点内容慢慢地修正。

按照提示，打开浏览器，输入 http://0.0.0.0:4000/ 就可以看到我们的静态站点了。
大家动手试试吧。

使用这种方式调试页面，需要一个终端开着jekyll服务，
我们只需要修改文件保存好后，刷新网页就可以在看到改动的样子，
当改到我们满意为止就可以 push 到 Github 上了，所以还是比较方便的。

