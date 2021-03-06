---
title: 同源策略和跨域解决方案
date: 2019-03-29 02:00:33
tags: web
---

### 为什么要跨域

跨域是web开发中常见的场景之一，要想了解跨域，首先得先搞清楚，为什么要跨域而不能直接连接，这是谁受到了什么限制。

还有HTTP请求遇到跨域问题，这个请求是发送的时候被拦截了还是响应数据的接受被拦截了。


<!-- more -->

## 同源策略

web浏览器有一个安全策略，这个策略是用来限制网页脚本（js）访问数据必须和当前网页地址是**相同的来源**，这个安全策略就是同源策略。

源是http资源的地址——url，url[协议类型]://[服务器地址]:[端口号]/[资源层级UNIX文件路径][文件名]?[查询]#[片段ID] 组成，浏览器只判断url的地址的几个部分相同即认为是同源。

* 协议
* 域名
* 端口


> 同源策略：浏览器的一个安全功能，不同源的客户端脚本在没有明确授权的情况下，不能读写对象自资源。**其阻止的是数据的接受而不是请求的发送**

当然也有些web技术不受同源策略限制：

* 超链接：`<a href=".."></a>`
* 重定向：`window.location=.`
* 表单提交:  form的onSubmit事件

部分html的标签可以引入跨域资源，但js不能读写加载内容。

* `<script src="..."></script>`
* `<img src="..">`
* `<link href="..">`
* `<iframe src=""..>`

## 跨域

正是因为受到**浏览器同源策略**的约束，有时候我们不得不使用其他域名的数据，我们就需要使用跨域这种方式解决，引用一段维基百科的介绍。

> 跨域资源共享（英语：Cross-origin resource sharing，缩写：CORS），用于让网页的受限资源能够被其他域名的页面访问的一种机制。

我们在说实现方式前，有一点要注意：**如果是协议和端口不同，“前台”是无能为力的**

## 实现方式

实现方式：

- JSONP
- CORS
- document.domain
- window.name
- postMessage H5

需要跨域的场景：

1. 上传图片、文件
2. 富文本编辑器
3. 页面请求第三方接口

### JSONP

一般我们使用js发送请求是使用AJAX的方式，AJAX会受到同源策略的约束，而我们使用`script`标签也能发送请求，而且，上文也提交`script`标签不受同源策略的约束。那么我们可以动态创建一个`script`标签发送请求，这样响应的数据就不会浏览器阻止。服务端响应回前台一个可执行的回调函数即可获取数据。

由于服务端响应的是一个回调函数，需要前台调用才能获取真正响应的数据，相当于把JSON（前后端传输数据的一种格式）放置在一个盒子里，所以叫**JSON with Padding —— JSONP**。

JSONP已经是一种很成熟的跨域方式了，很多http库都会有支持，比如jQuery、axios.. 当然你也可以自己实现封装。

**JSONP仅支持GET请求，且需要后端支持，返回一个能被js执行的回调函数。**

————

**降域 document.domain**

将两个不同源的域名document.domain设置为同一个即可；存在安全性问题，一个网站被攻击，另一个也有安全漏洞，只适用于cookie和iframe窗口。

**跨域资源共享 CORS**

设置服务器响应头 Access-Control-Allow-Origin 指定允许跨域的源，实现浏览器向跨源服务器，发出XMLHttpRequest请求，从而克服了AJAX只能同源使用的限制。

**JSON with Padding JSONP**

动态插入script标签，通过script标签引入一个js文件， 在服务端输出JSON数据，客户端执行回调函数，从而解决了跨域的数据请求。 jsonp+padding--将json填充到一个盒子里，（使用回调函数获取json数据）；兼容性好，简单易用，支持浏览器与服务器双向通信。当然也有一些缺点：权限漏洞，只能发送GET请求，需要防止XSS。

**window.name**

一个窗口的声明周期内，窗口载入的所有页面都是共享一个name属性的，每个页面都对window.name有读写权限，其属性持久存在，不因新页面载入而进行充值。

在原页面中使用一个隐藏的iframe充当中间人角色，由iframe去获取数据，src设为目标页面，再把src设置跟原页面同一个域，否则受到同源策略的限制。原页面再去得到iframe获取到的数据，iframe的window.name。

**postMessage**

H5提供的一个API