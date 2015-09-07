---
layout: post
title: "ios9 网络问题 schema白名单"
date: 2015-09-07 09:46
comments: true
categories: [ios]
keywords: http,https,schema,iOS 9, openUrl,canOpen
description: 
sharing: true
published: true 
---


ios 9 适配两个明显的问题
<!--more-->

1：网络问题
   iOS9 网络协议开始从http转换到https. 所以你的app 如果服务器不支持tls2估计很多网络模块打不开什么
   有两中解决方案：1) 服务器升级支持https
                 2) 或者在plist添加申明，不适用默认的https,转到http协议，具体做法如下
                 {% img /images/schema.png %} 

2：schema 白名单问题，白名单，IOS9 需要把你要打开的schema 添加到info.plist 白名单里面去
参考如下：
<key>LSApplicationQueriesSchemes</key>
<array>
    <string>twitter</string>
    <string>whatsapp</string>
</array>
