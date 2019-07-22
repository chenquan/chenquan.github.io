---
layout: post
title: spring-eureka注册中心自我保护机制
categories: [spring]
description: spring-eureka注册中心自我保护机制
---



# spring-eureka注册中心自我保护机制

## 自我保护机制的背景



![spring-eureka](/img/picture/20190722/spring-eureka.png)

首先对Eureka注册中心需要了解的是Eureka各个节点都是平等的，没有ZK中角色的概念， 即使N-1个节点挂掉也不会影响其他节点的正常运行。

默认情况下，如果Eureka Server在一定时间内（默认90秒）没有接收到某个微服务实例的心跳，Eureka Server将会移除该实例。但是当网络分区故障发生时，微服务与Eureka Server之间无法正常通信，而微服务本身是正常运行的，此时不应该移除这个微服务，所以引入了自我保护机制。

## 自我保护机制

官方对于自我保护机制的定义：

https://github.com/Netflix/eureka/wiki/Understanding-Eureka-Peer-to-Peer-Communication

自我保护模式正是一种针对网络异常波动的安全保护措施，使用自我保护模式能使Eureka集群更加的健壮、稳定的运行。

## 问题
自我保护机制的工作机制是如果在15分钟内超过85%的客户端节点都没有正常的心跳，那么Eureka就认为客户端与注册中心出现了网络故障，Eureka Server自动进入自我保护机制，此时会出现以下几种情况：

1、Eureka Server不再从注册列表中移除因为长时间没收到心跳而应该过期的服务。

2、Eureka Server仍然能够接受新服务的注册和查询请求，但是不会被同步到其它节点上，保证当前节点依然可用。

3、当网络稳定时，当前Eureka Server新的注册信息会被同步到其它节点中。

因此Eureka Server可以很好的应对因网络故障导致部分节点失联的情况，而不会像ZK那样如果有一半不可用的情况会导致整个集群不可用而变成瘫痪。

例如：我们在使用Eureka的时候，如果服务提供者我们没有让它正确退出的话（正确测试退出使用IDEA可以点击左下角的Exit而不是Stop），Eureka控制面板是可以看到提供者依然还在运行（实际已经Down，但显示Up），这导致了我们在访问该服务时会出现错误

## 解决方案

### 1）关闭自我保护开关

Eureka自我保护机制，通过配置 eureka.server.enable-self-preservation来true打开/false禁用自我保护机制，默认打开状态，建议生产环境打开此配置。

服务端配置：

```yaml
eureka:
  server:
    # 测试时关闭自我保护机制，保证不可用服务及时踢出
    enable-self-preservation: false
```

客户端配置：

```yaml

# 心跳检测检测与续约时间
# 测试时将值设置设置小些，保证服务关闭后注册中心能及时踢出服务
eureka:
  instance:
    lease-renewal-interval-in-seconds: 1
    lease-expiration-duration-in-seconds: 2
```

以上配置说明

lease-renewal-interval-in-seconds 每间隔1s，向服务端发送一次心跳，证明自己依然”存活“。

lease-expiration-duration-in-seconds  告诉服务端，如果我2s之内没有给你发心跳，就代表我“死”了，将我踢出掉。

### 2）快速切换请求或断路器
使用负载均衡的方式，设置当一个请求超过多少秒之后，切换请求下一个注册服务，例如使用Ribbon+Hystrix配置实现负载均衡和断路器

