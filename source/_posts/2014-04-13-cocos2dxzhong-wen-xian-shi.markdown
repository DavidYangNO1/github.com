---
layout: post
title: "cocos2dx中文显示"
date: 2014-04-13 17:22:08 +0800
comments: true
categories: [cocos2dx]
sharing: true
published: true 
---

cocos2dx 里面的label 对中文显示的支持不是很友好，在windows 环境下，会出现乱码，这主要是编码的问题。
中文支持主要还是还是使用UTF8。 因此解决的方法就是转换成UTF8 . 
方法如下： <!--more-->


1）方法1：如果考虑做过多语言发布app的，就知道本地话问题。一般都是通过读取语言文件，因此可以通过读取UTF8格式的文件，把UI显示的内容保存到文件里面
这是一种解决方法

2）方法2：就是转换窄字符，宽字符

``` c++
class  WStrToUTF8
{
public:
     static std::string StrToUTF8(const string& src)
     {
          std::string curLocale=setlocale(LC_ALL,NULL);
          setlocale(LC_ALL, "");
          int len=src.length()+1;
          wchar_t *str =new wchar_t[len];
          int total=mbstowcs(str,src.c_str(),len);
          str[total]=0;
          std::wstring wstr=str;
          delete[] str;
          setlocale(LC_ALL, curLocale.c_str());
          return WStrToUTF8(wstr);
     }

     static std::string WStrToUTF8( const wstring& src)
     {

          std::string dest;
          for (size_t i = 0; i < src.size(); i++){
               wchar_t w = src[i];
               if (w <= 0x7f)
                    dest.push_back((char)w);
               else if (w <= 0x7ff){
                    dest.push_back(0xc0 | ((w >> 6)& 0x1f));
                    dest.push_back(0x80| (w & 0x3f));
               }
               else if (w <= 0xffff){
                    dest.push_back(0xe0 | ((w >> 12)& 0x0f));
                    dest.push_back(0x80| ((w >> 6) & 0x3f));
                    dest.push_back(0x80| (w & 0x3f));
               }
               else
                    dest.push_back('?');
          }
          return dest;
     }
};
```

Demo 如下：

``` c++
LabelTTF::create(WStrToUTF8::StrToUTF8("大闹天宫3！"), "Arial", 12);

LabelTTF::create(WStrToUTF8::WStrToUTF8("大闹天宫"), "Arial", 12);
```
