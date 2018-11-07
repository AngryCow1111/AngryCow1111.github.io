---
layout: post
title:  阿里云服务器 mysql连接错误
no-post-nav: true
category: others
<<<<<<< HEAD
tags: [linux]
excerpt: 学习远程环境配置！GO!GO!GO!
=======
tags: [mysql]
excerpt: mysql远程连接配置
>>>>>>> 7d1a34a063de351afa70d04d6d00afb8bf2a3092
---

# navicat连接阿里云mysql错误

## 1. 添加阿里云安全规则

- 打开安全组
  首先创建安全组，然后再安全组中添加规则，如下所示，详情可以参考官方文档 
  https://helpcdn.aliyun.com/document_detail/25471.html?spm=5176.doc25468.2.4.RfJyPU  

  ![mysql-aliyun](https://angrycow1111.github.io/assets/images/2018/it/mysql-aliyun.png)

- 打开配置规则

  ![mysql-aliyun](https://angrycow1111.github.io/assets/images/2018/it/mysql-aliyun-002.png)

- 配置规则

  ![mysql-aliyun](https://angrycow1111.github.io/assets/images/2018/it/mysql-aliyun-003.png)

## 2. 修改mysql配置

- 修改mysql配置，允许远程访问

1. 修改表

   可能是你的帐号不允许从远程登陆,只能在localhost。这个时候只要在localhost的那台电脑,登入mysql后,更改 "mysql" 数据库里的 "user" 表里的 "host" 项,从"localhost"改称"%" 

   mysql -u root -pvmwaremysql>use mysql;

   mysql>update user set host = '%' where user = 'root';

   ![mysql-aliyun-005](https://angrycow1111.github.io/assets/images/2018/it/mysql-aliyun-005.png)

   mysql>select host, user from user; 

   ![mysql-aliyun-006](https://angrycow1111.github.io/assets/images/2018/it/mysql-aliyun-006.png)

2. 授权

   例如,你想myuser使用mypassword从任何主机连接到mysql服务器的话。 

   GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'%' IDENTIFIED BY 'mypassword' WITH GRANT OPTION; 

   如果你想允许用户myuser从ip为192.168.1.3的主机连接到mysql服务器,并使用mypassword作为密码 

   GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'192.168.1.3' IDENTIFIED BY 'mypassword' WITH GRANT OPTION; 

- 重启mysql服务（记住，否则可能无法生效）

  systemctl restart mysqld.service;

  ![mysql-aliyun-007](https://angrycow1111.github.io/assets/images/2018/it/mysql-aliyun-007.png)

测试成功！

![mysql-aliyun-008](https://angrycow1111.github.io/assets/images/2018/it/mysql-aliyun-008.png)
