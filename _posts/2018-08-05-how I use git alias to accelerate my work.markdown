---
layout: post
title: 大部分时间我是这么使用Git
date: 2018-08-05 19:29:24.000000000 +08:00
---

### 背景
Git是个好东西，有诸多规范，也有大量命令，容易让人迷糊，这里简单说说平时主要是怎么使用它。

### 配置
```
alias gl="git log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit'"
alias ga='git add -A'
alias gb='git branch'
alias gk='git checkout'
alias gc='git commit -m'
alias gm='git merge'
alias gs='git status'
alias gps='git push'
alias gpl='git pull'
```

### 使用
**gs** 等价于**git status** 用于查看当前的修改状态，多少未track、多少未commit等，这个使用频率应该是最高的了，在Git命令树中。

**ga** 等价于**git add -A**  可以直接将当前目录下面所有未commit的，未track的文件统统添加到Git中。

**gc 'feat: a new function'** 等价于**git commit -m 'feat: a new function'** 也就是提交当前目录下未提交的修改记录

**gps** 等价于**git push** 假设你在master分支，这个需要先执行：**git push --set-upstream origin master**，大概的意思就是将本地master分支与远程origin仓库中的master关联，然后你在本地master分支之下直接git push就等价于执行了**git push origin master**

**gpl** 等价于**git pull**，后面的同上**git push**

上面四个是最常用的命令，比使用它们的等价完整命令来说，可以节省不少时间。毕竟多人协作开发的环境下，git行为是很频繁的。

### 最后
Git和日志一样，它是我们开发过程中，代码变化的记录过程，在当代码想要进行分支回滚、责任归属、版本切换等操作的时候是很方便的，所以对于commit信息要写的尽量清晰，不可模糊。[commit规范](http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)可参考，把其中的type记下来，在工作中使用，有好处。

根据2-8法则，简化最常用的那20%命令，可以节约80%的时间也不一定呢。

### 参考
- [git的使用和别名配置](https://www.jianshu.com/p/5c4511c7dd88)
- [commit规范](http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)
- [优雅的提交你的 Git Commit Message](https://juejin.im/post/5afc5242f265da0b7f44bee4)

