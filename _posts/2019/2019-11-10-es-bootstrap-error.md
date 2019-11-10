---
layout: post
title:  elasticsearch启动错误
no-post-nav: true
category: nosql
tags: [elasticsearch]
excerpt: elasticsearch nosql
---
## 错误

1.1 root用户启动elasticsearch报错

         Elasticsearch为了安全考虑，不让使用root启动，解决方法新建一个用户，用此用户进行相关的操作。如果你用root启动，会出现“java.lang.RuntimeException: can not runelasticsearch as root”错误。
```
org.elasticsearch.bootstrap.StartupException: java.lang.RuntimeException:can not run elasticsearch as root
at org.elasticsearch.bootstrap.Elasticsearch.init(Elasticsearch.java:125) ~[elasticsearch-6.1.1.jar:6.1.1]
at org.elasticsearch.bootstrap.Elasticsearch.execute(Elasticsearch.java:112) ~[elasticsearch-6.1.1.jar:6.1.1]
at org.elasticsearch.cli.EnvironmentAwareCommand.execute(EnvironmentAwareCommand.java:86) ~[elasticsearch-6.1.1.jar:6.1.1]
at org.elasticsearch.cli.Command.mainWithoutErrorHandling(Command.java:124) ~[elasticsearch-cli-6.1.1.jar:6.1.1]
at org.elasticsearch.cli.Command.main(Command.java:90) ~[elasticsearch-cli-6.1.1.jar:6.1.1]
at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:92) ~[elasticsearch-6.1.1.jar:6.1.1]
at org.elasticsearch.bootstrap.Elasticsearch.main(Elasticsearch.java:85) ~[elasticsearch-6.1.1.jar:6.1.1]
Caused by: java.lang.RuntimeException: can not run elasticsearch as root
at org.elasticsearch.bootstrap.Bootstrap.initializeNatives(Bootstrap.java:104) ~[elasticsearch-6.1.1.jar:6.1.1]
at org.elasticsearch.bootstrap.Bootstrap.setup(Bootstrap.java:171) ~[elasticsearch-6.1.1.jar:6.1.1]
at org.elasticsearch.bootstrap.Bootstrap.init(Bootstrap.java:322) ~[elasticsearch-6.1.1.jar:6.1.1]
at org.elasticsearch.bootstrap.Elasticsearch.init(Elasticsearch.java:121) ~[elasticsearch-6.1.1.jar:6.1.1]
... 6 more
```
解决方法：

我们需要添加用户。

adduser ***   //添加用户

passwd ***  //给用户赋值

添加完用户之后：

用root用户执行 ： chown -R 文件夹名 用户名

将这几个压缩包所在的文件夹及解压完的文件夹权限给你新建的用户。之后再使用新用户启动就OK了。

1.2 max_map_count过小

错误“max virtual memory areas vm.max_map_count [65530]is too low, increase to at least [262144]”，max_map_count文件包含限制一个进程可以拥有的VMA(虚拟内存区域)的数量，系统默认是65530，修改成262144。解决方法是修改/etc/sysctl.conf配置文件，添加vm.max_map_count=262144，记得需要重启机器才起作用。

1.3 max file descriptors过小

错误“max file descriptors [65535] for elasticsearchprocess is too low, increase to at least [65536]”，maxfile descriptors为最大文件描述符，设置其大于65536即可。解决方法是修改/etc/security/limits.conf文件，添加“* - nofile65536 * - memlock unlimited”，“*”表示给所有用户起作用。