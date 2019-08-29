---
layout: post
title:  springboot整合rabbitmq
no-post-nav: true
category: springboot
tags: [rabbitmq]
excerpt: rabbitmq springboot
---
# springboot整合rabbitmq
## 添加依赖
pom.xml 如下
```xml
 <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-amqp</artifactId>
</dependency>
```
## 配置rabbitmq
- application.xml如下：
```xml
spring:
    rabbitmq:
        host: localhost
        port: 5672
        publisher-confirms: true
        virtual-host: /
        username: admin
        password: admin
```
配置参数如下：
|参数|可选值|描述|
--|--|--
|host|peer1(可自定义)|域名
|port|默认5672|端口
|publisher-confirms|true/false|是否开启消息确认
|virtual-host|/|虚拟域名
|username|guest（默认）|用户名
|password|guest（默认）|密码
- 配置类
```java
@Configuration
public class RabbitmqConfiguration {

    @Bean
    public RabbitTemplate rabbitTemplate(ConnectionFactory connectionFactory) {
        RabbitTemplate rabbitTemplate = new RabbitTemplate(connectionFactory);
        rabbitTemplate.setMessageConverter(new Jackson2JsonMessageConverter());
        return rabbitTemplate;

    }

    @Bean
    public SimpleRabbitListenerContainerFactory rabbitListenerContainerFactory(ConnectionFactory connectionFactory) {
        SimpleRabbitListenerContainerFactory simpleRabbitListenerContainerFactory = new SimpleRabbitListenerContainerFactory();
        simpleRabbitListenerContainerFactory.setConnectionFactory(connectionFactory);
        simpleRabbitListenerContainerFactory.setMessageConverter(new Jackson2JsonMessageConverter());
        return simpleRabbitListenerContainerFactory;

    }
```
**RabbitTemplate:** 发送消息   
默认MessageConvertor是SimpleMessageConverter，可以处理字符串或者字节数组，而我们一般需要处理json，所以设置为Jackson2JsonMessageConverter。

**SimpleRabbitListenerContainerFactory：**  接收消息
默认MessageConvertor是SimpleMessageConverter，可以处理字符串或者字节数组，而我们一般需要处理json，所以设置为Jackson2JsonMessageConverter。
   
## 实现生产者
示例代码如下：
```java
@Component
public class RabbitmqSender {

    @Autowired
    private RabbitTemplate rabbitTemplate;

    public void send(String queueName, Object message) {
        this.rabbitTemplate.convertAndSend(queueName, message);
    }
}
```
## 实现消费者
示例代码如下：
- 抽象消费者
```java
public interface AbstractConsumer<Q> {

    Q init();
}
```
- 具体消费者
```java
@RabbitListener( queues = "user-service", containerFactory = "simpleRabbitListenerContainerFactory" )
public class UserServiceConsumer implements AbstractConsumer<UserQueue> {


    @RabbitHandler
    public void process(@Payload User user) {
        System.out.println(JSON.toJSONString(user));
    }


    @Bean
    @Override
    public UserQueue init() {
        return new UserQueue("user-service");
    }
}
```

## 问题
1. 
reply-code=530, reply-text=NOT_ALLOWED - access to vhost '/' refused for user user_admin

解决方法：
定位发现是连接rabbitmq使用的用户没有赋予访问权限，我创建的是admin用户，给admin用户赋予‘/’目录的访问权限就可以，执行如下命令：
```shell
sudo rabbitmqctl  set_permissions -p / admin '.*' '.*' '.*'
```