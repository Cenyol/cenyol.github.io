---
layout: post
title: 为什么Java里面的基本类型的包装类是不可变的
date: 2018-10-20 10:32:24.000000000 +08:00
---

### 问题

早上在看《实战Java高并发设计》一书时，有个例子提到Integer是个不可变类型，类似String那样。如果对Integer i = 0; 执行i++；会得到另一个新的对象引用，指向Integer(1)，i的值是改，但是，包裹的对象也变了，是个新的，并不是在原来的Integer(0)内部对其中的value进行递增。

所以，如果按照书里的例子：
![](http://mdpic.cenyol.com/2019-01-02-55AC4BE2-BB80-48FC-A0F8-ABDD131C9C6B.png)


会导致第二个红框里面的加锁效果失效了，因为每当i改变的时候，加锁对象都是不一样的，等于没能阻止并行的发生。所以，对于Integer来说，尽量别用它作为锁对象，特别是当其会发生变化的时候。
！！此外，基础类型是不能用作锁的，尝试了使用int i去作为synchronized的参数，提示只接受reference。

！！！突然想起了之前使用final来修改Integer，因为用final int = 0；无法边改i的值，以为使用Integer就是可以偷偷修改其内部的value，但是试了下仍然不行，当时没想通。现在想来，就是Integer是个不可变量的缘故了。
看Integer的源码也会发现找不到可以修改器内部value的方法，该类并没有提供类似setValue的方法，而对Integer进行++操作估计也被重构过，语法糖，每次操作都是会返回新的Integer对象代替原来的对象。这也是Integer被final修饰之后，无法改变其值的缘故。理论上，final限制的是变量的值，对于对象来说，限制的是其引用对象的引用值(即对象地址值)，对于基础类型来说，是其变量自身的值。
但是，因为Integer在变化的时候，不让你简单的改变其内部value，而是产生新的对象来替代，所以被final修饰之后也就无法对其进行修改了。
！！！

问题来了，就是为何Integer要设计为不可变呢，操作的时候直接改变其值不是很方便吗，为何还要再去new个新对象来替代。

### 解决

从思想上来说，值，或者说整型、长整型、浮点型，比如i=5 -> i=6，这个过程来说，改变的是i自己而已，5它自己并没有变成6，只是用个与5不同的东西，叫做6来放到i这个变量里面，也就是说，平时的数字啥的，它们自身就都是一个个不可变量，嗯，有点道理。
所以放到包装类型这边也就说得通了。对此，Stack Overflow上有个回答：
![](http://mdpic.cenyol.com/2019-01-02-039C4333-DBDB-4D09-B309-67F478F2EDC4.png)


That’s not changing the number 5 - it’s changing the value of x .

！！前述基础类型不能作为synchronized的参数，只能是reference可能就是因为基础类型变量保存的值是时刻在变化，而reference相对来说较为稳定，只要reference的目标对象不变，无论对象的内容如何改变，它所保存的reference值(即地址值)也都不会改变，比较适合当做锁来使用。

还有个例子可以说明，如果Integer是个可变对象，也就是说，我们可以直接操作其内部value的时候的弊端：
![](http://mdpic.cenyol.com/2019-01-02-A42BC7DC-DF43-45B8-ADE4-8DCCC0A74449.png)


每一次，想把不同的值分别赋予foo1、foo2、foo3，但是！！！Integer是个对象，如果它是个可变量，那么i始终指向一个Integer，无论其内部value怎么变化。所以上述问题就是出现，三个foo都指向同一个Integer。只要对其中一个foo进行操作，总会影响到另外两个的值，这点从Object的角度来理解很简单。


### 总结

看了源码，类似Integer的还有其他包装类型，如Long、Float、Double、Boolean、Short、Byte和String等等，哦String不属于包装类型，但它是个不可变类型。

### 参考
- [Why are Java wrapper classes immutable?](https://stackoverflow.com/questions/12370544/why-are-java-wrapper-classes-immutable)
- [Primitive wrapper class](https://en.wikipedia.org/wiki/Primitive_wrapper_class)




