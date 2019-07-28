---
layout: post
title:  spring boot 设置tomcat post参数限制
no-post-nav: true
category: springboot
tags: [springboot]
excerpt: tomcat springboot post参数限制
---

# Eureka快速创建注册中心

今天传图片，用的base64字符串，POST方法，前端传送的时候总是莫名其妙的崩溃，先以为是文件大小被限制了，但是我这个是字符串接收，不是文件接收，于是又继续查询，原来post本身没有参数大小限制，但是tomcat给限制了

解决方法：
1. 外置的tomcat

在server.xml里面添加或者修改
```
<Connector port="8080" protocol="HTTP/1.1" 
  connectionTimeout="2000" 
  redirectPort="8443" 
  URIEncoding="UTF-8"
  maxThreads="3000"
  compression="on" compressableMimeType="text/html,text/xml" 
  maxPostSize="0" 
/>
```
没错就是修改这里的maxPostSize的值，默认是1024，改成0，就可以不限制了大小了。
2. 在server.xml里面添加或者修改
```
server.tomcat.max-http-post-size=0
```
==修改了配置之后没有做做热部署的伙伴记得重启服务器==

在一段公司工作经历结束后，继续寻找另一段...... 默认孤单的岁月，削减了自己锐气，最好办法就是坚持！！！阳光、午后、代码与我！但也不能缺了，朋友与午餐，以及餐后的闲暇，hahaha