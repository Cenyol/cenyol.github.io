---
layout: post
title: Docker(一) - Dockerfile初步使用
date: 2019-04-02 11:46:39.000000000 +08:00
---

#### Example

先来看个常见的Dockerfile内容

```
FROM openjdk:8-jre-alpine
MAINTAINER cenyol <mr.cenyol@gmail.com>

# 一些参数，这里表示zk的版本名称，也是后面的目录名称
ARG DISTRO_NAME=zookeeper-3.4.14
ARG ZK_ZIP="$DISTRO_NAME.tar.gz"

# 先修改镜像源，墙内没办法
# Install required packages, alpine镜像必须，为了瘦身默认没有安装bash(这么基础的工具居然没有默认，有毒)
# 一些建议：尽量将所有命令放在一个RUN里面，以减少docker层。另外，wget的压缩包可以在解压之后就删掉，瘦身镜像
RUN echo 'https://mirrors.ustc.edu.cn/alpine/v3.9/main' > /etc/apk/repositories \
    && apk add --no-cache bash su-exec \
    && java -version \
    && wget -O $ZK_ZIP  http://mirrors.shu.edu.cn/apache/zookeeper/stable/$ZK_ZIP \
    && tar -xzf "$ZK_ZIP" \
    && cd /$DISTRO_NAME/conf \
    && mv zoo_sample.cfg zoo.cfg \
    && ls / \
    && rm /$ZK_ZIP \
    && ls /

# 设置工作目录，等价于cd 命令
WORKDIR /$DISTRO_NAME

# 设置环境变量，以便可以在CMD中直接执行对应目录下面的程序
ENV PATH=$PATH:/$DISTRO_NAME/bin

# 暴露端口，后端在docker run里面的-p参数里，比如2181:2181，后者就是这里的端口号，前者是宿主机的端口号
EXPOSE 2181

# docker run启动之后，执行的开机命令，第一个是命令，后面接参数
CMD ["zkServer.sh", "start-foreground"]
```

#### 概念：Docker层

镜像是由Base层外加上镜像创建时候，各种docker命令产生的层所组成的，一叠层层们的集合，即镜像是个集装箱，里面装着各类层。如下图所示：
![](http://mdpic.cenyol.com/2019-04-03-15542944502676.jpg)

每一层都是在创建镜像时，留下的一个痕迹。比如Dockerfile里面的RUN、ADD、COPY等命令每次运行都会产生一层，往上碟。理论上来说，这个层的数量是有限制的。不宜过多，一般来说几个十来个还是可以接受的。

这些层形成一个镜像Im之后(称之为镜像层)，如果基于Im启动一个容器，启动的时候会默认在Im上面叠一个可写层(叫做容器层)，然后在容器上面进行写操作，包括创建新文件、删除文件等。其中，删除操作使用了个幌子。如果被删除的文件来自镜像层中，那么实际只是在容器层做了个标记，告诉顶层应用说这个文件已经被删啦，如果应用要读取直接返回不存在，就算实际它还存在，哈哈。
如果是修改镜像层中已有的数据，则是先将镜像层中的文件复制到容器层，然后对容器层中的副本进行修改，后面容器中应用对这个文件的操作都是基于容器层中的副本进行，镜像层的原本不会受到干扰。这也是镜像可以被多个容器共享的原理。

所以可以理解为，容器和镜像都是集装箱，一层层的。只不过，镜像这一叠层层是只读，不能被修改，以便进行共享的时候，不会互相影响。而容器这个集装箱可以理解为是套在镜像上面，新增了一层可读写层，以便容器内的程序运行需要。大概图下所示：
![](http://mdpic.cenyol.com/2019-04-03-15542952343066.jpg)

#### 镜像精简

记录做事后清理工作，比如rm压缩包，以精简镜像大小。如上Example中，在压缩包解压之后，就可以直接删掉，给镜像省个35M的大小。

其他的还有，清理包管理更新时候留下的缓存等，没用的东西都可以删掉。

#### Dockerfile常用命令

见：docker help

**docker exec** 往已有的容器中发送命令，有时候启动的容器是-d参数，不方便attach进去，可以通过这个来在容器中执行命令。

**docker logs** 在创建容器的时候，有时候一些错误信息没有直接在命令行进行输出，可以通过这个命令来获取特定容器的日志信息，查看更加详细的记录。

#### 使用Alpine运行Go Server

在本地电脑上编译好go文件，然后cp到alpine中进行执行：
编译命令：CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build  test.go
前面的参数是设置系统的指令架构，Mac和linux不一样，如果没有arch参数直接编译会执行失败。编译之后再丢到alpine中，发布的时候也可以避免源码泄露的风险。参考：https://my.oschina.net/u/3305368/blog/1853766

比如简单写个http server，几M就能在线上跑一个项目，还是不错的，如果使用Busybox那就更小了。

#### 实践建议

就自己来说，在个人目录下面建了个docker文件夹，用来放各种Dockerfile，无论是学习还是工作上线的时候都可以。这样就可以达到共享，减少重复工作的目的，这也是Dockerfile的目的。

#### 参考
- [Docker镜像分层技术](http://www.maiziedu.com/wiki/cloud/dockerimage/)
- [Dockerfile 最佳实践](https://yeasy.gitbooks.io/docker_practice/appendix/best_practices.html)
- [理解docker的rootfs和分层构建联合挂载的概念](https://haojianxun.github.io/2018/05/03/%E7%90%86%E8%A7%A3docker%E7%9A%84rootfs%E5%92%8C%E5%88%86%E5%B1%82%E6%9E%84%E5%BB%BA%E8%81%94%E5%90%88%E6%8C%82%E8%BD%BD%E7%9A%84%E6%A6%82%E5%BF%B5/)


