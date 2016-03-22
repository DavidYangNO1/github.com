---
layout: post
title: "roundCorners"
date: 2016-03-22 23:20
comments: true
categories: [ios]
keywords: CAShapeLayer UIBezierPath UIRectCorner
description: ios create round corner
sharing: true
published: true  
---

现实世界是没有边界的，地球是园的。 iOS 里面的UIViews 也是可以弄成圆的，对一个UIView 生成圆角第一反应是修改CALayer属性
<!--more-->
用过设置view.layer.cornerRadius = num;


{% img /images/round1.png %} 

但是如果产品有个需求，只需要设置某个角是圆角，而不是4个角都是圆角，这么解决？
好吧，求美术MM还不如自己搞定。ios 里面可以用过mask,结合UIBezierPath来达到目的
具体代码如下:
``` c++
let cornerRadius: CGFloat = 8
let maskLayer = CAShapeLayer()
maskLayer.path = UIBezierPath(roundedRect:view.bounds,
							  byRoundingCourners:[.BottomLeft,.BottomRight],
							  cornerRadii:CGSize(width:cornerRadius,height:cornerRadius)
							).CGPath
view.layer.mask = mskLayer
```
{% img /images/round2.png %} 

通过byRoundingCorners 属性数组，你可以按照4个角配置其中几个圆角