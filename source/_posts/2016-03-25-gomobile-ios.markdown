---
layout: post
title: "ios 结合 golang gomobile 产生这样的火花"
date: 2016-03-25 02:55
comments: true
categories: [golang]
keywords: gomobile ios
description: 使用gomobile 夸平台，写移动端 
sharing: true
published: true  
---


golang 开发基础服务组件，write once, run everwhere(主要是iOS，Android)
<!--more-->

gomobile 工具让你编译go 代码，在iOS 里面调用，使用gomobile,可以把golang 写的基础组件，在Android,iOS,服务端直接共享，通讯。
下面的教程主要讲下从配置环境的iOS里面调用go package代码.

<h1>安装gomobile</h1>
首先：下载安装gomobile
``` c++
1:go get golang.org/x/mobile/cmd/gomobile
2:gomobile init
```
第1步主要是下载安装，第二步会配置安装编译器，主要是用来打包成ios，Android,底层是C++编译器，生成打包的。主要是下载下面两个包：gomobile-ndk-r10e-linux-x86_64，gomobile-ndk-r10e-darwin-x86_64。需要翻墙。

<h1>用go写一个基础组件，golang里面叫做Package</h1>
``` c++
package example

func Add(x int, y int) int {
	return x + y
}
```
使用下面命令编译上面的go代码，生成Framework.
gomobile bind -target ios package文件夹。

上面会生成一个以go package命名的iOS framework

<h1>iOS 调用 framework</h1>
把上面生成的framework 拖到xcode 工程里面去,
打包生成的go代码到OC这边都是C函数，以go+package+go函数名称 命名的，
OC就可以直接调用了。


<h1>gomobile的遐想</h1>
gomobile 解决了前后端write run everwhere.简单说移动端的基础组件，服务，可以用golang 来写，android,iOS都可以调用。 做基础组件开发不错选择。至于是否可以用golang 来开个进程，给iOS弄个后天服务，不知道可行么，后续可以尝试下。拥抱未来吧