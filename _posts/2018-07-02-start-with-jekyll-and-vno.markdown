---
layout: post
title: 基于Github Pages和Jekyll搭建的博客
date: 2018-07-02 17:30:24.000000000 +08:00
---

### 说明

已有一个[博客](https://cenyol.com)用Wordpress搭建的，放在阿里云的服务器上面，有时候会考虑到万一哪天没钱续费阿里云的服务器了，那这个博客怎么办，搬到其他地方又怕麻烦，怎么也不是个长久之计，怎样才能够无忧无虑永久免费的跑个博客呢。据说Github就提供了这么一种服务，谷歌问了下，叫做[Pages](https://help.github.com/categories/github-pages-basics)，还有一个不错的工具叫做[Jekyll](https://jekyllrb.com)。还有个问题就是，这个博客之前是可以评论的，但是后来换了个爆款主题之后不知道哪里出了问题，评论功能出不来。想接一些第三方的社会化评论平台又倒闭的倒闭，麻烦的麻烦，谷歌问了下，有个叫做[Gitment](https://github.com/imsun/gitment)的不错，确实惊艳。所以就花了点时间，将各种开源的工具、源码、主题组合在一起，弄出了这个博客。

### Vno - 一个Jekyll主题模板

直接Clone下来，然后放到自己刚建的Github项目仓库中，就可以访问Vno自带的示例页面了，具体过程见[这里}(https://www.jianshu.com/p/88c9e72978b4)。

[Jekyll中文使用手册](https://jekyllcn.com)

### 自定义域名

我的域名是放在阿里云上面，直接新建个新的解析，CNAME类型的，值为：blog.cenyol.com，指向：cenyol.github.io

然后在Github仓库中配置自定义域名，在项目的settings中配置。

到这一步之后，就可以使用：http://blog.cenyol.com 直接访问本博客了，整个过程下来还是比较简单方便的，就喜欢这样的，不折腾。同时因为整个博客的内容信息都放在Github上面，某种意义上来说，算是永久免费的运行这个博客了，不要操心服务器维护的问题。

### 评论功能

前面说了关于评论功能的缺失，在寻找Gitment的过程中，看到不少人之前把评论数据一会放Disqus，一会多说，然后又是网易云跟帖，到后面又不了了之。看到Gitment的实现原理，感觉应该是稳的，因为它将评论作为Issue放在了Github仓库中，很不错嘛Gitment的使用指南看[这里](https://imsun.net/posts/gitment-introduction)。

接Gitment过程中遇到的一些问题的解决[方法1](http://liujinkai.com/2017/10/24/gitment-pinglun-chajian)、[方法2](http://xichen.pub/2018/01/31/2018-01-31-gitment)。

### 从http到htpps

先给个[链接](https://imququ.com/post/letsencrypt-certificate.html)，后面有空再弄吧这个。

### 后续

以后可能会在这个博客中时不时的放一些碎片化的技术知识，可能是开发过程中遇到一些小问题的解决方法、也可能是自己看IT书籍过程的一些小总结。
