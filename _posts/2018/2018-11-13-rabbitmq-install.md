---
layout: post
title:  rabbitmq安装（centos7.4）
no-post-nav: true
category: others
tags: [rabbitmq]
excerpt: rabbitmq安装
---

# rabbitmq简单安装记录

- Yum安装socat  

  \# yum -y install socat 

   出现如下错误：![rabbit-001](https://angrycow1111.github.io/assets/images/2018/it/rabbit-001.png)

  解决方法：

  - 卸载自带yum，重新安装yum

  ```linux
  rpm -aq|grep yum|xargs rpm -e --nodeps
  ```

  - 去网址下载rpm包  [http://mirrors.163.com/centos/7/os/x86_64/Packages](http://mirrors.163.com/centos/7/os/x86_64/Packages)

    ```linux
    python-libs-2.7.5-58.el7.x86_64.rpm
    python-2.7.5-58.el7.x86_64.rpm
    python-iniparse-0.4-9.el7.noarch.rpm
    python-pycurl-7.19.0-19.el7.x86_64.rpm
    rpm-python-4.11.3-25.el7.x86_64.rpm
    yum-metadata-parser-1.1.4-10.el7.x86_64.rpm
    
    yum-3.4.3-154.el7.centos.noarch.rpm 
    yum-plugin-fastestmirror-1.1.31-42.el7.noarch.rpm
    ```

    下载好之后前面6个包依次安装,安装命名如下

    ```linux
    rpm -ivh *.rpm
    ```

  ​	后面的2个包,是互相依赖，所以一起安装



  ```linu
  rpm --nodeps --force -ivh yum-3.4.3-154.el7.centos.noarch.rpm
  
  yum-plugin-fastestmirror-1.1.31-42.el7.noarch.rpm
  ```



- 修改yum源


进入yum源目录

```linux
cd  /etc/yum.repos.d
```

下载文件

```
wget http://mirrors.163.com/.help/CentOS7-Base-163.repo
```

修改repo源：   将文件中的$releasever替换成7 （7是指LINUX版本号）, 下面是本人改了之后的截图

![rabbit-yum-001](https://angrycow1111.github.io/assets/images/2018/it/rabbit-yum-001.png)
=======
    ![rabbit-yum-001](https://angrycow1111.github.io/assets/images/2018/it/rabbit-yum-001.png)
>>>>>>> 75a8b18bdf000c5f788181cc81cd90decdbaff11

​	运行makecache 生成缓存

```linux
yum makecache 
```

运行yum clean all

```linux
yum clean all
```

更新yum文件(等待时间有点长)

```linux
yum update
```

装好后就可以安装 rabbitMQ

```linux
   rpm -ivh rabbitmq-server-3.6.9-1.el6.noarch.rpm
  Preparing...           ########################################### [100%]
  1:rabbitmq-server ########################################### [100%]装好后就可以安装 rabbitMQ 了。
```

- 启动rabbitmq

```linu&#39;x
./rabbitmq-server start
```



- 如果要安装web管理，执行以下指令：


```linu
 rabbitmq-plugins enable rabbitmq_management
```



- 然后就可以访问：


```linux
http://locahost:15672
```

