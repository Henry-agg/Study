# Redis



## 百度百科

Redis（Remote Dicitionary Server 远程字典服务） 是一个开源的使用 ANSI C 语言编写、支持网络、可基于内存、可持久化的日志型、Key-Value 的数据库

Redis 是一个 Key-Value 存储系统，支持存储的 value 类型很多：String（字符串）、List（列表）、Set（集合）、Zset（有序集合）和 Hash（哈希类型）

Redis 支持主从同步，在redis主从模式中，主是可以读写，但是从是只读的



## yum安装

```mysql
yum -y install epel-release
yum -y install redis
```

启动服务：

```shell
service redis start
```

报错：

```mysql
[root@Fp-02 ~]# service redis start
Starting redis-server: 
*** FATAL CONFIG FILE ERROR ***
Reading the configuration file, at line 163
>>> 'logfile /var/log/redis/redis.log'
Can't open the log file: Permission denied
                                                           [FAILED]
```

解决：

```mysql
chmod 777 /var/log/redis/redis.log
```



## 源码安装

上传源码包至服务器

解压、进入源码目录

由于makefile文件已经写好，我们只需要直接在源码目录执行make命令进行编译

```mysql
make && make install
```

会在当前目录下生成本个可执行文件，分别是redis-server、redis-cli、redis-benchmark、redis-stat，它们的作用如下：

redis-server：Redis服务器的daemon启动程序

redis-cli：Redis命令行操作工具。当然，你也可以用telnet根据其纯文本协议来操作

redis-benchmark：Redis性能测试工具，测试Redis在你的系统及你的配置下的读写性能

redis-stat：Redis状态检测工具，可以检测Redis当前状态参数及延迟状况。

创建 redis 目录，同意管理 redis，配合脚本启动

```mysql
mkdir redis
```

配置参数

| 参数           | 意义                                                         |
| -------------- | ------------------------------------------------------------ |
| daemonize      | 是否以后台daemon方式运行                                     |
| pidfile        | pid文件位置                                                  |
| port           | 监听的端口号                                                 |
| bind           | 监听地址                                                     |
| timeout        | 请求超时时间                                                 |
| loglevel       | log信息级别                                                  |
| logfile        | log文件位置                                                  |
| databases      | 开启数据库的数量                                             |
| save * *       | 保存快照的频率，第一个*表示多长时间，第二个*表示执行多少次写操作。在一定时间内执行一定数量的写操作时，自动保存快照。可设置多个条件。 |
| rdbcompression | 是否使用压缩                                                 |
| dbfilename     | 数据快照文件名（只是文件名，不包括目录）                     |
| dir            | 数据快照的保存目录（这个是目录                               |
| appendonly     | 是否开启appendonlylog，开启的话每次写操作会记一条log，这会提高数据抗风险能力，但影响效率。 |
| appendfsync    | appendonlylog如何同步到磁盘（三个选项，分别是每次写都强制调用fsync、每秒启用一次fsync、不调用fsync等待系统自己同步） |



## 启动

yum 安装：service redis start

源码 安装：redis 的启动程序 和 脚本启动

​	启动程序：

```mysql
redis-server /etc/redis/6379.conf
```

​	脚本启动：

```mysql
cd /root/redis-5.0.6/utils
cp redis_init_script /etc/rc.d/init.d/redis
service redis start
```

​	只限启动和停止，没有重启



## 主从

开启第二台服务器，安装 redis

修改配置文件

```mysql
# replicaof <masterip> <masterport>
replicaof 10.0.0.11 6379
```

重启验证

进入主 redis 查看状态 info

```mysql
127.0.0.1:6379> info
# Replication
role:master	# 身份 主
connected_slaves:1	# 连接的从
slave0:ip=10.0.0.11,port=6379,state=online,offset=1,lag=1	# 从1
master_repl_offset:1
repl_backlog_active:1
repl_backlog_size:1048576
repl_backlog_first_byte_offset:2
repl_backlog_histlen:0
```

从的状态

```mysql
127.0.0.1:6379> info
# Replication
role:slave	# 身份 从
master_host:10.0.0.12	# 主的IP
master_port:6379	# 主的端口
master_link_status:up
master_last_io_seconds_ago:10
```



