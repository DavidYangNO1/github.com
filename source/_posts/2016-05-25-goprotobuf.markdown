---
layout: post
title: "golang + Protobuffs"
date: 2016-05-25 00:28
comments: true
categories: [golang]
keywords: binary, client, go, golang, google, JSON, protobuf, protobuffers, protobuffs, protocol buffer, server, transport
description: 使用Protobuf 做通讯协议，在golang 环境下
sharing: true
published: true  
---

许多APP在通讯交互格式这块，通常会选择JSON,或者 plain text, JSON应为Node.js 开发的上升尤其受欢迎。真实情况，JSON并不是很快的一种格式，所以protobuf 做为一种可选项，具体更快，更容易使用，速度比起JSON快多了。
<!--more-->

基于JSON与Protobu性能比较如下

{% img /images/benchmark.png %} 

下面就一步步讲解下golang 环境下这么使用protobuf

1)环境搭建
   1.1:mac 环境安装 protoc
``` c++
   brew install protoc  (brew mac 神器，你懂得) 
```
   1.2:安装protobuf 插件
``` c++
   go get -u github.com/golang/protobuf/protoc-gen-go
```


2）通讯协议文件 .proto -> NetMessage.proto
``` c++
// version
syntax = "proto3";
// package of the file
package main;

message Client{
	required int32 id = 1;
	optional string name = 2;
	optional string email = 3;
	optional string country = 4;

	message Mail {
		optional string remoteEmail  =1;
		optional string body = 2; 
	}

	repeated Mail inbox = 5;
}
```
   以上定义了客户端与服务端通讯的消息格式

3）导出协议对应的go文件
``` c++
	protoc --go_out=. NetMessage.proto
```


4) 创建服务端

   服务端主要是在POST消息体里面接收一个protobu。然后打印出打的内容，具体代码如下
``` c++
package main

import (
	"fmt"
	"github.com/golang/protobuf/proto"
	"io/ioutil"
	"net/http"
)

func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		myClient := Client{}
		data, err := ioutil.ReadAll(r.Body)
		if err != nil {
			fmt.Println(err)
		}

		if err := proto.Unmarshal(data, &myClient); err != nil {
			fmt.Println(err)
		}

		println(myClient.Id, ":", myClient.Name, ":", myClient.Email, ":", myClient.Country)

		for _, mail := range myClient.Inbox {
			fmt.Println(mail.RemoteEmail, ":", mail.Body)
		}
	})
	http.ListenAndServe(":3000", nil)
}

```

5) 创建客户端
   客户端主要创建代码生成的client 结构体，把它的内容发送到服务器，代码如下：
``` c++
package main

import (
	"bytes"
	"fmt"
	"github.com/golang/protobuf/proto"
	"net/http"
)

func main() {

	myClient := Client{Id: 1, Name: "David Yang", Email: "davidyanggo@gmail.com", Country: "CH"}
	clientInbox := make([]*Client_Mail, 0, 20)
	clientInbox = append(clientInbox,
		&Client_Mail{RemoteEmail: "davidyanggo1@gmail.com", Body: "Hello. davidyanggo1."},
		&Client_Mail{RemoteEmail: "davidyanggo2@gmail.com", Body: "Bye, davidyanggo2."}
	)
	myClient.Inbox = clientInbox

	data, err := proto.Marshal(&myClient)
    if err != nil {
        fmt.Println(err)
        return
    }

    _, err = http.Post("http://localhost:3000", "", bytes.NewBuffer(data))

    if err != nil {
        fmt.Println(err)
        return
    }
}

```


总结：
protobuf 很容易使用，提高应用程序的交互速度，推荐在项目里面使用。
想要更深入了解参考一下文档：https://developers.google.com/protocol-buffers/docs/gotutorial

快乐编码，活着玩命，不是玩命活着
