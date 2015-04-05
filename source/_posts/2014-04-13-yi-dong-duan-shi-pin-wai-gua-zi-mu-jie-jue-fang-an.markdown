---
layout: post
title: "移动端视频外挂字幕解决方案"
date: 2014-04-13 23:21:52 +0800
comments: true
categories: [ios]
sharing: true
published: true  
keywords: webvtt字幕文件，IOS，Android
description: webvtt字幕文件，IOS，Android
---

webvtt字幕文件已经很好的支持跨平台，特别是 html5的支持。 ios 6以后在这方面也已经无缝集成，底层做字幕渲染支持。
<!--more-->

首先说下外挂字幕的场景吧，有需求的地方才会有技术的跟进。 想必大家都看过美剧吧，视频运营商拿过来的很多已经内部集成了e文，那要翻译显示中文就是外挂字幕上场了，webvtt 技术就很好解决了这个问题。

有需求才有市场，下面就谈谈外挂字幕webvtt文件这么在视频里面集成。


1） 首先是说下在ios系统下字幕文件与视频播放文件的流程。 直接看流程图

{% img /images/vtt_1.png %} 

m3u8文件是为了多码率的适配而提出的，为了支持外挂字体，需要整合vtt与m3u8文件。整个合并步骤流程图如上，下面说下具体的步骤
2） 创建webvtt 外挂字幕（字幕拓展名为.vtt）

.vtt 文件可以像.ts文件一样，利用mediasubtitlesegmenter工具
拆成片段，每一个片段都是一个小的.vtt字幕文件，.vtt文件内容以及拆分的片段文件列表如下截图：

{% img /images/vtt_2.png %} 


上面是具体的.vtt 文件内容， 有起始时间，结束时间，显示相对位置


{% img /images/vtt_3.png %} 
    
3)建立一个.m3u8 字幕播放列表文件.
	
把上面拆分的字幕文件.vtt 的链接地址url放到.m3u8字幕播放列表文件里面，字幕url地址可以是绝对路径或者相对路径, 具体的内容如下截图：
     
{% img /images/vtt_4.png %}

上面是字幕文件集合，也就是subtitle.m3u8 的文件内容

4)添加.m3u8 字幕文件播放列表地址到主.m3u8播放文件里面
	
主.m3u8文件内容主要包含字幕文件地址与ts文件地址，ts文件与字幕文件主要通过Group-ID,SUBTITLES 属性建立视频内容与文字对应关系，主.m3u8文件具体内容如下截图

{% img /images/vtt_5.png %}

5)主.m3u8文件解释，主要介绍几个关键的属性

``` c++

#EXT-X-MEDIA:TYPE=SUBTITLES,GROUP-ID="subs",NAME="English",DEFAULT=YES,AUTOSELECT=YES,FORCED=NO,LANGUAGE="eng",URI="Subtitle.m3u8" 

```

表示链接到字幕文件的相关属性设置

``` c++

#EXT-X-MEDIA:TYPE=SUBTITLES 表示字幕文件标识 
GROUP-ID="subs" 表示字幕文件与ts文件对应的标识，
DEFAULT= YES  表示视频播放默认的字幕文件 
LANGUAGE="eng" 表示字幕语言,支持多语言字幕
URI="Subtitle.m3u8  字幕的路径文件名


#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=263851,CODECS="mp4a.40.2, avc1.4d400d",RESOLUTION=416x234,AUDIO="bipbop_audio",SUBTITLES="subs" 
aaa.m3u8  
表示链接的ts文件相关属性设置
SUBTITLES="subs"  链接到字幕文件，建立ts文件与字幕文件标识
aaa.m3u8 里面都是对应的ts文件

```

按照上面的流程建立好webvtt文件。之后就是视频客户端解析。


ios 端 vtt接口VTTSettingManager.h 的实现

``` c++

@interface VTTSettingManager : NSObject
{
}

@property(nonatomic,retain) AVPlayer * player;

-(id)initWithPlayer:(AVPlayer*)newPlayer;

/* ! vtt 字幕设置
 * @pram: entity 字幕实体类
 *
 */
-(void)setVttFontStyle:(VTTSettingEntity*)entity;


/* ! 开启字幕
 *   如果影片有webvtt 设置才起作用
 *   返回true 有字幕,操作成功，false 影片没有字幕，操作失败
 */
-(BOOL)enableVtt;


/* ! 关闭字幕
 *   如果影片有webvtt 设置才起作用
 *   返回true 操作成功，false 操作失败
 */
-(BOOL)disableVtt;

/* ! 设置字幕语言(要设置的区域， vtt文件需要存在对于的区域语言 )
 * @pram: localeIdentifier 区域 如en_US
 */
-(BOOL)setVTTLanguage:(NSString*)localeIdentifier;

@end

```


``` c++

/*~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
 *
 * webvtt 字幕设置实体类
 *
 *~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~*/
@interface VTTSettingEntity : NSObject

@property(nonatomic,retain) NSString * fontFamilyName;   //(不传值，默认为空) 字体家族

@property(nonatomic,assign) float relativeSize;          //(不传值，默认是100) 字体相对大小 如：relativeSize ==120 表示字体大小为原来的120%

@property(nonatomic,assign) float  fr;  //The foreground color for text,representing r  0.0-1.0
@property(nonatomic,assign) float  fg;  //The foreground color for text,representing g  0.0-1.0
@property(nonatomic,assign) float  fb;  //The foreground color for text,representing b  0.0-1.0
@property(nonatomic,assign) float  fa;  //The foreground color for text,representing a  0.0-1.0
@property(nonatomic,assign) BOOL  foregroundColorEnable;    // (fr,fg,fb,fa传了值，foregroundColorEnable设置为true，默认是false)foreground color enable;

@property(nonatomic,assign) float  br;  //The background color for text,representing r  0.0-1.0
@property(nonatomic,assign) float  bg;  //The background color for text,representing g  0.0-1.0
@property(nonatomic,assign) float  bb;  //The background color for text,representing b  0.0-1.0
@property(nonatomic,assign) float  ba;  //The background color for text,representing a  0.0-1.0
@property(nonatomic,assign) BOOL  backgroundColorEnable;    //(br,bg,bb,ba传了值，backgroundColorEnable设置为true，默认是false) background color enable;

@property(nonatomic,assign) BOOL boldStyle;    // (不传值，默认是false)设置粗体

@property(nonatomic,assign) BOOL italicStyle;  // (不传值，默认是false)设置斜体

@property(nonatomic,assign) BOOL underlineStyle;  //(不传值，默认是false)设置下划线

@property(nonatomic,retain) NSString * cssName;   //(不传值，默认是空)css style name;

@end

```


参考文档

http://en.wikipedia.org/wiki/HTTP_Live_Streaming

https://developer.apple.com/streaming