## 持久化

为什么要做持久存储

我们大家都知道Redis是一个把数据存储在内存中的nosql数据库，内存保存数据是很容易丢失的，比如服务器由于一些特殊原因导致关机后，那么内存中的所有数据都会丢失，所以我们需要将保存在内存中的数据库写入到磁盘中，这样就可以实现数据的持久存储了，就算服务器关机数据以然保存于硬盘当中



Redis实现数据持久化的方式： aof 和rdb

AOF持久存储

AOF实现的方式，是以日志的形式把所有执行过的指令给保存下来，有点类似MySQL的二进制日志一样！那么再恢复数据库的时候，就是将所有的指令再执行一遍，以此来实现数据库的持久化的

AOF的优点：

- 可以保持很高的的数据完整性，如果设置追加file的时间是1s，如果redis发生故障，最多会丢失1s的数据；
- 如果日志写入不完整支持redis-check-aof来进行日志修复
- 可读性强.由于aof是将命令写入文件中，我们可以直接查看命令内容，同时也可以修改日志文件内容

AOF的缺点：

- AOF文件比RDB文件大，且恢复速度慢

  

Rdb持久存储

RDB持久化存储即是将redis存在内存中的数据以快照的形式保存在本地磁盘中。将某个时间的内存数据存储在一个rdb文件中。在redis服务重新启动的时候会加载rdb文件中的数据。

Rdb的优点：

- RDB的性能很好，需要进行持久化时，主进程会fork一个子进程出来，然后把持久化的工作交给子进程，自己不会有相关的I/O操作
- 在数据量比较大的情况下，rdb的恢复速度要快于AOF

rdb的缺点：

- rbd比较容易造成数据丢失，假设每5分钟保存一次快照，那么如果由于某些原因导致redis无法正常工作，那么从上次保存快照到redis出现故障的这段时间的数据就会丢失！

  

AOF持久化的实现

配置 redis.cnf

```shell
启用AOF：appendonly yes  
指定AOF保存文件名字: appendfilename "appendonly.aof"
```

其他相关：

\#一旦插入命令，立即同步到磁盘，保证了完全的持久化，但是速度慢，不推荐

appendfsync always

\#AOF每秒进行同步

```mysql
appendfsync everysec
```

\#不自动同步，性能最好，但是持久化没有保证

```mysql
appendfsync no
```

随着日志内容的递增，AOF文件会越来越大，为了解决这种问题，我们可以对AOF文件进行重写，执行如下操作：

```shell
redis-cli -h ip -p port bgrewriteaof
```

执行的过程：在当前的快照保存工作结束后，开启一个子进程，将AOF文件进行重写，合并set命令等操作到一个临时文件，达到缩小文件大小的目的。重写结束后后将临时文件替换为新的AOF文件（重写过程中如果有新的redis操作命令，会提交到缓存中，重写结束后追加到AOF文件内）

在redis2.4以上的版本后，重写机制可以自动触发

当目前的AOF文件大小超过上一次重写文件大小的百分之几时进行重写，如果没有重启过，则以启动时的AOF文件大小为依据

```
auto-aof-rewrite-percentage 100
```

允许重写的最小AOF文件大小

```
auto-aof-rewrite-min-size 64mb
```



RDB持久化的实现

在redis.conf中默认是开启的

保存的文件名：dbfilename dump.rdb

保存的目录dir ./

手动备份: 登录到redis服务器，执行save即可

自动备份

```
save m n
```

修改配置项 save m n即表示在 m 秒内执行了 n 次写的命令则进行备份（并且关系）



## 哨兵

基于遗嘱双从实现哨兵，并且在每个 redis 上都部署哨兵

给哨兵生成配置文件

```mysql
cd /root/redis-5.0.6/utils
cp sentinel.conf /etc/redis/
vim /etc/redis/sentinel.conf
```

所有哨兵修改配置文件

```mysql
bind 0.0.0.0
protected-mode no
port 26379
sendinel monitor mymaster 10.0.0.11(哨兵主) 6379 2(票数)
```

重启哨兵

```mysql
redis-sendinel /etc/redis/sendinel.conf
```

测试，前端启动

























