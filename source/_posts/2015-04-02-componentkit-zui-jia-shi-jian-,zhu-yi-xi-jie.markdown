---
layout: post
title: "ComponentKit 最佳实践，注意细节"
date: 2015-04-02 21:54:23 +0800
comments: true
categories: ComponentKit
sharing: true
published: true 
keywords: ComponentKit ,最佳实践,优化
description: ComponentKit ,最佳实践,优化
---

ComponentKit  最佳实践,开发中需要注意的地方
<!--more-->

1:代码行不超过300行：

  代码行多维护起来就不方便，组件化的代码文件快总共最好不要超过300行，如果超过，最佳的处理方式是拆分单独的CKCompositeComponent


2：避免直接设置组件宽度100%，不利于组件重用。好的处理方式是通过父组件来制定宽度
   比如如果父组件是CKStackLayoutComponent,使用CKStackLayoutAlignItemsStretch 拉伸填充

3: 通过+new创建一个组件，nil检查
   +new 创建的对象，又可以为空，比如：

``` c++

   @implementation MyComponent
     {
            NSString *_name;
     }

     + (instancetype)newWithName:(NSString *)name
     {
            MyComponent *c = [super newWithComponent:...];
            c->_name = [name copy]; // Crashes if c is nil
            return c;
     }

``` 
     正确的做法是检查对象c是否为空

4：代码块避免过多的分支，分支过多，可读性不好
   比如

``` c++

   + (instancetype)newWithArticle:(Article *)article
     {
		  CKComponent *headerComponent;
		  if (article.featured) {
		    headerComponent = [FeaturedArticleHeaderComponent newWithArticle:article];
		  } else {
		    headerComponent = [RegularArticleHeaderComponent newWithArticle:article];
		  }

		  UIEdgeInsets insets = {10, 10, 10, 10};
		  CGFloat imageSize = 20;
		  if (iPad) {
		    insets = {20, 20, 20, 20};
		    imageSize = 40;
		  }

		  return [super newWithComponent:
		          [CKStackLayoutComponent
		           newWithView:{}
		           size:{}
		           style:{}
		           children:{
		             {headerComponent},
		             {[ArticleTextComponent
		               newWithArticle:article
		               insets:insets
		               imageSize:imageSize]},
		           }]]
	}

``` 
    如果分支过多，考虑把组件拆分到单独的小组件，再利用复合模式组装起立

5： Components 不能够被delegate引用

    Components 的生命周期都是短暂的，不是手动控制。 可选的是采用CKComponentController，CKComponentController生命持久的，他们持久化跟踪每个版本comonent 的更新。
   
    比如下面的方式是不推荐的

``` c++

    @implementation AlertDisplayComponent <UIAlertViewDelegate>
	{
	  UIAlertView *_alertView;
	}

	- (void)didTapDisplayAlert
	{
	  _alertView = [[UIAlertView alloc] initWithTitle:@"Are you sure?"
	                                          message:nil
	                                         delegate:self ...];
	  [_alertView show];
	}

	- (void)alertView:(UIAlertView *)alert didDismissWithButtonIndex:(NSInteger)buttonIndex
	{
	  [self updateState:...];
	}
	@end

``` 
  
  因为上面的组件self 可能deallocate，所以alert的delegate为空，不是你想要的结果
   最佳方式

``` c++

   @interface AlertDisplayComponentController : CKComponentController <UIAlertViewDelegate>
	
   @end

	@implementation AlertDisplayComponentController
	{
	  UIAlertView *_alertView;
	}

	- (void)didTapDisplayAlert

	  _alertView = [[UIAlertView alloc] initWithTitle:@"Are you sure?"
	                                          message:nil
	                                         delegate:self ...];
	  [_alertView show];
	}

	- (void)alertView:(UIAlertView *)alert didDismissWithButtonIndex:(NSInteger)buttonIndex
	{
	  [self.component updateState:...];
	}
	@end

``` 

6： 对象传递到组件应该不可变的

    因为+new 在后台线程创建，创建的组件在多线程操作，可变的对象mutable 是nonatomic,即便是atomic，还是存在race condition,会影响代码的逻辑
