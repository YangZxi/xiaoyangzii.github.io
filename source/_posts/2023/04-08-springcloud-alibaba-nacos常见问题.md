---
layout: post
title: springcloud+alibaba+nacos常见问题
date: 2023-04-08 21:56:27
tags:
  - java
  - springcloud
  - nacos
---

## 说明
此贴收集在使用`springcloud-springcloud-alibaba-nacos`中常见问题
版本说明
|  名称   | 版本  |
|  :----:  | :----:  |
| nacos | 2.1.1 |
| spring-boot  | 2.7.6 |
| spring-cloud  | 2021.0.5 |
| spring-cloud-alibaba  | 2021.0.5.0 |


## 问题集锦

### spring-cloud
#### 开启@LoadBalanced后，仍报服务找不到错误
问题原因：在新版本中spring-cloud中，官方放弃了ribbon，使用loadbalancer
``` xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-loadbalancer</artifactId>
    <version>2.2.0.RELEASE</version>
</dependency>
```

### nacos
#### 调整权重不生效，还是轮询 
问题原因：同[开启@LoadBalanced后，仍报服务找不到错误](#开启-LoadBalanced后，仍报服务找不到错误)，所以需要手动配置开启负载均衡
``` yaml
spring:
  cloud:
    loadbalancer:
      nacos:
        enabled: true
```

