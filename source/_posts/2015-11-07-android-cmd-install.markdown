---
layout: post
title: "android cmd install"
date: 2015-11-07 00:31
comments: true
categories: [Android]
keywords: Android cmd
sharing: true
published: true  
description: android 命令行安装打包
---

android 命令行安装打包
<!--more-->

1: CMD  切换到Android 项目root 目录


2：使用Gradle 编译项目，使用gradle 脚本执行assembleDebug 编译项目

``` c++

chmod +x gradlew
./gradlew assembleDebug

``` 
3：项目root 目录下的build 生成 {app name}.apk

4: 安装apk 到设备

adb install bin/{app name}.apk