---
title: oracle查询条件中不等于碰上NULL会发生什么
layout: post
category: oracle
splitor: <!--more-->
---

有时表的一些字段是可选的，并不是必须有值的，这时候没有赋值的字段值便是NULL。这时候，我们使用了 ```coluName != 某个值``` 查询条件时，你会惊奇的发现，这列为NULL的记录被过滤掉了，没有被查询出来，结果和我们预期的不一样。

[官方文档](http://docs.oracle.com/cd/E11882_01/server.112/e26088/functions091.htm)上的说明是，NULL是未知或无意义。NULL 只能使用is 或者is not 来比较。当它遇上 ```=``` 或者 ```！=```时，结果是 UNKNOWN. UNKNOWN 虽然不等于 FALSE，对于被查询的记录来说，UNKNOWN的记录也是不会返回的，在这点上有点像FALSE。

类似的，在使用```not in （子查询）```的时候，也要注意，如果子查询的结果中有NULL记录，该查询条件返回空结果集。

**如何解决这个问题**

 * 声明列的默认值，避免出现值为null的情况
 * 使用LNNVL

    LNNVL函数用于某个语句的where子句中的条件，如果条件为true就返回false；如果条件为UNKNOWN或者false就返回true.这个函数的处理逻辑真是绕。举个例子 如果 我要查询a不等于10且包括值为NULL记录的时候，条件需要这样写```LNNVL(a = 10)```