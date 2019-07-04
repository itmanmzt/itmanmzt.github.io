---
layout: post
#标题配置
title: Redis的数据结构
#时间配置
date:   2019-07-04 16:24:00 +0800
#大类配置
categories: 数据库



---

* content
{:toc}
---
---

# data structure
Redis作为一种高性能键值对数据库，支持的键值数据类型有：字符串类型、散列类型、列表类型、集合类型、有序集合类型，而键（key）全部都是字符串，在key值得命名上不要过长，否则影响使用效率。下面我们将对这些类型一一进行演示

## 字符串类型
字符串在Redis中是二进制安全的（二进制安全和数据安全没有关系，二进制安全问题是因为频繁的编解码可能因为码表不同导致乱码引起的，MySQL就是二进制不安全的），在Redis中字符串类型的value最多可以容纳数据长度是512M。String的使用环境主要是用于保存json格式的字符串<br>
存储String的常用命令有赋值、取值、删除、数值增减和一些扩展命令。
命令演示：
![](https://itmanmzt.github.io/styles/images/redis/001.jpg){:align="center"}<br><br>
命令介绍：<br>
1、set命令是用户存储一个键值对信息，相当于map的put方法；<br>
2、get命令是用户获取key对应的value，相当于map的get方法；<br>
3、getset命令是一个扩展命令，是一个修改命令，获取已经存在的键值对信息并修改对应的value；<br>
4、del命令是通过key删除已存在的键值对信息，相当于map的remove方法；<br>
5、incr/decr命令是自增/自减命令，会获取key对应的value执行自增/自减操作并保存，如果对应的key不存在，则会新建对应的键值对并将value默认赋值为0，然后再执行自增/自减操作，如果对应的value不能进行数值计算，则会返回error<br>
6、incrby/decrby命令是一个扩展命令，是在incr/decr的基础上进行操作，后面会增加一个参数用于指定要增加/减少的数值大小；<br>
7、append命令是一个扩展命令，和Java中StringBuffer的append的作用一样，都是用于拼接字符串的；<br>

## 散列类型
Redis中的散列类型可以看成是具有String类型的key和String类型的value的map容器，每个Hash最多可以存储4294967295个键值对，Hash的特点是占用的磁盘空间非常少。<br>
存储Hash常用命令有赋值、取值、删除、增加数字和自学命令。<br>
命令演示：
![](https://itmanmzt.github.io/styles/images/redis/002.jpg){:align="center"}
![](https://itmanmzt.github.io/styles/images/redis/003.jpg){:align="center"}
![](https://itmanmzt.github.io/styles/images/redis/004.jpg){:align="center"}<br><br>
命令介绍：<br>
1、hset命令是为key设置一对键值对value；<br>
2、hmset命令是为key设置多对键值对value;<br>
3、hget命令是通过key和value键值对的key来获取value键值对的value；<br>
4、hmget命令是通过key和多个value键值对的key来获取它们对应value键值对的value；<br>
5、hgetall命令是通过key来获取所有value键值对的key和value；<br>
6、hdel命令是删除一个或多个键值对；<br>
7、del命令是直接删除整个hash；<br>
8、hincrby命令和String类型的hincrby作用一样；<br>
9、hexists命令是判断指定的键值对是否存在；<br>
10、hlen命令是获取hash中存在的键值对数量；<br>
11、hkeys命令是获取hash中所有的键值对的key；<br>
12、hvals命令是获取hash中所有的键值对的value；<br>
## 列表类型
Redis的列表类型选取的是链表的数据结构，因为在redis操作中最多的操作是进行元素的增删。其使用环境包括做大数据集合的增删和任务队列。<br>
存储list常用的命令包括两端添加、查看列表、两端弹出、获取列表元素个数和扩展命令。<br>
命令演示：
![](https://itmanmzt.github.io/styles/images/redis/005.jpg){:align="center"}
![](https://itmanmzt.github.io/styles/images/redis/006.jpg){:align="center"}
![](https://itmanmzt.github.io/styles/images/redis/007.jpg){:align="center"}
![](https://itmanmzt.github.io/styles/images/redis/008.jpg){:align="center"}<br><br>
命令介绍：<br>
1、lpush/rpush命令是在链表的左端/右端按顺序压入数据；<br>
2、lrange命令是查看列表中的数据，列表左端的下标从0开始，最右端的下标为-1，通过0-5可以查看左端前六个元素，通过(-6)-(-1)可以查看最右端的六个元素，通过0-(-1)可以查看列表的全部元素；<br>
3、lpop/rpop命令是从列表的左端/右端弹出一个元素；<br>
4、lrem命令是从列表中删除元素，lrem list n a表示从左端删除列表中n个a元素，lrem list -n a表示从右端删除列表中n个a元素，lrem list 0 a表示删除列表中所有的a元素；<br>
5、lset命令通过指定下标和元素，把指定下表的元素替换为目标元素；<br>
6、linsert命令通过linsert list before/after a b表示在a元素的前面/后面插入b元素；<br>
7、rpoplpush命令是弹出一个列表最右端的元素并将其插入到另一个列表的最左端,可以实现两队队列或循环队列；<br>
8、llen命令是返回列表的元素个数；
## 集合类型
Redis的集合类型和Java中的HashSet一样具有无序不重复的特点。<br>
存储set常用命令有添加/删除元素、获取集合中的元素、集合中的差集运算、集合中的交集运算、集合中的并集运算和扩展命令。<br>
命令演示：
![](https://itmanmzt.github.io/styles/images/redis/009.jpg){:align="center"}
![](https://itmanmzt.github.io/styles/images/redis/010.jpg){:align="center"}<br><br>
命令介绍：<br>
1、sadd命令用于添加元素,相当于set的add方法，当出现存储相同元素的时候只会保留一个；<br>
2、srem命令用于删除元素，相当于set的remove方法；<br>
3、smembers命令用于获取集合中的元素，相当于set的toArray方法；<br>
4、sismembers命令用于判断某个元素是否存在，相当于set的contains方法；<br>
5、sdiff命令用于差集运算，计算第一个集合相对第二个集合的差集；<br>
6、sinter命令用于交集运算；<br>
7、sunion命令用于并集运算；<br>
8、sdiffstore/sinterstore/sunionstore命令用于存储运算结果；<br>
9、scard命令用于返回集合中元素的个数，相当于set的size方法；<br>
10、srandmember命令用于返回集合中的一个随机数；
## 有序集合类型
有序集合类型相对于集合类型的区别在于其成员在集合中的位置是有序的，其使用场景可以是创建排行榜。<br>
存储Sorted-set常用命令有添加元素、获得元素、删除元素、范围查询和扩展命令。<br>
命令演示：
![](https://itmanmzt.github.io/styles/images/redis/011.jpg){:align="center"}
![](https://itmanmzt.github.io/styles/images/redis/012.jpg){:align="center"}<br><br>
命令介绍：<br>
1、zadd命令用于添加元素，同时为该元素添加分数；<br>
2、zscore命令用于获取元素对应的分数;<br>
3、zrange/zrevrange命令用于获取集合中元素的正序/逆序排列，可以添加withscores关键字同时获取元素的分数；<br>
4、zcard命令用于获取集合中元素的个数；<br>
5、zrem命令用于删除集合中的一个或多个元素，zremrangebyrank可以删除指定排名范围的元素，zremrangebyscore可以删除指定分数范围的元素；<br>
6、zrangebyscore命令可以获取指定分数范围的元素；<br>
7、zincrby命令可以给指定的元素增加分数；<br>
8、zcount命令可以返回指定分数范围内元素的个数；


  <br>

<br>

<center>有Marin的地方就有你的收获</center>

