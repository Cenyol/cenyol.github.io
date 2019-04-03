---
layout: post
title: 常用数字检验相关函数
date: 2019-01-24 11:46:39.000000000 +08:00
---

### 常用数字检验相关函数

#### org.apache.commons.lang3.StringUtils.isNumeric(string)

根据源码介绍

> Checks if the String contains only unicode digits. A decimal point is not a unicode digit and returns false.

可知只有当string仅包含了0~9这10个unicode编码的字符时才返回true，比如：1、432、0，其他情况一律返回false，包括-1，0.1等都是false，因为“-”和“.”不是unicode digits。

另外，以Unicode表示的digits也是返回true的，比如当string="\u0967\u0968\u0969"，返回true，至于这个Unicode是表示多少，咨询Google。

#### org.apache.commons.lang3.math.NumberUtils.isDigits(string)

等价于上述的**StringUtils.isNumeric(string)**，根据源码可知，该方法在内部直接调用了isNumeric()，委托了。


#### org.apache.commons.lang3.math.NumberUtils.isParsable(string)

> 检验提供的字符串是否可以转换为number,可解析的number包括下面的方法Integer.parseInt(String), Long.parseLong(String), Float.parseFloat(String) or Double.parseDouble(String)，这个方法可以替代ParseException异常当调用上面的方法时；
> 
>十六进制和科学符号被认为是不可解析的；
>
>null和空字符串返回false。


#### org.apache.commons.lang3.math.NumberUtils.isCreatable(string)

>检查字符串是否是一个有效的number,有效数字包括进制标有0x或0X预选项，八进制数、科学记数法和标有类型限定符的数字，以前导零开头的非十六进制字符串被视为八进制值，因此字符串09将返回false,因为9不是有效的八进制，然而从0开始的数字，被视为十进制，null、空或者空串将返回false;
 参数。
 
 
 下图是对上述的一些简单验证
 ![](http://mdpic.cenyol.com/2019-01-24-15483007800565.jpg)

结果如下
![](http://mdpic.cenyol.com/2019-01-24-15483008308743.jpg)


### 参考
 - [Java工具类NumberUtils详解](https://blog.csdn.net/yaomingyang/article/details/79132653)



