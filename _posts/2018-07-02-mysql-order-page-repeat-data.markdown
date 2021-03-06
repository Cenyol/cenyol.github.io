---
layout: post
title: MySQL查询时根据Order排序翻页后出现的数据重复问题
date: 2018-07-02 19:05:16.000000000 +08:00
---

### 问题

昨天提出一个问题，说某个接口提供的列表数据在第一页和第四页出现了一项相同的数据，id=81。这接口不是之前我开发的，毕竟才刚入职一个月。

在mysql中我们通常会采用limit来进行翻页查询，比如limit(0,10)表示列出第一页的10条数据，limit(10,10)表示列出第二页。但是，当limit遇到order by的时候，可能会出现翻到第二页的时候，竟然又出现了第一页的记录。

具体如下：
```
SELECT `id`,`title`,`createTime` FROM post ORDER BY createTime desc LIMIT 10,0;
```
使用上述SQL查询的时候，很有可能出现和LIMIT 10,10相同的某条记录。

### 解决

问了下谷歌，给了这篇文章，是淘宝数据库团队的答疑解惑，之前有人也遇到过相同问题，是在阿里云的RDS上面遇到的，大言不惭的断定是RDS的Bug，23333.

上述问题的原因是，在MySQL5.6之后，数据库对order by limit语句做了优化，即使用了priority queue。引用：

>使用 priority queue 的目的，就是在不能使用索引有序性的时候，如果要排序，并且使用了limit n，那么只需要在排序的过程中，保留n条记录即可，这样虽然不能解决所有记录都需要排序的开销，但是只需要 sort buffer 少量的内存就可以完成排序。
>
>之所以5.6出现了第二页数据重复的问题，是因为 priority queue 使用了堆排序的排序方法，而**堆排序**是一个不稳定的排序方法，也就是相同的值可能排序出来的结果和读出来的数据顺序不一致。
>
>5.5 没有这个优化，所以也就不会出现这个问题。

解决办法如下：

1. **索引排序字段** 之前的月报中，我们讨论过三星索引的设计，其中第二条就是利用索引的有序性，如果用户在字段添加上索引，就直接按照索引的有序性进行读取并分页，从而可以规避遇到的这个问题。

2. **正确理解分页** 还是要正确理解分页，分页是建立在排序的基础上，进行了数量范围分割。排序是数据库提供的功能，而分页却是衍生的出来的应用需求。在MySQL和Oracle的官方文档中提供了limit n和rownum < n的方法，但却没有明确的定义分页这个概念。还有重要的一点，虽然上面的解决方法可以缓解用户的这个问题，但按照用户的理解，依然还有问题：比如，这个表插入比较频繁，用户查询的时候，在read-committed的隔离级别下，第一页和第二页仍然会有重合。

分页一直都有这个问题，我们看分页常用的场景：1)早期的论坛 2)个人交易记录。这些场景都对数据分页都没有非常高的准确性要求。

### 总结

1. **不加order by的时候的排序问题** 用户在使用Oracle或MySQL的时候，发现MySQL总是有序的，Oracle却很混乱，这个主要是因为Oracle是堆表，MySQL是索引聚簇表的原因。所以没有order by的时候，数据库并不保证记录返回的顺序性，并且不保证每次返回都一致的。

2. **分页问题** 分页重复的问题，就如前面所描述的，分页是在数据库提供的排序功能的基础上，衍生出来的应用需求，数据库并不保证分页的重复问题。

3. **NULL值和空串问题** 不同的数据库对于NULL值和空串的理解和处理是不一样的，比如Oracle NULL和NULL值是无法比较的，既不是相等也不是不相等，是未知的。而对于空串，在插入的时候，MySQL是一个字符串长度为0的空串，而Oracle则直接进行NULL值处理。