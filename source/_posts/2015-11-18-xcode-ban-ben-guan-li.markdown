---
layout: post
title: "xcode 版本管理"
date: 2015-11-18 00:35
comments: true
categories: [ios]
keywords: xocde ios 版本管理
description: xocde ios 版本管理
sharing: true
published: true  
---

xcode agvtool 版本管理命令
<!--more-->

xcode agvtool 版本管理命令
agvtool 主要是管理项目的版本工具
xcode 项目工程文件project.pbxproj包含项目版本CURRENT_PROJECT_VERSION，agvtool工具运行的时候会查看 project.pbxproj里面的CURRENT_PROJECT_VERSION是否存在，如果存在然后更新build版本号，不存在在Xcode建立CURRENT_PROJECT_VERSION再去使用agvtool

主要是通过命令行来修改版本号，项目版本号

1：修改版本号
agvtool new-marketing-version <your_specific_version>
etc. agvtool new-marketing-version 2.0
上面命令主要会更新info.plist 里面的CFBundleShortVersionString 版本号位2.0

2：修改build 版本号
   
  2.1 自动增加build 版本号
  agvtool next-version -all
  上面命令会自动把当前版本增加到大于当前的整数版本号
  2.2 制定特定的版本号
  agvtool new-version -all <your_specific_version>
  etc. agvtool new-version -all 2.6.9

3：版本号查看
  3.1 查看当前版本
  agvtool what-marketing-version

  3.2 查看当前构建版本
  agvtool what-version