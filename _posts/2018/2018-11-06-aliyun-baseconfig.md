---
layout: post
title:  阿里云服务器，基础环境配置
no-post-nav: true
category: othes
tags: [linux]
excerpt: 学习远程环境配置！GO!GO!GO!
---

# 1. JDK安装

## 	1.1 查询已安装JDK

​		rpm -qa|grep java          

## 	1.2 卸载JDK

​		yum -y remove 路径

## 	1.3 解压上传的JDK

​		tar -xvf 压缩文件

## 	1.4 配置环境变量

​		JAVA_HOME JRE:JAVA_HOME:lib/JRE       
​		CLASS_PATH PATH
​		export JAVA_HOME=/home/soft/jdk1.8.0_111 
​		export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/libexport     PATH= ${JAV A_ HOME}/bin:$PATH

## 	1.5 保存配置文件

​      		source profile文件

# 2. 安装mysql

## 	2.1 查询并删除默认安装的mariadb（安装mysql后会自动覆盖原来的mariadb）

​		rpm -qa|grep mariadb      

​		yum -y remove mariadb具体版本

## 	2.2 安装mysql

​		（由于yum源上没有mysql-server。所以必须去官网下载，这里 我们用wget命令，直接获取）           

​	 	 wget -i -c http://dev.mysql.com/get/mysql57-community-release-el7-10.noarch.rpm

## 	2.3 安装mysql数据库

​		yum -y install mysql-community-server

## 	2.4 完成安装，查看、重启mysql

​		systemctl status mysqld; // 查询mysql的运行转态  

​		![](https://github.com/AngryCow1111/AngryCow1111.github.io/assets/images/2018/it/mysql_status.jpg)

​		systemctl restart mysqld;// 重启mysql
​		systemctl stop mysqld;// 停止mysqld

​		![](https://github.com/AngryCow1111/AngryCow1111.github.io/assets/images/2018/it/mysql_stop.jpg])

​		此时MySQL已经开始正常运行，不过要想进入MySQL还得先找出此时root用户的密码

​		通过如下命令可以在日志文件中找出密码：grep "password" /var/log/mysqld.log

## 	2.5 进入mysql数据库

​		mysql -uroot -p 输入密码

​		![](https://github.com/AngryCow1111/AngryCow1111.github.io/assets/images/2018/it/mysql_enter.jpg)
​		输入初始密码，此时不能做任何事情，因为MySQL默认必须修改密码之后才能操作数据库修改密码命令：
​		ALTER USER 'root'@'localhost' IDENTIFIED BY '123';

## 	2.6 解决报错!

​		![](https://github.com/AngryCow1111/AngryCow1111.github.io/assets/imag			es/2018/it/mysql_password_error.png)

​		以上报错是说新设置的密码过于简单，解决方式：
​		//首先按照默认密码格式复杂度更改 

## 	2.7 查看MySQL完整的初始密码规则

​		查看MySQL完整的初始密码规则，查看的前提是必须先用ALTER USER命令更改过密码（SHOW VARIABLES LIKE 'validate_password%';）

## 	2.8 修改验证密码策略     

​		因为当前的密码太复杂不方便后期做实验，所以使用命令修改密码策略两种方式：
​		mysql> set global validate_password_policy=0;
​		mysql> set global validate_password_policy=LOW;
​		注：密码策略分四种
​		1、OFF（关闭） 2、LOW（低） 3、MEDIUM（中） 4、STRONG（强）
​		修改密码长度           
​		上边改完策略之后我们在改长度 mysql> SET GLOBAL validate_password_length=4;

## 	2.9 都改完之后查看密码规则

​		mysql> SHOW VARIABLES LIKE 'validate_password%';

## 	2.10 改为简单密码

​		接下来就可以将刚才的复杂密码改为简单的四位的密码了；

## 	2.11 卸载安装源自动更新

​		此时还有一个问题，就是因为安装了Yum Repository，以后每次yum操作都会自动更新，因为当前数据库已安			装完成，所以把这个卸载掉：
​	yum remove mysql57-community-release.noarch

## 	2.12 初始化数据库

​		mysql_secure_installation


