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

## Redis基础知识讲解
1、Redis是单进程的，对Linux内核的大批量文件处理描述符EPOLL进行了包装，实现对IO的复用，从而提高处理速度；<br>
2、Dbsize是返回redis中key的个数；Flushdb是清空当前库；Flushall是通杀全部库；。

## Redis配置文件介绍
1、Units内存大小单位：1kb=1024bytes ；1k=1000bytes;<br>
2、include包含：redis配置文件中可以引用其他的配置文件；<br>
通用命令：<br>
3、bind：进行端口的绑定；<br>
4、timeout:如果超过多少秒客户端没有响应则关闭与其连接，设置为0则表示关闭超时；<br>
5、tcp-keepalive：每过多少秒就测试客户端是否还处于连接状态，建议设置成60；<br>
6、loglevel:日志级别（debug,verbose,notice,warning),从上往下信息越来越少；<br>
7、logfile:指定日志名称和目录；<br>
8、syslog-enabled:是否开启日志;<br>
9、tcp-backlog:连接队列<br>
![](https://itmanmzt.github.io/styles/images/redis/015.jpg){:align="center"}<br><br>
安全命令：访问密码的查看、设置和取消；在Linux命令下可以通过config get requirepass获取密码，通过config set requirepass "*****"来设置密码，在设置完密码之后所有对redis的访问都会被拦截，必须在访问前输入密码指令auth “*****”，后续的命令才能被放行；<br>
限制设置：<br>
10、maxclients:最大连接客户端数量；<br>
11、maxmemory:最大缓存内存大小；<br>
12、maxmemory-policy:缓存清楚策略，当达到最大缓存时；<br>
![](https://itmanmzt.github.io/styles/images/redis/016.jpg){:align="center"}<br><br>
13、maxmemory-samples:设置样本数量，LRU算法和最小TTL算法都并非是精确的算法，而是估算值，所以可以设置样本的大小，redis默认会检查这么多个key并选择其中LRU的那个。默认设置为5；<br>







  <br>

<br>

<center>有Marin的地方就有你的收获</center>