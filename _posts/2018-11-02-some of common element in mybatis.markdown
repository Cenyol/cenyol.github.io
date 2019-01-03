---
layout: post
title: MyBatis常用的一些XML元素
date: 2018-11-02 20:12:39.000000000 +08:00
---


### Driver中的Url配置

如果需要update操作返回的受影响的行数，那么需要在url后面加个useAffectedRows=true参数，如：

```
jdbc:mysql://${db.host}:${db.port}/${db.database}?useUnicode=true&amp;characterEncoding=UTF-8&amp;allowMultiQueries=true&amp;useAffectedRows=true
```

不然默认情况下，update返回表示的是匹配的行数，不管有没有记录发生变化。

### 传入到dao里面不过是object还是map都可以直接使用其中的字段名或者key来引用

```
List<GoodsOperationRecordPO> getByIds(SearchHistoryParams params);
```

```
<select id="getByIds" parameterType="list" resultType="GoodsOperationRecordPO">
    SELECT * FROM tb_goods_operation WHERE id in
    <foreach item="item" index="index" collection="opIds" open="(" separator="," close=")">
        #{item}
    </foreach>
    ORDER BY id DESC LIMIT #{pageSize} OFFSET #{offset}
</select>
```

上述#{}中和foreach的collection中都是直接使用了object的字段名来引用的，不需要写明其实例名，如#{params.pageSize}等价于#{pageSize}，map类似。

### 动态XML SQL语法

<choose> <when> <otherwise> 这三个标签的作用同Java里面的switch语句，等价于switch case default，用于多个取其中一个条件的情况下。

<where> <if>的作用是用于多个可选的情况下，可全要、可部分也可都不要。这个组合比上述switch的组合有个优点就是，它可以自动去除and关键字。比如：

```
&lt;select id="searchIds" resultType="long"&gt;
    SELECT id from tb_goods_operation
    <where>
        <if test="groupId != null and groupId > 0">
            AND groupId = #{groupId}
        </if>
        <if test="operatorName != null and operatorName != ''">
            AND operatorName = #{operatorName}
        </if>
        <if test="startTime != null and startTime > 0">
            AND createTime >= #{startTime}
        </if>
        <if test="endTime != null and endTime > 0">
            <![CDATA[ AND createTime < #{endTime} ]]>
        </if>
    </where>
&lt;/select>
```


虽然第一个if里面有个and，当所有的if都成立时，where标签会自动去除第一个条件前面的and，因为它懂得，而switch组合就不懂了。它往往需要在前面加个没有and的查询条件，比如下：


```
echo 'hello'
```


```
&lt;select id="findActiveBlogLike" resultType="Blog"&gt;
  SELECT * FROM BLOG WHERE state = 'ACTIVE'
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
&lt;/select>
```


### #{}和${}的区别
引用Stack Overflow上的一个回答

> By default, using the #{} syntax will cause MyBatis to generate PreparedStatement properties and set the values safely against the PreparedStatement parameters (e.g. ?). While this is safer, faster and almost always preferred, sometimes you just want to directly inject a string unmodified into the SQL Statement. For example, for ORDER BY, you might use something like this:

> ORDER BY ${columnName}

> Here MyBatis won't modify or escape the string.

> NOTE It's not safe to accept input from a user and supply it to a statement unmodified in this way. This leads to potential SQL Injection attacks and therefore you should either disallow user input in these fields, or always perform your own escapes and checks.

通俗的讲就是#{}等价于Java中的PreparedStatement所产生的效果，它具有更快、更安全的效果，但是有时候，可能需要直接插入，不经过任何通配符或者预处理。就可以直接使用${}，这种方式存在被注入的风险。

### 参考
- [Dynamic SQL](http://www.mybatis.org/mybatis-3/dynamic-sql.html)
- [mybatis 多参数 list和String](https://blog.csdn.net/u010913106/article/details/50538379)
- [When to use $ vs #?](https://stackoverflow.com/questions/39954300/when-to-use-vs)


