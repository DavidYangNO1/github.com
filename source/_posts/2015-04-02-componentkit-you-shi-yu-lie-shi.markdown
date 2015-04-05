---
layout: post
title: "ComponentKit 优势与劣势"
date: 2015-04-02 21:49:41 +0800
comments: true
categories: ComponentKit
sharing: true
published: true  
keywords: ComponentKit ,优势与劣势
description: ComponentKit ,优势与劣势
---


ComponentKit 突出的地方是在复杂的列表视图，在Facebook的News Feed IOS版本app得到了很好的体现
<!--more-->


优势：


  1：简单声明式：就像React 这个javascript库一样。

  2：滑动性能的提升：所有的布局操作都在后台线程操作，这样主线程不会太忙

  3；View回收重用，所有组件视图都是按照声明表达式方式来配置，使得view可以很好的自动回收重用

  4: 组件化：组件使得Compnentkit构建负责的UI，不会超过300行代码

注意点：

  1：UI接口不是list或者表格不适合ComponentKit


  2: ComponentKit 要求C++，Objective。 目前还没有Swift版本
