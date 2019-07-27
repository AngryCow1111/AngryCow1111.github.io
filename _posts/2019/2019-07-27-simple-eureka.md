---
layout: post
title:  Eureka快速创建注册中心
no-post-nav: true
category: springboot
tags: [springboot]
excerpt: eureka springboot 注册中心
---

# Eureka快速创建注册中心## 创建springboot项目
- pom.xml文件如下:

```
<?xml version="1.0" encoding="UTF-8"?><project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">    <modelVersion>4.0.0</modelVersion>    <parent>        <groupId>org.springframework.boot</groupId>        <artifactId>spring-boot-starter-parent</artifactId>        <version>1.5.21.RELEASE</version>        <relativePath/> <!-- lookup parent from repository -->    </parent>    <groupId>com.angrycow1111</groupId>    <artifactId>register-center</artifactId>    <version>0.0.1-SNAPSHOT</version>    <name>register-center</name>    <description>Demo project for Spring Boot</description>    <properties>        <java.version>1.8</java.version>        <spring-cloud.version>Edgware.SR6</spring-cloud.version>    </properties>    <dependencies>        <!--eureka-->        <dependency>            <groupId>org.springframework.cloud</groupId>            <artifactId>spring-cloud-starter-eureka-server</artifactId>        </dependency>        <!--actuator-->        <dependency>            <groupId>org.springframework.boot</groupId>            <artifactId>spring-boot-starter-actuator</artifactId>        </dependency>        <!--processor-->        <dependency>            <groupId>org.springframework.boot</groupId>            <artifactId>spring-boot-configuration-processor</artifactId>            <optional>true</optional>        </dependency>        <dependency>            <groupId>org.springframework.boot</groupId>            <artifactId>spring-boot-starter-test</artifactId>            <scope>test</scope>        </dependency>    </dependencies>    <dependencyManagement>        <dependencies>            <dependency>                <groupId>org.springframework.cloud</groupId>                <artifactId>spring-cloud-dependencies</artifactId>                <version>${spring-cloud.version}</version>                <type>pom</type>                <scope>import</scope>            </dependency>        </dependencies>    </dependencyManagement>    <build>        <finalName>register-center</finalName>        <plugins>            <plugin>                <groupId>org.springframework.boot</groupId>                <artifactId>spring-boot-maven-plugin</artifactId>            </plugin>        </plugins>    </build></project>```## 配置注册中心- application.yml如下:```ymlserver:  port: 1111eureka:  environment: prod  instance:    prefer-ip-address: true    hostname: 127.0.0.1  client:    register-with-eureka: false    fetch-registry: false    service-url:      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```

相关配置参数如下标：

|参数|可选值|描述
|---|---|---|
|eureka.instance.prefer-ip-address | true/false |---|
|eureka.client.fetch-registry | true/false | 是否要检索服务|
| eureka.client.register-with-eureka | true/false | 是否注册到注册中心 |
| eureka.client.fetch-registry | true/false | 是否要检索服务 |
| eureka.client.service-url.defaultZone | http://ip:port/eureka/或者http://hostname/eureka/ | 注册中心地址 |
| eureka.instance.prefer-ip-address | true/false | 是否使用ip注册服务 |
| eureka.instance.hosthome | peer1 | 域名   |      | eureka.instance.ip-address | 127.0.0.1 | prefer-ip-address为true时使用 |

- 开启注册中心功能使用@EnableEurekaServer注解开启注册中心功能。