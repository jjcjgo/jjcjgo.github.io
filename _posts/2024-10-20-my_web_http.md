---
layout: post
title: "http请求头"
date:   2024-10-20
tags: web
comments: true
author: jjcjgo
---
请求头（Request Headers）是在HTTP协议中用于传递关于请求的额外信息的部分。它包含了客户端（通常是浏览器或应用程序）
通常在发送给服务器的数据包中
请求头的作用有几个方面：
可以储存客户端信息例如ua头和储存用户的登录信息cookie之类的
下面列举出几个常用的请求头
 User-Agent：标识客户端使用的浏览器和操作系统信息。可以通过$_SERVER['HTTP_USER_AGENT']获取。

 Accept：指定客户端能够处理的内容类型，即可接受的媒体类型。可以通过$_SERVER['HTTP_ACCEPT']获取。
 
 Content-Type：指定请求体中的数据格式类型。常见的取值有application/json、application/x-www-form-urlencoded等。可以通过$_SERVER['CONTENT_TYPE']获取。

 Authorization：用于进行身份验证的凭证信息。常见的取值有Bearer Token、Basic Authentication等。可以通过$_SERVER['HTTP_AUTHORIZATION']获取。

Cookie：包含来自客户端的Cookie信息。可以通过$_SERVER['HTTP_COOKIE']获取。

 Referer：指示当前请求是从哪个URL页面发起的。可以通过$_SERVER['HTTP_REFERER']获取。

 Host：指定服务器的域名或IP地址。可以通过$_SERVER['HTTP_HOST']获取。

X-Requested-With：指示请求是否由Ajax发起的。通常在Ajax请求中会设置该头部字段，取值为"XMLHttpRequest"。可以通过$_SERVER['HTTP_X_REQUESTED_WITH']获取。

Content-Length：指定请求体的长度。可以通过$_SERVER['CONTENT_LENGTH']获取。

Cache-Control：控制缓存行为的指令。用于指定客户端和代理服务器如何缓存响应。可以通过$_SERVER['HTTP_CACHE_CONTROL']获取。
这些是PHP中常见的请求头，可以使用$_SERVER超全局变量来获取它们的值。根据具体的需求，你可以在PHP中通过相应的方法或变量来处理这些请求头信息


从此可以得出服务器端通过请求头去识别用户，因此就有了通过伪造请求头来达到目的的方法

下面说几个ctf中常用的请求头
Content-Type 文件类型通过修改可以使服务器错误的识别文件类型

Location：用于重定向到其他页面。当服务器需要将客户端重定向到另一个页面时使用

Set-Cookie：设置要在客户端存储的 Cookie 值可以利用cookie值来形成一些payload

User-Agent ua头可以修改浏览器的名字和客户端的信息

Referer：存储的页面跳转的信息

X-Forwarded-For :记录着用户的ip可以修改同时还有client ip

vim 记录客户端的代理


灵活修改运用这些请求头可以绕过一些检测机制从而达到目的


