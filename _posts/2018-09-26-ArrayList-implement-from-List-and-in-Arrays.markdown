---
layout: post
title: new ArrayList与Arrays.asList
date: 2018-09-26 15:32:24.000000000 +08:00
---
### 问题

遇到过这个问题：

```
    @Test
    public void testArrayList() {
        List<Integer> list1 = new ArrayList<>();
        list1.add(1);
        list1.add(2);
        list1.add(3);
        log.info("\n{}", list1);
        
        list1 = Arrays.asList(1, 2, 3, 4, 5);
        list1.add(6);                            // throw a UnsupportedOperationException
        log.info("\n{}", list1);
    }
```

也就是说，Arrays.asList返回的那个list是不能进行Add操作的，谷歌了下，还发现不能进行remove操作，只能是get、set这两个操作。You can only read or overwrite the elements.

还有类似的情况，代码片段如下：

```
    List<Integer> list1 = new ArrayList<Integer>(Arrays.asList(ia));  //copy
    List<Integer> list2 = Arrays.asList(ia);
```

其中ia是个整型数组。类似前面的说法，在这里list1可以进行增删改查，而list2只能进行改查，不能增删。为什么呢？简单看了下asList的源码，发现它返回的就是个ArrayList对象呀，为什么不能进行增删操作。源码片段如下：

```
    @SafeVarargs
    @SuppressWarnings("varargs")
    public static <T> List<T> asList(T... a) {
        return new ArrayList<>(a);
    }
```

所以，问题就是，为什么返回的都是ArrayList，但是，new ArrayList得到的对象可以进行增删改查，而通过Arrays.asList却只能进行改查呢。


### 解决

直接引用Stack Overflow上的一个回答：

> First, let's see what this does:

> Arrays.asList(ia)
It takes an array ia and creates a wrapper that implements List<Integer>, which makes the original array available as a list. Nothing is copied and all, only a single wrapper object is created. Operations on the list wrapper are propagated to the original array. This means that if you shuffle the list wrapper, the original array is shuffled as well, if you overwrite an element, it gets overwritten in the original array, etc. Of course, some List operations aren't allowed on the wrapper, like adding or removing elements from the list, you can only read or overwrite the elements.

> Note that the list wrapper doesn't extend ArrayList - it's a different kind of object. ArrayLists have their own, internal array, in which they store their elements, and are able to resize the internal arrays etc. The wrapper doesn't have its own internal array, it only propagates operations to the array given to it.

> On the other hand, if you subsequently create a new array as

> new ArrayList<Integer>(Arrays.asList(ia))
then you create new ArrayList, which is a full, independent copy of the original one. Although here you create the wrapper using Arrays.asList as well, it is used only during the construction of the new ArrayList and is garbage-collected afterwards. The structure of this new ArrayList is completely independent of the original array. It contains the same elements (both the original array and this new ArrayList reference the same integers in memory), but it creates a new, internal array, that holds the references. So when you shuffle it, add, remove elements etc., the original array is unchanged.

简单翻译一下就是：
> Arrays.asList返回的只是个马甲对象，这个马甲披在源数组外面，加上了和list接口类似的get、set、size等方法。但是由于数组的大小是固定不变的原因，并且这个马甲所做的一些操作都是直接传递到源数组的，所以理论上是不能对马甲进行增删的操作。嗯，这个原因好理解。

> 而new ArrayList返回的就是个切切实实的List，是可以进行增删改查的，这就是他们的区别，不难理解。

但是！

还有个问题就是，在Arrays.asList的源码里面，看到了它也是返回了ArrayList，为何它却是个不能增删呢。

后来在同一个Stack Overflow回答的更下面看到另一条记录，他说：

> Well this is because ArrayList resulting from Arrays.asList() is not of the type java.util.ArrayList . Arrays.asList() creates an ArrayList of type java.util.Arrays$ArrayList which does not extend java.util.ArrayList but only extends java.util.AbstractList

意思就是说，我们在Arrays.asList里面看到的那个ArrayList和new ArrayList不是同一个类。赶紧继续深入看一下源码，果然，Arrays.asList里面的那个ArrayList是个内部类，属于Arrays这个包内的。并且，从它的类实现代码看来，它也只提供了这几个方法：

![](http://orstu229n.bkt.clouddn.com/java.util.Arrays$ArrayList.png)

和我们平时说的new ArrayList不一样，它长这样：

![](http://orstu229n.bkt.clouddn.com/java.util.ArrayList.png)

前者名字叫做：java.util.Arrays$ArrayList，后者名字叫做：java.util.ArrayList，是个正经的List。

到这里也就知道了为什么了。java.util.Arrays$ArrayList这个内部类就是我们说的那个马甲，之所以它能够进行如下的赋值操作：

```
    List<Integer> list2 = Arrays.asList(ia);
```

是因为Arrays.asList返回的java.util.Arrays$ArrayList类继承了java.util.AbstractList，而后者也实现了List这个接口，因而在自动upcast的时候不会发生错误。

### 总结

看源码的时候，应该要更加深入，或许就有解了。

### 参考
- ![Difference between Arrays.asList(array) and new ArrayList<Integer>(Arrays.asList(array))](https://stackoverflow.com/questions/16748030/difference-between-arrays-aslistarray-and-new-arraylistintegerarrays-aslist)
- ![维基百科上对Wrapper模式的介绍](https://en.wikipedia.org/wiki/Adapter_pattern)