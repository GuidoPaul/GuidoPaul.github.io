---
layout: post
title: Compile and Execute Java In Linux
description: 学习Java已经有一段时间，本人比较习惯于在Linux的环境下使用Vim编辑器编写Java程序，并且使用命令行的方式编译和运行Java程序。今天比较完整地阐述一下如何在Linux下编译和运行Java。
---

学习Java已经有一段时间，本人比较习惯于在Linux的环境下使用Vim编辑器编写Java程序，并且使用命令行的方式编译和运行Java程序。
今天比较完整地阐述一下如何在Linux下编译和运行Java。

# 一、官网下载JDK
Java SDK最早名字叫做JavaSoftware Develop Kit，后来改名为JDK，即Java DevelopKit。
JDK作为Java开发工具包，主要用于构建在Java平台上运行的应用程序、Applet 和组件等。
下载和安装JDK的同时安装上JRE，JRE即Java RuntimeEnvironment，是Java运行环境。
现在的最新版本是Jdk8，点击进入[官网](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)下载。

# 二、安装JDK
### 1.在/usr/local下创建java文件夹

		sudo mkdir /usr/local/java
		
### 2.将下载得到的 .tar.gz 压缩文件解压到 /usr/local/java目录，执行如下命令。

		// 进入下载目录
		cd ~/Download
		// 移动 .tar.gz 压缩文件到 /usr/localjava
		sudo mv jdk-8u5-linux-x64.tar.gz /usr/local/java/
		// 切换到 /usr/local/java 文件夹
		sudo cd /usr/local/java/
		// 解压缩 .tar.gz 压缩文件
		sodo tar -zxf jdk-8u5-linux-x64.tar.gz
		// 删除 .tar.gz 压缩文件
		sudo rm jdk-8u5-linux-x64.tar.gz

# 三、配置环境变量
### 1.修改系统配置文件

		sudo vim /etc/profile
	
### 2.在 /etc/profile 文件最后添加

		#set java environment
		JAVA_HOME=/usr/local/java/jdk1.8.0_05
		export CLASSPATH=.:$JAVA_HOME/lib:$CLASSPATH
		export PATH=$JAVA_HOME/bin:$PATH

## 注意:输出PATH(export PATH=)时千万不要漏写了 :$PATH ，它是系统环境变量的路径，命令行所执行命令的路径都写在PATH路径里面，如果修改错了，那么会有很严重的后果。

### 3.使文件生效，并测试JDK版本

		source /etc/profile
		java -version

如果显示如下内容，则表示安装成功了

		java version "1.8.0_05"
		Java(TM) SE Runtime Environment (build 1.8.0_05-b13)
		Java HotSpot(TM) 64-Bit Server VM (build 25.5-b02, mixed mode)

# 四、编译和运行Java文件
### 前提: 以下所有 Java 示例文件在 JavaDemo 文件下运行
### 知识点: java的类的全名: 全限定名 = 包名 + 类名

### 1.单独运行JavaDemo1.java程序

Java示例程序 JavaDemo1.java 如下：

		public class JavaDemo1 {
			public static void main(String[] args) {
				System.out.println("Hello Java");
			}
		}

方式一：

		// 编译JavaDemo1.java生成JavaDemo1.class文件
		javac JavaDemo1.java
		// 运行JavaDemo1程序
		java JavaDemo1

方式二：

		// 在JavaDemo文件夹下创建 bin 文件夹，用于存放class文件
		mkdir bin
		// 指定路径生成可执行文件
		javac -d bin JavaDemo1.java
		// 运行JavaDemo1程序
		java -cp bin/ JavaDemo1

解释一下参数

-d :directory, 指定类文件的目标目录

-cp:即-classpath, 调用指定路径的可执行文件

### 2.带包的一个java主程序
Java示例程序 JavaDemo2.java 如下：

		package j2se;

		public class JavaDemo2 {
			public static void main(String[] args) {
				System.out.println("Hello Java");
			}
		}

如果还是采用方式一的方法, 编译运行该Java程序就会出现 NoClassDefFoundError 的报错,
可行的方法如下:

方式三：

		// 指定路径生成可执行文件
		javac -d bin JavaDemo2.java
		// 运行JavaDemo2程序
		java -cp bin/ j2se.JavaDemo2

方式四：

		// 临时修改CLASSPATH，仅在当前终端窗口上有效
		export CLASSPATH=.:./bin
		// 指定路径生成可执行文件
		javac -d bin JavaDemo2.java
		// 运行JavaDemo2程序
		java j2se.JavaDemo2

### 3.项目举例：
项目内容：检查点是否在圆內

Point.java 程序代码如下:

		package com.circle;

		public class Point {
			int x, y;
			
			Point() {

			}
			Point(int x, int y) {
				this.x = x;
				this.y = y;
			}

			public double distance(Point other) {
				return Math.sqrt((x - other.x) * (x - other.x) + (y - other.y) * (y - other.y));
			}
		}

Circle.java 程序代码如下:

		package com.circle;

		public class Circle {
			Point center;
			double r;

			public Circle() {

			}
			public Circle(Point center, double r) {
				this.center = center;
				this.r = r;
			}

			public boolean contains(Point p) {
				return p.distance(center) < r;
			}
		}

CircleDemo.java 程序代码如下:

		package com.circle;

		public class CircleDemo {
			public static void main(String[] args) {
				Point p1 = new Point(1, 1);
				Point p2 = new Point(2, 2);
				Point o = new Point(0, 0);
				Circle c = new Circle(o, 2);
				System.out.println(c.contains(p1));
				System.out.println(c.contains(p2));
			}
		}

具体编译和运行操作如下:

		// 在JavaDemo文件夹下创建 bin 和 src 文件夹
		mkdir bin
		mkdir src
		// 将Point.java, Circle.java, CircleDemo.java放入 src 文件夹
		// 项目目录内容如下:
		.
		├── bin
		└── src
			├── CircleDemo.java
			├── Circle.java
			└── Point.java
		// 编译src下所有文件到指定路径, 生成可执行文件
		javac -d bin/ src/*
		// 运行JavaDemo2程序
		java -cp bin com.circle.CircleDemo
		//运行结果为
		true
		false
		// 现在项目目录内容如下:
		.
		├── bin
		│   └── com
		│       └── circle
		│           ├── Circle.class
		│           ├── CircleDemo.class
		│           └── Point.class
		└── src
			├── CircleDemo.java
			├── Circle.java
			└── Point.java

# Other
这里再提一下，如何使用jar打包和解压jar文件。
打包jar文件主要采用了标准的zip打包方式

		cd bin
		jar -cf ../circle.jar com

解压jar文件

		unzip circle.jar 或 jar -xf circle.jar

希望通过以上的总结能够让读者了解基本的Java编译和运行的命令。

