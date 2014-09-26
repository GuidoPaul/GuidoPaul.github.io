---
layout: post
title: 为你的GitHub Pages添加DisQus评论系统
description: 之前的一段时间开始入门HTML和CSS，就琢磨着自己尝试做个网页，经同学的介绍，GitHub可以搭建免费的静态网页，本人就开始自己尝试着去接触一下。乘着今天课程不多，抽出时间了解了一下博客下端的评论系统。大家可以看一下现在博客的下面的评论系统，它就是我今天的学习成果了。它叫 DisQus ，怎么样，不错吧。
---

之前的一段时间开始入门HTML和CSS，就琢磨着自己尝试做个网页，经同学的介绍，
GitHub可以搭建免费的静态网页，本人就开始自己尝试着去接触一下。
乘着今天课程不多，抽出时间了解了一下博客下端的评论系统。
大家可以看一下现在博客的下面的评论系统，它就是我今天的学习成果了。
它叫 DisQus ，怎么样，不错吧。

## Let's start
	
### 第一步：注册帐号
首先我们需要一个DisQus的帐号，这是必须的。你可以点击下面的链接进入官网。
<br />
[DisQus](http://disqus.com/)
<br />
点击右下角的 "Add Disqus to Your Site"
![DisQus's official website](/images/2014-02-26-1.png)
<br />
需要填写你的Username，Email address，Password。

### 第二步：填入相关信息
我们会默认进入一个添加你的站点的页面，你需要填写你的Site name 和 DisQus URL，
其中 Disqus URL 中填写的内容将成为你的“shortname”，之后可能会需要它。
<br />
![Site profile](/images/2014-02-26-2.png)
<br />

### 第三步：嵌入代码
与其说是安装DisQus，倒不如直接说是一种代码的嵌入。
经过以上步骤的填写，我们会看到以下页面。
<br />
![Choose your platform](/images/2014-02-26-3.png)
<br />
因为我们是在GitHub上添加评论系统，最好的方式应该就是选择代码了，即“Universal Code”。
选择之后，我们就看到两串js的代码，上面的一段就是我们需要的评论系统的代码。
按照提示，我们只需要将这些代码放到每篇博客下的下面就行了，
但在GitHub上有页面模板的概念，你只需要你将这些代码完整的放到_layout/post.html下就好了。
<br />
![Universal Code](/images/2014-02-26-4.png)
<br />

其中需要注意的是下面这行代码的引号中需要写入刚才提到的“shortname”。
<br />

	var disqus_shortname = 'shenglin';

### 第四步：神奇的事情就这么发生了
当我们上传我们的修改后，重新刷新我们的博客网站，评论系统就这么出现了。
怎么样，对于我这个还没怎么接触js的初学者来说，这已经算比较神奇了。

## Other
Disqus的社会化登录功能可以让你很方便的使用 Facebook，Twitter等社会化网站的账号进行评论，
形成真正的内容创建者和访问者之间的社会化交互。
Disqus可以使用在多个平台上，包括主流的Wordpress及Joomla,Dupral等等系统，
你还可以通过他们的API开发出适合自己网 站系统的评论插件。
还有一些国内的评测系统，如果打开DisQue出现延迟等情况，不妨试试
[友言](http://www.uyan.cc/)、[多说](http://duoshuo.com/)等评论系统。

