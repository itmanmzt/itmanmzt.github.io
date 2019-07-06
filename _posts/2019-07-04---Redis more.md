---
layout: post
#标题配置
title: Redis的扩展信息
#时间配置
date:   2019-07-04 00:26:00 +0800
#大类配置
categories: 数据库



---

* content
{:toc}
---
---
# redis

## 通用的redis命令
命令演示：<br>
![](https://itmanmzt.github.io/styles/images/redis/011.jpg){:align="center"}
![](https://itmanmzt.github.io/styles/images/redis/012.jpg){:align="center"}<br><br>
命令介绍：<br>
1、keys *命令可以获取数据库的所有键值对的key值；<br>
2、keys my?命令可以获取以my开头并且还含有另外一个字符的键值对的key，一个？代表一个任意字符；<br>
3、del命令可以删除数据库中的一个或多个键值对；<br>
4、exists命令可以知道指定的键值对是否存在于数据库中；<br>
5、rename命令可以修改键值对的key；<br>
6、type命令可以知道对应键值对的数据类型；<br>
7、expire命令可以给一个键值对设置过期时间，以秒为单位；<br>
8、ttl可以返回键值对的过期状态，-1为未设置过期时限，-2为以过期，正数表示还有多少秒过期；<br>
9、select命令用于选择操作哪个数据库，从0开始，默认有16个数据库；<br>
10、move命令可以把指定的键值对移到指定的数据库中；



  <br>

<br>

<center>有Marin的地方就有你的收获</center>