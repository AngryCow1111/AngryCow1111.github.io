---
layout: post
title:  阿里云安装和简单使用docker
no-post-nav: true
category: others
tags: [others]
excerpt: 阿里云 docker mysql
---

# 阿里云安装和简单使用docker

## 安装docker

### step 1: 安装必要的一些系统工具

```linux
sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```
### Step 2: 添加软件源信息
    sudo yum-config-manager --add-repo  http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
### Step 3: 更新并安装Docker-CE
    sudo yum makecache fast
    sudo yum -y install docker-ce
### Step 4: 开启Docker服务
    sudo service docker start

## docker安装mysql
###  搜索mysql镜像)
    docker search mysql
    
### 下载mysql镜像，默认最新版
    docker pull mysql 
    
### 启动mysql
    docker run -p 53306:3306 -v $PWD/mysql:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=password --name mysql5719 -d 16f9fffc75d8
    -p53306:3306：将容器的3306端口映射到主机的3306端口；
    
    -v$PWD/mysql:/var/lib/mysql：将主机当前目录下的/mysql挂载到容器的/var/lib/mysql；
    
    -e MYSQL_ROOT_PASSWORD=password：初始化root用户的密码；
    
    --name 给容器命名，mysql5719；
    
    -d 表示容器在后台运行 (错误【是刚才下载的mysql的imageID，也就是我们所说的镜像ID】)
    
## 连接mysql
内部连接：   
### 进入mysql容器
    docker exec -it containerID bash
### 登录mysql
    mysql -uroot -p
    enterpassword
外部navicat连接：
### 配置mysql连接



![mysql-docker-setting](https://angrycow1111.github.io/assets/images/2018/it/mysql-docker-setting.jpg)

![mysql-docker-setting1](https://angrycow1111.github.io/assets/images/2018/it/mysql-docker-setting1.jpg)



## docker常用命令

### 启动container
    docker start containerID
### 关闭container
    docker stop containerID
### 查看全部container
    docker ps -a
### 查看运行中的container
    docker ps
### 查看container详情
    docker inspect containerID
### 查看dockerIP
    docker inspect -f '{{ .NetworkSettings.IPAddress }}' 8a20433827c6
### 删除container
    docker -rm containerID
### 查看镜像
    docker images
### 删除镜像
    docker rmi imageID

