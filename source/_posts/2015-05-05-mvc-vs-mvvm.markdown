---
layout: post
title: "mvc vs mvvm"
date: 2015-05-05 00:39
comments: true
categories: [框架]
sharing: true
published: true  
keywords: mvc ,mvvm, ios, 框架
description: mvc ,mvvm, ios, 框架
---

前段时间，结合公司的项目实际情况，做了MVC vs MVVM的框架比较，技术分享
<!--more-->

1）MVC FLow
	1:MVC是最初的UI设计模式，1970年的时候就出现了，最早出现在Smalltalk语言中，
	2:介绍MVC 这种模式
	3:上图这种Model-View-Controller之间的环状关系


{% img /images/mvc1.png %} 

2） MVC Case
	1：流程图介绍
	2：Massive View Controller
	3：目前的项目

{% img /images/mvc2.png %} 

3）MVC劣势

	1:Massive Controller，关联了View,Model,双向环状关系，
	  不利于后续维护，特别是多人协作时候，一个Controller文件动辄就几千行代码

	2:关系复杂，后续UI风格更新，在几千行代码修改什么感觉？一不留神改了model，或者controller的东西，容易产生bug

	3:单元测试不方便， 改动的范围不能够控制在一个较小的范围。

	4:不能够复用。
	  比如iphone版本，ipad版本，不管UI这么变，业务逻辑是基本相同的，Controller与model，view这种强耦合关系不方便复用


{% img /images/mvc3.png %} 


4）mvvm 本质
    1:view 引用了View Model,单向的
    2:ViewModel 引用了Model,单向的

{% img /images/mvvm1.png %} 

5) mvvm 流程图讲解
    1：MVVM模式依赖于数据绑定，它是一个框架级别的特性，用于自动连接对象属性和UI控件
    2：IOS没有数据绑定框架，可以通过ReactiveCocoa来实现数据绑定功能
    3：ReactiveCocoa开源的项目，目的就是定义一个统一的事件处理接口，这样它们可以非常简单地进行链接、过滤和组合。
       ReactiveCocoa结合了一些编程模式：
	   函数式编程：利用高阶函数，即将函数作为其它函数的参数。
       响应式编程：关注于数据流及变化的传播

{% img /images/mvvm2.png %} 

6)mvvm 优势
    1：低耦合
       视图（View）可以独立于Model变化和修改，一个ViewModel可以绑定到不同的"View"上，当View变化的时候Model可以不变，当Model变化的时候View也可以不变。
    2：可重用性
       对于iphoen,ipad不同版本，不同的只是UI, 视图逻辑放到ViewModel,可以达到视图逻辑重用
    3：独立开发
       开发人员可以专注于ViewModel，model的开发，UI开发人员通过Controller,View,xib,storyboad专注页面，模块业务清晰
    4：单元测试
       ViewModel 独立于UI很方便做单元测试，不会像MVC里面的Controle一样做测试那么别扭
       
{% img /images/mvvm3.png %} 

7）总结：从产品项目开发的经验来看，显然mvvm比起mvc这种模式优势很明显，推荐大家引入到实际项目中去，主要是思想上