---
layout: post
title: 一些常见的Docker官方镜像的常见用法
date: 2018-08-01 15:32:24.000000000 +08:00
---
### 说明

Docker官方也慢慢的把一些常用镜像迁移到Alpine上，这就很棒棒了，毕竟小啊。本文主要介绍一些平时经常使用的镜像的用法，当然了大都是基于Alpine的，比如Redis、MySQL、PostgreSQL、RabbitMQ之类的。

### Redis镜像

既然官方的镜像就是基于Alpine，那么使用的时候直接这样：
```
docker run -it -p 6379:6379 redis --requirepass "mypassword"
```
就OK啦，后面的 --requirepass "mypassword"这个参数是redis用来设置访问密码专用的，前面的-p参数映射端口。

如果觉得每次直接run需要下载镜像比较麻烦的话，可以先pull到本地：
```
docker pull redis
```

### MySQL镜像

先上脚本：
```
docker run -d --name mysql -p 3306:3306 -v $(pwd):/app -e MYSQL_DATABASE=testdb -e MYSQL_USER=cenyol -e MYSQL_PASSWORD=password -e MYSQL_ROOT_PASSWORD=rootpassword mysql
```
参数MYSQL_USER与MYSQL_PASSWORD是用来设置新用户名和密码的，如果一会不想通过root账号来访问，就可以使用这个啦。MYSQL_ROOT_PASSWORD是用来设置root账号的密码，还有就是MYSQL_DATABASE新建数据库。
-d参数用来后台运行容器，和-it相反。


### PostgreSQL镜像

官方Dockerfile仓库地址点[这里](https://github.com/docker-library/postgres)，然后找个最新版基于Alpine的build一下就好了。比如我：
```
```
然后运行：
```
docker run --name postgres -e POSTGRES_PASSWORD=password -p 5432:5432 -d cenyol/alpine-postgres
```
其中，POSTGRES_PASSWORD参数用来设置账号postgres的密码。后面登录的时候要用。登录方式有多种，可以使用命令行、UI管理工具、或者直接JDBC里面连上。比如下命令行：
```
psql -U postgres -h 192.168.100.172 -p 5432
```
这个镜像构建还蛮久的，至少比上面的Redis和MySQL久了不少。懒得等，直接使用官方pull下来的镜像来用。


### RabbitMQ镜像

后面用到了再补充吧这个。基本思路类似，从官方提供的Dockerfile找个基于Alpine的构建，然后运行。


### Java运行时镜像

Java应用可能会用到。

### Nginx镜像

多个应用跑在同一台服务器上面，可以根据Nginx来监听不同的域名，转发到不同的端口来进行区分。

### 总结

发现了个规律，就是自己通过官方提供的Dockerfile build之后的镜像比官方提供的小了不小，难道是我错了，官方还没把镜像迁移到Alpine上面吗？我们不排除这个可能。所以还是自己维护好这几个镜像，后面传到阿里云的镜像库去吧，记得描述清楚相关信息，方便直接run使用。

### 参考
- [MySQL 官方Docker镜像的使用](https://itbilu.com/linux/docker/EyP7QP86M.html)
- [IT笔录中Docker相关文章](https://itbilu.com/linux/docker)
- [Docker安装PostgreSQL](https://blog.csdn.net/liuyueyi1995/article/details/61204205)
