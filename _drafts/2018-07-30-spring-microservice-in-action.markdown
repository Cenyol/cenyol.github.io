---
layout: post
title: Spring Microservice in Action
date: 2018-07-30 15:32:24.000000000 +08:00
---

### 说明

一直想系统的好好学学分布式微服务架构相关的知识，但是一直找不到怎么下手，比如如何开始搭建、搭建之后如何进行高并发的测试来验证分布式的效果之类的，就是一直没开始但一堆想法理不清，最烦这样的。最近再看《Spring Microservice in Action》一书，昨晚简单看了下大纲，感觉还不错，有点符合我想要的知识。所以打算这样：先跟着《Spring Microservice in Action》纵向大概了解一下分布式架构的一些典型套路，宏观上有个整体的概念，之后在分别从各个模块横向深入学习各种具体的工具的使用方法，分别总结对比优缺点，这样后面进行技术选型的时候也比较有底。这些模块主要有：
- 消息队列：ActiveMQ、RabbitMQ、Kafka
- 网关服务：Zuul
- 缓存：Redis
- 数据库：MySQL、MongoDB
- 服务发现治理：Eureka、Consul、Dubbo
- 日志分析：ES、Logstash、Kibana
- 监控服务：PinPoint
- CI：Gitlab与Pig(一个自动分布式部署的工具)
- 负载均衡和限流：Ribbon
- 配置服务：Spring Cloud Config Service、Apollo
- 代码开发框架：Spring Boot&Grails、Go语言
- Docker相关：常用的镜像(比如Alphine)、[Dockerfile与Compose的用法](https://yeasy.gitbooks.io/docker_practice/cases/os/alpine.html)、Kubernetes等，这块比较随意

以上列表后面会不断完善和补充的，现在刚开始学习只是把之前听到的看到的零零碎碎写上去。所以打算写个系列(系列名就叫做Microservice，如何使用Tag看[这里](https://codinfox.github.io/dev/2015/03/06/use-tags-and-categories-in-your-jekyll-based-github-pages/))，当做是自己的学习过程的记录，本文先把《Spring Microservice in Action》的学习记录写上，后面再分别研究每个工具的时候，再分别开一篇来记录，每个模块同类的工具都尽量的去了解，然后用一篇日记总结对比。以前学习选型工具的时候基本都是看到一个就一直用到尾，也没去多对比对比，局限了自己的视野。比如之前就一直使用PHP里的Yii2，也不懂的去了解了解laveral之类的，再大点也没去深入学习Java或者Go或者Python等等，导致自己一直在Yii2里面的一亩三分地，美其名曰精通一个工具就行。精通归精通，但是对其他的工具也是要有基本的认识，毕竟存在即合理，任何事物都有其他特长嘛。

不知道干什么的时候，就看看这本书，或者玩一玩上述的工具，每周一个，感觉要好几个月才能基本的了解完，慢慢来。

### 第一章


### 第二章 使用Spring Boot来创建微服务

设计微服务架构时需要考虑的三个关键点:
- 对业务问题进行解耦，注意你在描述问题时所使用的动名词，有助于解耦的思路
- 建立合理的服务粒度，关注服务的责任任务、如何与其他服务进行交互等
- 定义服务接口

以下几点可以说明微服务粒度不合理，前三点说明太粗了，后几点说明太细粒度了：
- 服务有了太多的责任，也就是说需要做的事情太多啦
- 关联管理了太多的数据表
- 有着太多的测试样例
- 服务成为了某个domain的一部分，就像养兔子一样，哈哈，直译
- 服务A严重依赖服务A
- 服务成了简单的CRUD的集合，微服务的拆分更多是根据业务逻辑而不是基于数据进行增删改查的

微服务架构应该是个逐渐演变的过程，在这个过程中虽然不能一开始就拥有正确的设计方案，但是可以避免过度设计，这也是为什么从粗粒度开始开发的原因，然后时刻根据业务的具体需要再进行服务的拆分，避免教条化的架构设计。

服务接口的一些设计原则，这些原则可以让接口更加易用易理解：
- 拥抱REST哲学，使用这些HTTP动词去建立行为模型
- 使用URI去交流意图
- 使用Json作为数据交互格式
- 使用HTTP status code来表示结果状态，成功还是失败

对于运维分布式结构的系统时，有这么几点需要注意：
- 服务应该要自包含、可独立运行部署，也就是服务集成
- 可配置，启动的时候可以自动去配置中心获取，也就是服务引导
- 实例对客户端透明，即对于客户端来说，它应该只需要通过逻辑地址去服务中心获取服务，而不必要知道具体服务实例的实际地址，也就是服务注册发现
- 应该要时刻上报自身的健康状况，以便挂掉的时候客户端绕过这个路由到其他正常的服务，也就是服务监控

### 第三章 使用Spring Cloud配置管理服务



### 第四章 服务发现

服务发现比如Netflix公司开源的Eureka和Ribbon，前者用来服务治理，比如服务注册、服务健康管理、服务地址映射等，是属于比较中心化的概念；而后者Ribbon则是用在客户端，拥有客户端缓存、负载均衡等功能。这两者都属于Spring Cloud的子模块，可以很方便的集成到Spring Boot开发的项目或者微服务中。

Feign 了解一下

### 第五章 Hystrix异常监控处理

Circuit Breaker，熔断模式

Fallback，服务降级，PlanB模式

Bulkhead，隔离运行，沙盒模式


### 第六章 Zuul网关路由

这个网关的模式好像是：客户端向Zuul发起请求，然后Zuul自己再以客户端的身份重新再向目标服务提供者发起另一个HTTP请求。这样做之后，它就可以各种插手各种干扰了，过分！

网关的几点注意事项：
1. 静态路由和动态路由

2. 认证和授权

3. 指标收集和记录


网关是否会成为单点故障和潜在的性能瓶颈，记住以下几点：
1. 在网关前面跑个负载均衡是个合适的设计，也可以保证多网关的可扩展性。但是在所有的服务实例前面跑个负载均衡并不是个好的主意呢；
2. 保持服务网关功能代码的无状态性，就是说，不要再网关代码里面保存任何的信息，否则会限制它的扩展能力。每个网关实例看起来应该是一个个克隆体，拥有一致的副本，互不依赖。
3. 保持服务网关的轻巧，如果网关太重，那么就很可能成为一个瓶颈，性能阻塞点。要是在网关里面涉及到了数据库调用，那么就很容易成为性能问题的根源了，切记。

Zuul里面路由配置的几种方式：
1. 通过Eureka服务发现来自动映射路由
2. 通过Eureka服务发现来手动映射路由，用于：当你觉得服务名称很长觉得全称都显示在Url上会不好看的时候，可以用这个
3. 手动映射静态路由。这个主要是用在当一些服务不是由Java跑的时候，比如Go啊，PHP啊，或者Python之类的。由于是手动静态路由，所以默认情况下是不会触发Ribbon来进行负载均衡这个静态路由的，因为Ribbon已经与Eureka整合在一起。如果一定要用Ribbon来进行负载均衡，那么就需要关掉Ribbon与Eureka的关联，再在静态路由上面配置使用Ribbon进行路由。这样做带来的问题就是，影响到其他注册在Eureka上的服务的负载功能，而且由于关了Ribbon，那么Zuul网关每次在向Eureka查询服务的地址之后都不会将结果缓存到本地，因而这会大大加大了Eureka的负担。所以通常不建议关闭Zuul上Ribbon与Eureka的功能。如果真有一些非JVM的程序，那么可以另开一台Zuul服务，来单独配置Ribbon，这里需要使用到[Spring Cloud Sidecar](http:// cloud.spring.io/spring-cloud-netflix/spring-cloud-netflix.html#spring-cloud-ribbon-without-eureka)这个服务来集成Ribbon。

动态重载路由配置信息。链接到配置中心服务之后，如果配置中心服务使用了Git来保存配置信息，那么当使用Git push了新信息之后，Zuul会暴露一个POST 接口用来刷新路由配置信息的，/refresh。

为了安全起见，Zuul也用了Hystrix和Ribbon，后者前文已经提过。至于使用了Hystrix，默认情况下如果Zuul里任何调用耗时超过1秒，Zuul将会返回500给客户端，当然了这个是可以自定义的。比如在Zuul配置文件里做如下配置：

```
hystrix.command.default.execution.isolation.thread.timeoutInMilliseconds: 2500
```
就可以把默认的1秒修改为2.5秒啦，全局有效。如果对某个特定的服务有定义单独的超时值，可以替换前面配置中的那个**default**字段为对应服务在Eureka中的服务ID，比如对于licensingservice的超时时间想配置为3秒，那么可以这样：

```
hystrix.command.licensingservice.execution.isolation.thread.timeoutInMilliseconds: 3000
```

该注意的是，对于超时这个配置项，在配置了Hystrix之后，记得Ribbon上面也进行配置。

在Zuul网关上，可以实现比较强大的功能就是实现一些Filter，有三类Fileter分别是pre-、post和route filter这三类，代表分别是：
1. TrackingFiler，通过产生一个唯一的关联ID来识别客户端请求所经历的事件，所经过的服务，以便后面进行一些统计分析操作，比如日志聚合。
2. SpecialRoutesFilter，有点重定向的感觉，但是据说不是重定向。反正就是路由重置，用在A/B测试的时候有用。
3. ResponseFilter，可以完成将关联ID注入到相应的请求中，以便客户端进行识别访问。还可以做一些审计、敏感信息过滤之类的。




### 总结


### 参考

- [《Microservice》](https://martinfowler.com/articles/microservices.html)，Martin Fowler大哥写的
- 

