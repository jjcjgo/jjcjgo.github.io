---
layout: post
title: 文件上传
date:   2024-11-20
tags: web
comments: true
author: jjcjgo
---
 
文件上传文件上传是利用一些文件上传的漏洞来上传木马从而达到提权的目的     


文件上传安全指的是攻击者通过利用上传实现后门的写入连接后门进行权限控制的安全问题，对于如何确保这类安全问题，一般会从原生态功能中的文件内容，文件后缀，文件类型等方面判断，但是漏洞可能不仅在本身的代码验证逻辑中出现安全问题，也会在语言版本，语言函数，中间件，引用的第三方编辑器等存在缺陷地方配合利用。另外文件上传也有多个存储逻辑，不同的文件存储方案也会给攻击者带来不一样的挑战


而达成这个目的需要两个条件第一是需要能将木马传上去第二则是木马能被解析两个条件缺一不可     


现在我们来讲几个常见的上传绕过案例：        



**js前端绕过**      



如果开发只在前端做了过滤处理的话那是非常简单就能绕过的有两种方法一种是把浏览器的js代码给禁用另一种方法是讲数据包修改即可    

对于是不是前端该如何判断呢？上传文件如果还没有数据包就被过滤了则是前端过滤      

防范方式：把验证放在后端

**MIME类型**   


如果是通过识别数据包mime类型来过滤的话改包即可Content-Type：image/png

防范方式:使用多重验证并且开启后缀过滤


**文件头判断** 

如果是通过文件头来判断的话在文件的头部加上没被过滤类型的文件头即可

防范方式:使用多重验证并开启文件后缀过滤



**黑名单过滤**


如果是用了黑名单过滤的话有几种方法这要看实际情况     

1：如果是通过删除类似php这种敏感文件名的话使用双写pphphp有概率绕过,如果是递归查找则不行

2: 可以通过多种可以被解析的文件后缀进行爆破绕过例如php5之类的     

3：可以利用window对大小写不严格进行大小写后缀绕过liunx则不行



**白名单过滤**

如果是用了白名单的话也有几种     

1:低版本GET-%00截断     


自动解码一次     


/var/www/html/upload/x.php%00      


2.低版本POST-%00截断       

手工解码一次     

../upload/x.php%00 二次解码

3.空格绕过window文件名后缀加空格的时候会自动删除如果没过滤则可以利用   


4.点绕过和空格同理window服务器可用


**条件竞争**   


这是和代码逻辑有关的如果代码中是先把文件上传在检测删除而不是先检测在上传的话就可以利用服务器的延迟去访问上传的文件有概率执行     

方法：需要用木马在服务器中生成另一个木马文件即可上传不断发包，请求不断发包即可。

防范方式：将文件上传的逻辑变成先先检测在储存

  **解析漏洞**

  .htaccess

  
AddType application/x-httpd-php .png

  apache框架漏洞可以上传一个.htaccess的配置文件将文件后缀解析成你想要的(前提是有这个漏洞才行)

apache多后缀解析     


apache的解析是从后往前进行的  

使用多个陌生后缀可以让阿帕奇解析到你想要的文件后缀  


例子： shell.php.jpg(多后缀)


shell.php.aaa.ccc(陌生后缀)

防范方式:使用最新的补丁或者不适用apache框架

**图片马**

可以在图片中插入后门代码即可制作同时还要注意二次渲染，如果二次渲染的话还要确定二次渲染的范围不要让代码被渲染了     


**函数缺陷**


用了move_uploaded_file 函数的话则可以用1.php/.文件名绕过    


getimagesize突破    


这个函数会检测图片的大小这时候就要用cmd把代码插入到图片中    

但是图片马可能不会被解析这时候需要用文件包含包含你上传上去的文件来让后门代码解析


文件上传后端检测exif_imagetype()     

（遇到了再补充)    


**waf绕过**
有时候文件上传有waf需要绕过     

**编码绕过**   

使用不同的编码方式（如URL编码、Unicode编码）来混淆恶意负载，使WAF无法识别。例如，通过二次URL编码，可以使得WAF在解码时未能正确处理数据，从而允许恶意请求通过14.

**请求方式改变**   

有些WAF只对GET请求进行严格检测，而对POST请求的过滤较为宽松。通过将攻击负载放在POST请求中，可以绕过WAF的检测13.   

**参数复写**    


在URL中使用相同参数多次（例如?id=1&id=2），某些WAF可能只处理第一个参数，而后端程序可能取最后一个参数值，从而实现绕过45.   


**异常HTTP方法**    


使用非标准的HTTP方法（如PUT、DELETE等）发送请求，某些WAF可能未对这些方法进行检测，允许恶意请求通过12.   

**脏数据绕过**

发送大量无用数据（如填充字符）以使WAF失效。某些WAF在处理POST数据时只检查前8K的数据，后续的数据可能被忽略，从而允许攻击负载通过45.


**空格和特殊字符替换**


将空格替换为特殊字符（如%20或/**/），或者使用其他变体来规避关键字检测。例如，将SELECT替换为SELEC/**/T36.


**Cookie和HTTP头注入**


一些WAF只对GET和POST参数进行检测，而未对Cookie或HTTP头进行严格过滤。可以通过这些字段注入恶意负载来绕过WAF14.


**利用WAF的自身特性**


某些WAF可能会错误地处理特定类型的请求或负载，通过精心构造的请求可以利用这些漏洞实现绕过。例如，在SQL查询中使用特殊字符连接关键字，以避免被识别






