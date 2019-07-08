---
layout: post
#标题配置
title: Redis的扩展信息
#时间配置
date:   2019-07-07 23:49:00 +0800
#大类配置
categories: 数据库



---

* content
{:toc}
---
---

# redis

## redis的应用场景
![](https://itmanmzt.github.io/styles/images/redis/017.jpg){:align="center"}<br><br>

## 通用的redis命令
命令演示：<br>
![](https://itmanmzt.github.io/styles/images/redis/013.jpg){:align="center"}
![](https://itmanmzt.github.io/styles/images/redis/014.jpg){:align="center"}<br><br>
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

## Redis持久化
redis的持久化方式包括RDB(Redis Database)和AOF(Append Only File);持久化的使用方式包括RDB持久化、AOF持久化、无持久化和同时使用RDB和AOF；只做缓存时不需要进行持久化；<br>
### RDB
在指定的时间间隔内将内存中的数据集快照写入磁盘，也就是Snapshot快照，它恢复时是将快照文件直接读到内存里。<br>
在PDB过程中Redis会单独创建（fork）一个子进程来进行持久化，会先将数据写入到一个临时文件中，待持久化过程都接受了，再用这个临时文件替换上次持久化好的文件。整个过程中，主进程是不进行任何IO操作的，这就确保了极高的性能如果需要进行大规模数据的恢复，且对于数据恢复的完整性不是非常敏感，那RDB方式要比AOF方式更加的高效，RDB的缺点是最后一次持久化后的数据可能丢失。<br>
Fork的作用是复制一个与当前进程一样的进程。新进程的所有数据（变量、环境变量、程序计数器等）数值都和原进程一致，但是是一个全新的进程，并作为原进程的子进程。（Fork存在一个问题是对内存的压力，如果原redis进程很大那么再fork一份将给内存很大的压力）。<br>
RDB保存的备份文件为dump.rdb；其保存路径和其他配置如下：<br>
通过redis.conf文件的SNAPSHOTTING快照设置：<br>
1、save seconds changes:设置备份条件，如果多少秒内发生多少个数据变化则进行备份，如果设置为save ""表示关闭备份（一般我们会将备份文件复制一份到另外一台负责备份的机器上(冷拷贝），另外如果redis程序执行flushall或者shutdown命令，会立即进行备份，所以一般发生恶意删除数据的情况要从备份的机器中复制被删除前的备份文件到生产的机器上进行数据的恢复）；<br>
2、dbfilename：指定备份文件的名字；<br>
3、dir：指定备份文件的保存路径；<br>
4、stop-writes-on-bgsave-error:如果后台备份出错了则停止继续进行数据备份工作；<br>
5、rdbcompression:对于存储到磁盘中的快照，可以设置是否进行压缩存储，会通过LZF算法进行压缩，如果你不想消耗CPU来进行压缩的话可以关闭；<br>
6、rdbchecksum:在存储快照后，可以让redis使用CRC64算法进行数据校验，但这样子做会增加大约10%的性能消耗；<br>

在生产环境中如果不希望按着备份条件来希望即可进行备份，可以直接运行save或bgsave命令即可进行备份。save时只管保存，会阻塞其他进程，bgsave时redis会在后台异步进行快照操作；另外可以通过lastsave命令获取最后一次成功执行快照的时间；<br>

如何恢复：将备份文件移动到redis安装目录并启动服务即可，可以通过config get dir获取安装目录；<br>

优势：适合大规模的数据恢复和对数据完整性和一致性要求不高；<br>
劣势：在一定间隔时间做一次备份，所以如果redis意外down掉的话，就会丢失最后一次快照后的所有修改；Fork的时候，内存中的数据被克隆了一份，大致两倍的膨胀性需要考虑内存空间；<br>

### AOF
以日志的形式来记录每个写操作，将redis执行过的所有写指令记录下来（读操作不记录），只许追加文件但不可以改写文件，redis启动之初会读取该文件的重新构建数据；<br>
AOF通过redis.conf的APPEND ONLY MODE设置来开启：<br>
1、appendonly:更改为yes来开启，默认是关闭的；<br>
2、appendfilename:指定aof备份文件的名称；<br>
3、appendfsync:always:同步持久化，每次发生数据变更会被立即记录到磁盘，性能较差但数据完整性比较好；everysec:出厂默认推荐，异步操作，每秒记录，如果一秒内宕机，有数据丢失；no:不自动同步持久化；<br>
重写：当AOF文件大小超过所设定的阈值时，redis就会启动AOF文件的内容压缩，只保留可以恢复数据的最小指令集，可以使用命令bgrewriteaof;<br>
重写原理：AOF文件持续增长而过大时，会fork出一条新进程来将文件重写（也是先写临时文件最后再rename），遍历新进程的内存中数据，每条记录有一条的set语句。重写aof文件的操作，并没有读取旧的aof文件，而是将整个内存中的数据库内容用命令的方式重写一个新的AOF文件；<br>
触发机制：redis会记录上次重写时的AOF大小，默认配置是当AOF文件大小是上次rewrite后大小的一倍且文件大于64M时触发；<br>
4、no-appendfsync-on-rewrite:重写时是否可以运行appendfsync，用默认no即可，保证数据安全性；<br>
5、auto-aof-rewrite-min-size:设置重写的基准值(最小文件大小，单位为M）;<br>
6、auto-aof-rewrite-percentage：设置重写的百分比（本次文件大小比上次文件大小的增加了百分比，单位为%）;<br>

AOF的优势：可以设置高强度的同步机制；<br>
AOF的劣势：相同数据集的数据而言AOF文件要远大于RDB文件，恢复速度慢于RDB；AOF运行效率要慢于RDB，每秒同步策略效率较好，不同步效率和RDB相同;<br>

AOF和RDB同时开启之后redis都会做两者的备份，也就是说这两者是可以共存的，但是当我们重启redis服务之后redis默认会先去加载AOF的备份文件；<br>

当AOF和RDB备份文件被损坏后，可以通过bin目录下的修复程序进行修改，它会把不符合其语法规则的语句删掉；例如：redis-check-aof --fix appendonly.aof<br>

为什么不建议单独使用AOF：因为RDB更适合用于备份数据库（AOF在不断变化不好备份），快速重启，而且不会有AOF可能潜在的bug,留着作为一个以防万一的手段；<br>


## 事务
事务是可以一次执行多个命令，本质是一组命令的集合。一个事务中的所有命令都会序列化，按顺序地串行化执行而不会被其他命令插入，不许加塞；<br>
常用命令：multi:开启事务；exec:提交事务；discard：回滚事务；watch:监控；<br>
![](https://itmanmzt.github.io/styles/images/redis/018.jpg){:align="center"}<br><br>
下面我们将演示执行事务的过程：<br>
![](https://itmanmzt.github.io/styles/images/redis/019.jpg){:align="center"}<br><br>
![](https://itmanmzt.github.io/styles/images/redis/020.jpg){:align="center"}<br><br>
![](https://itmanmzt.github.io/styles/images/redis/021.jpg){:align="center"}<br><br>
![](https://itmanmzt.github.io/styles/images/redis/022.jpg){:align="center"}<		br><br>
对于在事务队列中存在错误的命令，如果这条命令在事务还没提交之前就报错的话，那么整个事务队列的命令都会被取消掉（我们称之为一人做错全体受罚）；但如果这条命令是在事务提交之后才报错的，那么只有这条命令没办法执行，不会影响其他命令的执行（我们称之为冤有头债有主）；所以我们称redis对事务是部分支持的，因为它并没有做到强一致性(原子性），所以不能说完全支持。<br>
redis的watch命令背后的机制类似于乐观锁，也就是说如果在事务还没提交的过程中有其他的线程更改了事务中操作的数据，那么最终会提交失败，需要获取新的数据再进行事务操作；下面我们将进行演示:<br>
![](https://itmanmzt.github.io/styles/images/redis/023.jpg){:align="center"}<br><br>

## 消息订阅发布
进程间的一种消息通信模式：发送者（pub）发送消息，订阅者（sub）接收消息(只需要简单了解，实际开发中不会用它来做消息中间件）；<br>
命令：<br>
![](https://itmanmzt.github.io/styles/images/redis/024.jpg){:align="center"}<br><br>
演示：<br>
![](https://itmanmzt.github.io/styles/images/redis/025.jpg){:align="center"}<br><br>
![](https://itmanmzt.github.io/styles/images/redis/026.jpg){:align="center"}<br><br>

## 主从复制
主从复制(Master/Slave)：主机数据更新后根据配置和策略，自动同步到备机的master/slaver机制，Master以写为主，Slave以读为主；<br>
其主要应用是读写分离和容灾恢复；<br>
主从复制只配从库不配主库，从库的配置通过slaveof 主库IP 主库端口号；这种配置方式只是暂时的，要想永久配置要到redis.conf中进行配置；<br>
主从复制经常使用的模式有四种：一主二仆、薪火相传、反客为主和哨兵模式；<br>
<b>我们先通过一主二仆来了解主从复制：</b><br>
首先我们开启三个redis的服务并登陆其对应端口的客户端，通过info replication命令获取其主从关系状态：<br>
![](https://itmanmzt.github.io/styles/images/redis/027.jpg){:align="center"}<br><br>
然后我们通过slaveof 主库IP 主库端口来设置一主二仆的主从关系：<br>
![](https://itmanmzt.github.io/styles/images/redis/028.jpg){:align="center"}<br><br>
从机跟从主机之前主机写入的数据从机也能拥有:<br>
![](https://itmanmzt.github.io/styles/images/redis/029.jpg){:align="center"}<br><br>
从机不能修改从主机复制过来的数据：<br>
![](https://itmanmzt.github.io/styles/images/redis/030.jpg){:align="center"}<br><br>
从机也不能自己写入主机不存在的数据：<br>
![](https://itmanmzt.github.io/styles/images/redis/031.jpg){:align="center"}<br><br>
主机关机后依旧保持主从关系：<br>
![](https://itmanmzt.github.io/styles/images/redis/032.jpg){:align="center"}<br><br>
当从机shutdown之后，主从关系断裂，重新启动后从机恢复主机身份（除非关系配置到redis.conf的配置文件中）：<br>
![](https://itmanmzt.github.io/styles/images/redis/033.jpg){:align="center"}<br><br>
<b>薪火相传：</b><br>
上一个Slave可以是下一个slave的master，slave同样可以接收其他slaves的连接和同步请求，那么该slave作为链条中下一个的master，可以有效减轻master的写压力；<br>
如果中途变更转向会清除之前的数据，重新建立拷贝最新的;<br>
薪火相传的好处是降低了主机的压力，坏处是可能出现传递延误或者出现错误；<br>
薪火相传依然只有第一个主机有写的权利，其他主从机都只有度的权利；<br>
命令演示：<br>
![](https://itmanmzt.github.io/styles/images/redis/034.jpg){:align="center"}<br><br>
<b>反客为主：</b><br>
通过slaveof no one使当前数据库停止与其他数据库的同步，转成主数据库；<br>
命令演示：<br>
![](https://itmanmzt.github.io/styles/images/redis/035.jpg){:align="center"}<br><br>
<b>哨兵模式：</b><br>
哨兵模式可以说是反客为主的自动版，能够后台监控主机是否故障，如果故障了根据投票数自动将从库转换为主库；<br>
实现方式：在配置文件目录下新建一个sentinel.conf文件，然后填写内容：sentinel monitor 被监控数据库名称 127.0.0.1 6379 1；这个1表示主机挂掉后slave投票看让谁接替成为主机，得票数多者成为主机；然后通过Redis-sentinel sentinel.conf 命令启动哨兵：<br>
![](https://itmanmzt.github.io/styles/images/redis/038.jpg){:align="center"}<br><br>
主机shutdown,观看哨兵如何工作(它们会投票选出新的主机）：<br>
![](https://itmanmzt.github.io/styles/images/redis/037.jpg){:align="center"}<br><br>
![](https://itmanmzt.github.io/styles/images/redis/036.jpg){:align="center"}<br><br>
shutdown的主机重启后会变成新主机的从机：<br>
![](https://itmanmzt.github.io/styles/images/redis/039.jpg){:align="center"}<br><br>

### 复制的原理
slave启动成功连接到master后会发送一个sync命令；<br>
master接到命令启动后台的存盘进程，同时收集所有接收到的用于修改数据集命令，在后台进程执行完毕后，master将传送整个数据文件到slave，以完成一次完成同步；<br>
全量复制：而slave服务在接收到数据库文件数据后，将其存盘并加载到内存中；<br>
增量复制：master继续将新的所有收集到的修改命令依次传给slave，完成同步；<br>
但是只要是重新连接master，一次完全同步(全量复制)将被自动执行；

### 主从复制的局限性
复制延时：由于所有的写操作都是先在master上操作，然后同步更新到slave上，所以从master同步到slave机器有一定的延迟，当系统很繁忙的时候，延迟问题会更加严重，slave机器数量的增加也会使这个问题更加严重；






  <br>

<br>

<center>有Marin的地方就有你的收获</center>