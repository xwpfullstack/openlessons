[TOC]
# Redis高性能缓存服务器
## Redis简介

Redis是一个开源的key-value存储系统。与Memcached类似，Redis将大部分数据存储在内存中，支持的数据类型包括：字符串、哈希表、链表、集合、有序集合以及基于这些数据类型的相关操作。Redis使用C语言开发，在大多数像Linux、BSD和Solaris等POSIX系统上无需任何外部依赖就可以使用。Redis支持的客户端语言也非常丰富，常用的计算机语言如C、C#、C++、Object-C、PHP、Python、Java、Perl、Lua、Erlang等均有可用的客户端来访问Redis服务器。当前Redis的应用已经非常广泛，国内像新浪、淘宝，国外像Flickr、Github等均在使用Redis的缓存服务。

一句话简介：
	Redis基于内存操作，读写速度很快，100000 读写/秒,可以作为内存型缓存服务器,提供持久化存储方案,可以作为结构不复杂的数据库使用。

## 为什么要缓存

5400转的笔记本硬盘：50-90MB/s

7200转的台式机硬盘：90-190MB/s

固态硬盘读写速度可以达到500MB/s

内存DDRIII1333的速度读写速度大概在8G/s

其他频率的条子速度根据大小会有很大的浮动

## Redis使用场景

1.取最新N个数据的操作

	比如典型的取你网站的最新文章，我们可以将最新的5000条评论的ID放在Redis的List集合中，并将超出集合部分从数据库获取。

2.排行榜应用，取TOP N操作

	这个需求与上面需求的不同之处在于，前面操作以时间为权重，这个是以某个条件为权重，比如按顶的次数排序，
	这时候就需要我们的sorted set出马了，将你要排序的值设置成sorted set的score，
	将具体的数据设置成相应的value，每次只需要执行一条ZADD命令即可。

3.需要精准设定过期时间的应用

	　　比如你可以把上面说到的sorted set的score值设置成过期时间的时间戳，那么就可以简单地通过过期时间排序，
	定时清除过期数据了，不仅是清除Redis中的过期数据，你完全可以把Redis里这个过期时间当成是对数据库中数据的索引，
	用Redis来找出哪些数据需要过期删除，然后再精准地从数据库中删除相应的记录。

4.计数器应用

	　　Redis的命令都是原子性的，你可以轻松地利用INCR，DECR命令来构建计数器系统。

5.uniq操作，获取某段时间所有数据排重值

	　　这个使用Redis的set数据结构最合适了，只需要不断地将数据往set中扔就行了，set意为集合，所以会自动排重。

6.Pub/Sub构建实时消息系统

	　　Redis的Pub/Sub系统可以构建实时的消息系统，比如很多用Pub/Sub构建的实时聊天系统的例子。

7.构建队列系统

	　　使用list可以构建队列系统，使用sorted set甚至可以构建有优先级的队列系统。

8.缓存

	　　最常用，性能优于Memcached(被libevent拖慢)，数据结构更多样化。

应用举例：

当Redis只是当做cache和MySQL同步使用时:

+ 读:  读redis->没有，读mysql->把mysql数据写回redis
+ 写:  写mysql->成功，写redis


## Linux下安装Redis

到Redis官网下载稳定版3.0.6

	http://redis.io/download

或者直接终端下输入:

	wget http://download.redis.io/releases/redis-3.0.6.tar.gz
	tar zxvf redis-3.0.6.tar.gz
	cd redis-3.0.6
	make
	sudo make install

## Redis基本操作

终端运行服务器：
	xwp@xwp-T420:~$ redis-server

指定配置文件运行服务器：
	xwp@xwp-T420:~$ redis-server [./redis.conf]

开启客户端：
	xwp@xwp-T420:~$ redis-cli
	127.0.0.1:6379>

设置/获取key:
	127.0.0.1:6379> set name itcast
	127.0.0.1:6379> get name 

删除key:
	127.0.0.1:6379> del name 

判断key是否存在:
	127.0.0.1:6379> exists name 

停止服务:
	终端CTRL + c
	客户端里SHUTDOWN

rdb持久化存储：
	save -- 阻塞服务，不建议使用
	bgsave -- 子进程异步备份数据

## Redis数据类型

+ String——字符串
+ Hash——字典
+ List——列表
+ Set——集合
+ Sorted Set——有序集合
+ Pub/Sub——订阅
+ Transactions——事务

### String——字符串

String 数据结构是简单的 key-value 类型，value 不仅可以是 String，也可以是数字。使用 Strings 类型，可以完全实现目前 Memcached 的功能，并且效率更高。还可以享受 Redis 的定时持久化（可以选择 RDB 模式或者 AOF 模式）.


string类型是二进制安全的。意思是redis的string可以包含任何数据，比如jpg图片或者序列化的对象。从内部实现来看其实string可以看作byte数组，最大上限是1G字节，下面是string类型的定义:

```c
　　struct sdshdr {
	　　long len;
	　　long free;
	　　char buf[];
　　};
```

　　len是buf数组的长度。

　　free是数组中剩余可用字节数，由此可以理解为什么string类型是二进制安全的了，因为它本质上就是个byte数组，当然可以包含任何数据了

　　buf是个char数组用于存贮实际的字符串内容。

　　另外string类型可以被部分命令按int处理.比如incr等命令，如果只用string类型，redis就可以被看作加上持久化特性的memcached。当然redis对string类型的操作比memcached还是多很多的，具体操作方法如下：




#### 命令示例:

set -- 设置key对应的值为string类型的value。

		> set name itcast

setnx -- 设置key对应的值为string类型的value。如果key已经存在，值不变，返回0，nx是not exist的意思。

	　　 > get name
	　　 "itcast"
		> setnx name itcast_new
		(integer) 0
	　　 > get name
		"itcast"

setex -- 设置key对应的值为string类型的value，并指定此键值对应的有效期。

		> setex color 10 red
		> get color
		"red"
		10秒后...
		> get color
		(nil)

setrange -- 设置指定key的value值的子字符串。

		> set email xwp@itcast.cn
		> get email
		"xwp@itcast.cn"
		> setrange email 4 gmail.com
		> get email
	　　"xwp@gmail.com"
	　　其中的8是指从下标为4(包含4)的字符开始替换

mset -- 一次设置多个key的值，成功返回ok表示所有的值都设置了，失败返回0表示没有任何值被设置。

		> mset key1 python key2 c++
	　　OK

msetnx -- 一次设置多个key的值，成功返回ok表示所有的值都设置了，失败返回0表示没有任何值被设置。

		> get key1
		"python"

get -- 获取key对应的string值,如果key不存在返回nil。

		> get name
		"itcast"

getset -- 设置key的值，并返回key的旧值。

		> get name
		"itcast"
	　	> getset name itcast_new
		"itcast"
		> get name
	　　"itcast_new"

getrange -- 获取指定key的value值的子字符串。

		> getrange name 0 4
	　　"itcas"

mget -- 一次获取多个key的值，如果对应key不存在，则对应返回nil。

		> mget key1 key2 key3

	　　1) "python"
	　　2) "c++"
	　　3) (nil)

incr -- 对key的值做加加操作

		> set age 20
		> incr age
		(integer) 21

incrby -- 同incr类似，加指定值 ，key不存在时候会设置key，并认为原来的value是 0

		> incrby age 5

	　　(integer) 5


decr -- 对key的值做的是减减操作，decr一个不存在key，则设置key为-1

decrby -- 同decr，减指定值

append -- 给指定key的字符串值追加value,返回新字符串值的长度。例如我们向name的值追加一个"redis"字符串:

		> append name redis
		> get name


### Hash——字典
在 Memcached 中，我们经常将一些结构化的信息打包成 hashmap，在客户端序列化后存储为一个字符串的值（一般是 JSON 格式），比如用户的昵称、年龄、性别、积分等。这时候在需要修改其中某一项时，通常需要将字符串（JSON）取出来，然后进行反序列化，修改某一项的值，再序列化成字符串（JSON）存储回去。简单修改一个属性就干这么多事情，消耗必定是很大的，也不适用于一些可能并发操作的场合（比如两个并发的操作都需要修改积分）。而 Redis 的 Hash 结构可以使你像在数据库中 Update 一个属性一样只修改某一项属性值。



hset -- 设置hash field为指定值，如果key不存在，则先创建。

	　　> hset myhash field1 Hello
	　　(integer) 1

hsetnx -- 设置hash field为指定值，如果key不存在，则先创建。如果field已经存在，返回0，nx是not exist的意思。

	　　> hsetnx myhash field "Hello"
	　　(integer) 1
	　　> hsetnx myhash field "Hello"
	　　(integer) 0

　		第一次执行是成功的，但第二次执行相同的命令失败，原因是field已经存在了。

hmset -- 同时设置hash的多个field。

	　　> hmset myhash field1 Hello field2 World
	　　OK

hget -- 获取指定的hash field。

	　　> hget myhash field1
	　　"Hello"
	　　> hget myhash field2
	　　"World"
	　　> hget myhash field3
	　　(nil)

	　　由于数据库没有field3，所以取到的是一个空值nil。

hmget -- 获取全部指定的hash filed。

	　　> hmget myhash field1 field2 field3
	　　1) "Hello"
	　　2) "World"
	　　3) (nil)


hincrby -- 指定的hash filed 加上给定值。

	　　> hset myhash field3 20
	　　(integer) 1
	　　> hget myhash field3
	　　"20"
	　　> hincrby myhash field3 -8
	　　(integer) 12
	　　> hget myhash field3
	　　"12"

hexists -- 测试指定field是否存在。

	　　> hexists myhash field1
	　　(integer) 1
	　　> hexists myhash field9
	　　(integer) 0
	　　通过上例可以说明field1存在，但field9是不存在的。

hlen -- 返回指定hash的field数量。

	　　> hlen myhash
	　　(integer) 4

hkeys -- 返回hash的所有field。

	　　> hkeys myhash
	　　1) "field2"
	　　2) "field"
	　　3) "field3"

	　　说明这个hash中有3个field。

hvals -- 返回hash的所有value。

	　　> hvals myhash
	　　1) "World"
	　　2) "Hello"
	　　3) "12"

	　　说明这个hash中有3个field。

hgetall -- 获取某个hash中全部的filed及value。

	　　> hgetall myhash
	　　1) "field2"
	　　2) "World"
	　　3) "field"
	　　4) "Hello"
	　　5) "field3"
	　　6) "12"



### List——列表
List 说白了就是链表（redis 使用双端链表实现的 List），相信学过数据结构知识的人都应该能理解其结构。使用 List 结构，我们可以轻松地实现最新消息排行等功能（比如新浪微博的 TimeLine ）。List 的另一个应用就是消息队列，可以利用 List 的 *PUSH 操作，将任务存在 List 中，然后工作线程再用 POP 操作将任务取出进行执行。Redis 还提供了操作 List 中某一段元素的 API，你可以直接查询，删除 List 中某一段的元素。

应用场景：
1.微博 TimeLine
2.消息队列

### Set——集合
Set 就是一个集合，集合的概念就是一堆不重复值的组合。利用 Redis 提供的 Set 数据结构，可以存储一些集合性的数据。比如在微博应用中，可以将一个用户所有的关注人存在一个集合中，将其所有粉丝存在一个集合。因为 Redis 非常人性化的为集合提供了求交集、并集、差集等操作，那么就可以非常方便的实现如共同关注、共同喜好、二度好友等功能，对上面的所有集合操作，你还可以使用不同的命令选择将结果返回给客户端还是存集到一个新的集合中。

应用场景：

	1.共同好友、二度好友
	2.利用唯一性，可以统计访问网站的所有独立 IP
	3.好友推荐的时候，根据 tag 求交集，大于某个 threshold 就可以推荐


### Sorted Set——有序集合
和Sets相比，Sorted Sets是将 Set 中的元素增加了一个权重参数 score，使得集合中的元素能够按 score 进行有序排列，比如一个存储全班同学成绩的 Sorted Sets，其集合 value 可以是同学的学号，而 score 就可以是其考试得分，这样在数据插入集合的时候，就已经进行了天然的排序。另外还可以用 Sorted Sets 来做带权重的队列，比如普通消息的 score 为1，重要消息的 score 为2，然后工作线程可以选择按 score 的倒序来获取工作任务。让重要的任务优先执行。

应用场景：

	1.带有权重的元素，比如一个游戏的用户得分排行榜
	2.比较复杂的数据结构，一般用到的场景不算太多


### 订阅-发布系统

Pub/Sub 从字面上理解就是发布（Publish）与订阅（Subscribe），在 Redis 中，你可以设定对某一个 key 值进行消息发布及消息订阅，当一个 key 值上进行了消息发布后，所有订阅它的客户端都会收到相应的消息。这一功能最明显的用法就是用作实时消息系统，比如普通的即时聊天，群聊等功能。


### 事务——Transactions

谁说 NoSQL 都不支持事务，虽然 Redis 的 Transactions 提供的并不是严格的 ACID 的事务（比如一串用 EXEC 提交执行的命令，在执行中服务器宕机，那么会有一部分命令执行了，剩下的没执行），但是这个 Transactions 还是提供了基本的命令打包执行的功能（在服务器不出问题的情况下，可以保证一连串的命令是顺序在一起执行的，中间有会有其它客户端命令插进来执行）。Redis 还提供了一个 Watch 功能，你可以对一个 key 进行 Watch，然后再执行 Transactions，在这过程中，如果这个 Watched 的值进行了修改，那么这个 Transactions 会发现并拒绝执行。



## Redis配置文件



在启动Redis服务器时，我们需要为其指定一个配置文件，缺省情况下配置文件在Redis的源码目录下，文件名为redis.conf。


redis配置文件被分成了几大块区域，它们分别是：

1. 通用（general）
2. 快照（snapshotting）
3. 复制（replication）
4. 安全（security）
5. 限制（limits)
6. 追加模式（append only mode)
7. LUA脚本（lua scripting)
8. 慢日志（slow log)
9. 事件通知（event notification）



为了对Redis的系统实现有一个直接的认识，我们首先来看一下Redis的配置文件中定义了哪些主要参数以及这些参数的作用。

+ daemonize no 

		默认情况下，redis不是在后台运行的。如果需要在后台运行，把该项的值更改为yes；

+ pidfile /var/run/redis.pid 

		当Redis在后台运行的时候，Redis默认会把pid文件放在/var/run/redis.pid，
		你可以配置到其他地址。当运行多个redis服务时，需要指定不同的pid文件和端口；

+ port 6379

		指定redis运行的端口，默认是6379；

+ bind 127.0.0.1

		指定redis只接收来自于该IP地址的请求，如果不进行设置，那么将处理所有请求。在生产环境中最好设置该项；

+ loglevel debug

		指定日志记录级别，其中Redis总共支持四个级别：debug、verbose、notice、warning，
		默认为verbose。debug表示记录很多信息，用于开发和测试。verbose表示记录有用的信息，
		但不像debug会记录那么多。notice表示普通的verbose，常用于生产环境。
		warning 表示只有非常重要或者严重的信息会记录到日志；

+ logfile /var/log/redis/redis.log 

		配置log文件地址，默认值为stdout。若后台模式会输出到/dev/null；

+ databases 16 

		可用数据库数，默认值为16，默认数据库为0，数据库范围在0- 15之间切换,彼此隔离；

+ save 

		保存数据到磁盘，格式为save <seconds> <changes>，指出在多长时间内，有多少次更新操作，
		就将数据同步到数据文件rdb。相当于条件触发抓取快照，这个可以多个条件配合。
		save 900 1就表示900秒内至少有1个key被改变就保存数据到磁盘；

+ rdbcompression yes
		
		存储至本地数据库时（持久化到rdb文件）是否压缩数据，默认为yes；

+ dbfilename dump.rdb

		本地持久化数据库文件名，默认值为dump.rdb；

+ dir ./

		工作目录，数据库镜像备份的文件放置的路径。这里的路径跟文件名要分开配置是因为redis在进行备份时，
		先会将当前数据库的状态写入到一个临时文件中，等备份完成时，再把该临时文件替换为上面所指定的文件。
		而这里的临时文件和上面所配置的备份文件都会放在这个指定的路径当中，AOF文件也会存放在这个目录下面。
		注意这里必须指定一个目录而不是文件；

+ slaveof <masterip> <masterport>

		主从复制，设置该数据库为其他数据库的从数据库。设置当本机为slave服务时，设置master服务的IP地址及端口。
		在Redis启动时，它会自动从master进行数据同步；

+ masterauth <master-password>

		当master服务设置了密码保护时(用requirepass制定的密码)slave服务连接master的密码；

+ slave-serve-stale-data yes 

		当从库同主机失去连接或者复制正在进行，从机库有两种运行方式：如果slave-serve-stale-data设置为
		yes(默认设置)，从库会继续相应客户端的请求。如果slave-serve-stale-data是指为no，
		除去INFO和SLAVOF命令之外的任何请求都会返回一个错误"SYNC with master in progress"；

+ repl-ping-slave-period 10

		从库会按照一个时间间隔向主库发送PING，可以通过repl-ping-slave-period设置这个时间间隔，默认是10秒；

+ repl-timeout 60

		设置主库批量数据传输时间或者ping回复时间间隔，默认值是60秒，一定要确保repl-timeout大于repl-ping-slave-period；

+ requirepass foobared 

		设置客户端连接后进行任何其他指定前需要使用的密码。因为redis速度相当快，所以在一台比较好的服务器下，
		一个外部的用户可以在一秒钟进行150K次的密码尝试，这意味着你需要指定非常强大的密码来防止暴力破解；

+ rename-command CONFIG ""  

		命令重命名，在一个共享环境下可以重命名相对危险的命令，比如把CONFIG重名为一个不容易猜测的字符：
		rename-command CONFIG b840fc02d524045429941cc15f59e41cb7be6c52。
		如果想删除一个命令，直接把它重命名为一个空字符""即可：rename-command CONFIG ""；

+ maxclients 128

		设置同一时间最大客户端连接数，默认无限制。Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数。
		如果设置 maxclients 0，表示不作限制。当客户端连接数到达限制时，
		Redis会关闭新的连接并向客户端返回max number of clients reached错误信息；

+ maxmemory <bytes> 

		指定Redis最大内存限制。Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key，
		Redis同时也会移除空的list对象。当此方法处理后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作。
		注意：Redis新的vm机制，会把Key存放内存，Value会存放在swap区；

+ maxmemory-policy volatile-lru  

		当内存达到最大值的时候Redis会选择删除哪些数据呢？有五种方式可供选择：
		volatile-lru代表利用LRU算法移除设置过期时间的key (LRU:最近使用 Least Recently Used )，
		allkeys-lru代表利用LRU算法移除任何key，
		volatile-random代表移除设置过过期时间的随机key，
		allkeys_random代表移除一个随机的key，
		volatile-ttl代表移除即将过期的key(minor TTL)，
		noeviction代表不移除任何key，只是返回一个写错误。

		注意：对于上面的策略，如果没有合适的key可以移除，写的时候Redis会返回一个错误；

+ appendonly no

		默认情况下，redis会在后台异步的把数据库镜像备份到磁盘，但是该备份是非常耗时的，而且备份也不能很频繁。
		如果发生诸如拉闸限电、拔插头等状况，那么将造成比较大范围的数据丢失，所以redis提供了另外一种更加高效的数据库备份及灾难恢复方式。
		开启append only模式之后，redis会把所接收到的每一次写操作请求都追加到appendonly.
		aof文件中。当redis重新启动时，会从该文件恢复出之前的状态，但是这样会造成appendonly.
		aof文件过大，所以redis还支持了BGREWRITEAOF指令对appendonly.aof 
		进行重新整理，你可以同时开启asynchronous dumps 和 AOF；

+ appendfilename appendonly.aof

		AOF文件名称,默认为"appendonly.aof";

+ appendfsync everysec 

		Redis支持三种同步AOF文件的策略: 
		no代表不进行同步，系统去操作，
		always代表每次有写操作都进行同步，
		everysec代表对写操作进行累积，每秒同步一次，默认是"everysec"，按照速度和安全折中这是最好的。

+ slowlog-log-slower-than 10000 
	
		记录超过特定执行时间的命令。执行时间不包括I/O计算，比如连接客户端，返回结果等，只是命令执行时间。
		可以通过两个参数设置slow log：一个是告诉Redis执行超过多少时间被记录的参数slowlog-log-slower-than(微妙)，
		另一个是slow log 的长度。当一个新命令被记录的时候最早的命令将被从队列中移除，下面的时间以微妙微单位，
		因此1000000代表一分钟。注意制定一个负数将关闭慢日志，而设置为0将强制每个命令都会记录；

+ hash-max-zipmap-entries 512 && hash-max-zipmap-value 64 

		当hash中包含超过指定元素个数并且最大的元素没有超过临界时，hash将以一种特殊的编码方式（大大减少内存使用）来存储，
		这里可以设置这两个临界值。Redis Hash对应Value内部实际就是一个HashMap，实际这里会有2种不同实现。
		这个Hash的成员比较少时Redis为了节省内存会采用类似一维数组的方式来紧凑存储，而不会采用真正的HashMap结构，
		对应的value redisObject的encoding为zipmap。当成员数量增大时会自动转成真正的HashMap，
		此时encoding为ht；

+ list-max-ziplist-entries 512 

		list数据类型多少节点以下会采用去指针的紧凑存储格式；

+ list-max-ziplist-value 64 

		数据类型节点值大小小于多少字节会采用紧凑存储格式；

+ set-max-intset-entries 512 

		set数据类型内部数据如果全部是数值型，且包含多少节点以下会采用紧凑格式存储；

+ zset-max-ziplist-entries 128 

		zsort数据类型多少节点以下会采用去指针的紧凑存储格式；

+ zset-max-ziplist-value 64 

		zsort数据类型节点值大小小于多少字节会采用紧凑存储格式。

+ activerehashing yes 

		Redis将在每100毫秒时使用1毫秒的CPU时间来对redis的hash表进行重新hash，可以降低内存的使用。
		当你的使用场景中，有非常严格的实时性需要，不能够接受Redis时不时的对请求有2毫秒的延迟的话，
		把这项配置为no。如果没有这么严格的实时性要求，可以设置为yes，以便能够尽可能快的释放内存；


## 持久化

### RDB方式(默认)

RDB方式的持久化是通过快照（snapshotting）完成的，当符合一定条件时Redis会自动将内存中的所有数据进行快照并存储在硬盘上。进行快照的条件可以由用户在配置文件中自定义，由两个参数构成：时间和改动的键的个数。当在指定的时间内被更改的键的个数大于指定的数值时就会进行快照。RDB是Redis默认采用的持久化方式，在配置文件中已经预置了3个条件：

	save 900 1    # 900秒内有至少1个键被更改则进行快照
	save 300 10   # 300秒内有至少10个键被更改则进行快照
	save 60 10000 # 60秒内有至少10000个键被更改则进行快照

可以存在多个条件，条件之间是“或”的关系，只要满足其中一个条件，就会进行快照。 如果想要禁用自动快照，只需要将所有的save参数删除即可。

Redis默认会将快照文件存储在当前目录(可CONFIG GET dir来查看)的dump.rdb文件中，可以通过配置dir和dbfilename两个参数分别指定快照文件的存储路径和文件名。

#### Redis实现快照的过程

Redis使用fork函数复制一份当前进程（父进程）的副本（子进程）；
父进程继续接收并处理客户端发来的命令，而子进程开始将内存中的数据写入硬盘中的临时文件；
当子进程写入完所有数据后会用该临时文件替换旧的RDB文件，至此一次快照操作完成。
在执行fork的时候操作系统（类Unix操作系统）会使用写时复制（copy-on-write）策略，即fork函数发生的一刻父子进程共享同一内存数据，当父进程要更改其中某片数据时（如执行一个写命令 ），操作系统会将该片数据复制一份以保证子进程的数据不受影响，所以新的RDB文件存储的是执行fork一刻的内存数据。

Redis在进行快照的过程中不会修改RDB文件，只有快照结束后才会将旧的文件替换成新的，也就是说任何时候RDB文件都是完整的。这使得我们可以通过定时备份RDB文件来实 现Redis数据库备份。RDB文件是经过压缩（可以配置rdbcompression参数以禁用压缩节省CPU占用）的二进制格式，所以占用的空间会小于内存中的数据大小，更加利于传输。

除了自动快照，还可以手动发送SAVE或BGSAVE命令让Redis执行快照，两个命令的区别在于，前者是由主进程进行快照操作，会阻塞住其他请求，后者会通过fork子进程进行快照操作。 Redis启动后会读取RDB快照文件，将数据从硬盘载入到内存。根据数据量大小与结构和服务器性能不同，这个时间也不同。通常将一个记录一千万个字符串类型键、大小为1GB的快照文件载入到内 存中需要花费20～30秒钟。 通过RDB方式实现持久化，一旦Redis异常退出，就会丢失最后一次快照以后更改的所有数据。这就需要开发者根据具体的应用场合，通过组合设置自动快照条件的方式来将可能发生的数据损失控制在能够接受的范围。如果数据很重要以至于无法承受任何损失，则可以考虑使用AOF方式进行持久化。

### AOF方式

默认情况下Redis没有开启AOF(append only file)方式的持久化，可以在redis.conf中通过appendonly参数开启：

	appendonly yes

在启动时Redis会逐个执行AOF文件中的命令来将硬盘中的数据载入到内存中，载入的速度相较RDB会慢一些

开启AOF持久化后每执行一条会更改Redis中的数据的命令，Redis就会将该命令写入硬盘中的AOF文件。AOF文件的保存位置和RDB文件的位置相同，都是通过dir参数设置的，默认的文件名是appendonly.aof，可以通过appendfilename参数修改：

	appendfilename appendonly.aof

配置redis自动重写AOF文件的条件

	auto-aof-rewrite-percentage 100  # 当目前的AOF文件大小超过上一次重写时的AOF文件大小的百分之多少时会再次进行重写，如果之前没有重写过，则以启动时的AOF文件大小为依据

	auto-aof-rewrite-min-size 64mb   # 允许重写的最小AOF文件大小

配置写入AOF文件后，要求系统刷新硬盘缓存的机制

	# appendfsync always   # 每次执行写入都会执行同步，最安全也最慢
	appendfsync everysec   # 每秒执行一次同步操作
	# appendfsync no       # 不主动进行同步操作，而是完全交由操作系统来做（即每30秒一次），最快也最不安全
	Redis允许同时开启AOF和RDB，既保证了数据安全又使得进行备份等操作十分容易。此时重新启动Redis后Redis会使用AOF文件来恢复数据，因为AOF方式的持久化可能丢失的数据更少



## Redis性能测试


+ 测试存取大小为100字节的数据包的性能

		xwp@xwp-T420:~$ redis-benchmark -h 127.0.0.1 -p 6379 -q -d 100

		PING_INLINE: 85910.65 requests per second
		PING_BULK: 123762.38 requests per second
		SET: 85763.29 requests per second
		GET: 81699.35 requests per second
		INCR: 82372.32 requests per second
		LPUSH: 83472.46 requests per second
		LPOP: 82712.98 requests per second
		SADD: 82236.84 requests per second
		SPOP: 83963.05 requests per second
		LPUSH (needed to benchmark LRANGE): 82850.04 requests per second
		LRANGE_100 (first 100 elements): 29585.80 requests per second
		LRANGE_300 (first 300 elements): 9348.42 requests per second
		LRANGE_500 (first 450 elements): 7562.58 requests per second
		LRANGE_600 (first 600 elements): 6780.58 requests per second
		MSET (10 keys): 94428.70 requests per second

+ 100个并发连接，100000个请求，检测host为localhost 端口为6379的redis服务器性能

		xwp@xwp-T420:~$ redis-benchmark -h 127.0.0.1 -p 6379 -c 100 -n 100000

		====== PING_INLINE ======
		  100000 requests completed in 0.83 seconds
		  100 parallel clients
		  3 bytes payload
		  keep alive: 1

		98.95% <= 1 milliseconds
		100.00% <= 1 milliseconds
		120192.30 requests per second

		====== PING_BULK ======
		  100000 requests completed in 0.82 seconds
		  100 parallel clients
		  3 bytes payload
		  keep alive: 1

		100.00% <= 0 milliseconds
		121506.68 requests per second

		====== SET ======
		  100000 requests completed in 0.82 seconds
		  100 parallel clients
		  3 bytes payload
		  keep alive: 1

		99.80% <= 1 milliseconds
		100.00% <= 1 milliseconds
		122249.38 requests per second

		====== GET ======
		  100000 requests completed in 0.81 seconds
		  100 parallel clients
		  3 bytes payload
		  keep alive: 1

		99.79% <= 1 milliseconds
		100.00% <= 1 milliseconds
		122699.39 requests per second

		====== INCR ======
		  100000 requests completed in 0.81 seconds
		  100 parallel clients
		  3 bytes payload
		  keep alive: 1

		99.95% <= 1 milliseconds
		100.00% <= 1 milliseconds
		124223.60 requests per second

		====== LPUSH ======
		  100000 requests completed in 0.82 seconds
		  100 parallel clients
		  3 bytes payload
		  keep alive: 1

		99.82% <= 1 milliseconds
		100.00% <= 1 milliseconds
		122100.12 requests per second

		====== LPOP ======
		  100000 requests completed in 1.30 seconds
		  100 parallel clients
		  3 bytes payload
		  keep alive: 1

		99.93% <= 1 milliseconds
		100.00% <= 1 milliseconds
		77160.49 requests per second

		====== SADD ======
		  100000 requests completed in 0.88 seconds
		  100 parallel clients
		  3 bytes payload
		  keep alive: 1

		99.81% <= 1 milliseconds
		99.97% <= 2 milliseconds
		100.00% <= 2 milliseconds
		113895.21 requests per second

		====== SPOP ======
		  100000 requests completed in 0.82 seconds
		  100 parallel clients
		  3 bytes payload
		  keep alive: 1

		99.78% <= 1 milliseconds
		100.00% <= 1 milliseconds
		121802.68 requests per second

		====== LPUSH (needed to benchmark LRANGE) ======
		  100000 requests completed in 0.81 seconds
		  100 parallel clients
		  3 bytes payload
		  keep alive: 1

		99.09% <= 1 milliseconds
		99.94% <= 2 milliseconds
		100.00% <= 2 milliseconds
		122850.12 requests per second

		====== LRANGE_100 (first 100 elements) ======
		  100000 requests completed in 2.13 seconds
		  100 parallel clients
		  3 bytes payload
		  keep alive: 1

		28.64% <= 1 milliseconds
		99.65% <= 2 milliseconds
		99.97% <= 3 milliseconds
		100.00% <= 3 milliseconds
		47036.69 requests per second

		====== LRANGE_300 (first 300 elements) ======
		  100000 requests completed in 5.46 seconds
		  100 parallel clients
		  3 bytes payload
		  keep alive: 1

		0.01% <= 1 milliseconds
		0.50% <= 2 milliseconds
		82.99% <= 3 milliseconds
		99.11% <= 4 milliseconds
		99.75% <= 5 milliseconds
		99.95% <= 6 milliseconds
		100.00% <= 6 milliseconds
		18325.09 requests per second

		====== LRANGE_500 (first 450 elements) ======
		  100000 requests completed in 7.94 seconds
		  100 parallel clients
		  3 bytes payload
		  keep alive: 1

		0.01% <= 1 milliseconds
		0.10% <= 2 milliseconds
		3.03% <= 3 milliseconds
		58.57% <= 4 milliseconds
		93.27% <= 5 milliseconds
		99.03% <= 6 milliseconds
		99.53% <= 7 milliseconds
		99.72% <= 8 milliseconds
		99.77% <= 9 milliseconds
		99.82% <= 10 milliseconds
		99.85% <= 11 milliseconds
		99.88% <= 12 milliseconds
		99.94% <= 13 milliseconds
		99.97% <= 14 milliseconds
		99.98% <= 15 milliseconds
		99.99% <= 16 milliseconds
		100.00% <= 17 milliseconds
		100.00% <= 17 milliseconds
		12600.81 requests per second

		====== LRANGE_600 (first 600 elements) ======
		  100000 requests completed in 10.34 seconds
		  100 parallel clients
		  3 bytes payload
		  keep alive: 1

		0.00% <= 1 milliseconds
		0.01% <= 2 milliseconds
		0.10% <= 3 milliseconds
		6.40% <= 4 milliseconds
		45.93% <= 5 milliseconds
		84.86% <= 6 milliseconds
		95.54% <= 7 milliseconds
		99.47% <= 8 milliseconds
		99.81% <= 9 milliseconds
		99.94% <= 10 milliseconds
		99.99% <= 11 milliseconds
		100.00% <= 11 milliseconds
		9673.99 requests per second

		====== MSET (10 keys) ======
		  100000 requests completed in 1.01 seconds
		  100 parallel clients
		  3 bytes payload
		  keep alive: 1

		84.16% <= 1 milliseconds
		99.59% <= 2 milliseconds
		99.85% <= 3 milliseconds
		100.00% <= 3 milliseconds
		99206.34 requests per second

## Redis之C接口

+ 网址：

		https://github.com/redis/hiredis

+ 下载:

		git clone https://github.com/redis/hiredis.git

+ 安装:
		
		cd hiredis
		make
		sudo make install
		安装输出
		mkdir -p /usr/local/include/hiredis /usr/local/lib
		cp -a hiredis.h async.h read.h sds.h adapters /usr/local/include/hiredis
		cp -a libhiredis.so /usr/local/lib/libhiredis.so.0.13
		cd /usr/local/lib && ln -sf libhiredis.so.0.13 libhiredis.so
		cp -a libhiredis.a /usr/local/lib
		mkdir -p /usr/local/lib/pkgconfig
		cp -a hiredis.pc /usr/local/lib/pkgconfig

		默认库安装在"/usr/local/lib/",设置加载库路径
		vim ~/.bashrc
		最后一行写入
		export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/lib/
		重启终端


+ 设置共享库环境


		函数原型：redisContext *redisConnect(const char *ip, int port)

说明：该函数用来连接redis数据库，参数为数据库的ip地址和端口，一般redis数据库的端口为6379
该函数返回一个结构体redisContext。
 
		函数原型：void *redisCommand(redisContext *c, const char *format, ...);

说明：该函数执行命令，就如sql数据库中的SQL语句一样，只是执行的是redis数据库中的操作命令，第一个参数为连接数据库时返回的redisContext，剩下的参数为变参，就如C标准函数printf函数一样的变参。返回值为void*，一般强制转换成为redisReply类型的进行进一步的处理。
 
		函数原型void freeReplyObject(void *reply);

说明：释放redisCommand执行后返回的redisReply所占用的内存
 
		函数原型：void redisFree(redisContext *c);

说明：释放redisConnect()所产生的连接。


+ 测试用例：

```c
#include <stdio.h>
#include <stdlib.h>
#include <stddef.h>
#include <stdarg.h>
#include <string.h>
#include <assert.h>
#include <hiredis/hiredis.h>

void doTest()
{
	//redis默认监听端口为6387 可以再配置文件中修改
	redisContext* c = redisConnect("127.0.0.1", 6379);
	if ( c->err)
	{
		redisFree(c);
		printf("Connect to redisServer faile\n");
		return ;
	}
	printf("Connect to redisServer Success\n");
	
	const char* command1 = "set key1 itcast_1";
	redisReply* r = (redisReply*)redisCommand(c, command1);
	
	if( NULL == r)
	{
		printf("Execut command1 failure\n");
		redisFree(c);
		return;
	}
	if( !(r->type == REDIS_REPLY_STATUS && strcasecmp(r->str,"OK")==0))
	{
		printf("Failed to execute command[%s]\n",command1);
		freeReplyObject(r);
		redisFree(c);
		return;
	}	
	freeReplyObject(r);
	printf("Succeed to execute command[%s]\n", command1);
	
	const char* command2 = "strlen key1";
	r = (redisReply*)redisCommand(c, command2);
	if ( r->type != REDIS_REPLY_INTEGER)
	{
		printf("Failed to execute command[%s]\n",command2);
		freeReplyObject(r);
		redisFree(c);
		return;
	}
	int length =  r->integer;
	freeReplyObject(r);
	printf("The length of 'key1' is %d.\n", length);
	printf("Succeed to execute command[%s]\n", command2);
	
	
	const char* command3 = "get key1";
	r = (redisReply*)redisCommand(c, command3);
	if ( r->type != REDIS_REPLY_STRING)
	{
		printf("Failed to execute command[%s]\n",command3);
		freeReplyObject(r);
		redisFree(c);
		return;
	}
	printf("The value of 'key1' is %s\n", r->str);
	freeReplyObject(r);
	printf("Succeed to execute command[%s]\n", command3);
	
	const char* command4 = "get key2";
	r = (redisReply*)redisCommand(c, command4);
	if ( r->type != REDIS_REPLY_NIL)
	{
		printf("Failed to execute command[%s]\n",command4);
		freeReplyObject(r);
		redisFree(c);
		return;
	}
	freeReplyObject(r);
	printf("Succeed to execute command[%s]\n", command4);	
	
	
	redisFree(c);
	
}

int main(void)
{
	doTest();
	return 0;
}


```

+ 编译

		gcc testredis.c -lhiredis -o testredis

+ 运行

		./testredis


## Redis之C++接口

Redis官方引入的c++库众多，但官方没有重点推荐使用哪个：

		http://redis.io/clients#c--


除此之外可以使用Redis官方提供的C语言实现的客户端hiredis，进行封装定制

```c++
//redis.h

#ifndef _REDIS_H_
#define _REDIS_H_

#include <iostream>
#include <string.h>
#include <string>
#include <stdio.h>

#include <hiredis/hiredis.h>

class Redis
{
public:

  Redis(){}

  ~Redis()
  {
    this->_connect = NULL;
    this->_reply = NULL;	    	    
  }

  bool connect(std::string host, int port)
  {
    this->_connect = redisConnect(host.c_str(), port);
    if(this->_connect != NULL && this->_connect->err)
    {
      printf("connect error: %s\n", this->_connect->errstr);
      return 0;
    }
    return 1;
  }

  std::string get(std::string key)
  {
    this->_reply = (redisReply*)redisCommand(this->_connect, "GET %s", key.c_str());
    std::string str = this->_reply->str;
    freeReplyObject(this->_reply);
    return str;
  }

  void set(std::string key, std::string value)
  {
    redisCommand(this->_connect, "SET %s %s", key.c_str(), value.c_str());
  }

private:

  redisContext* _connect;
  redisReply* _reply;

};

#endif 

```

测试用例：

```c++
//main.cpp

#include "redis.h"

int main(void)
{
  Redis *r = new Redis();

  if(!r->connect("127.0.0.1", 6379))
  {
    printf("connect error!\n");
    return 0;
  }
  r->set("name", "itcast cpp");
  printf("Get the name is %s\n", r->get("name").c_str());
  delete r;

  return 0;
}

```

Makefile编译：

```
#Makefile

redis: redis.cpp redis.h
  g++ redis.cpp -o redis -L/usr/local/lib/ -lhiredis

clean:
  rm redis.o redis
```


## Redis之python接口

参考：

		http://redis.io/clients#python

		推荐redis-py

安装:

		sudo pip install redis

Parser可以控制如何解析redis响应的内容。redis-py包含两个Parser类，PythonParser和HiredisParser。
默认，如果已经安装了hiredis模块，redis-py会使用HiredisParser，否则会使用PythonParser。

HiredisParser是C编写的，由redis核心团队维护，性能要比PythonParser提高10倍以上，所以推荐使用。

安装方法：

		sudo pip install hiredis

命令行使用：
	
		启动python或ipython

		>>> import redis
		>>> r = redis.StrictRedis(host='localhost', port=6379, db=0)
		>>> r.set('foo', 'bar')
		True
		>>> r.get('foo')
		'bar'

测试代码：

```python
import redis

class Database:  
    def __init__(self):  
        self.host = 'localhost'  
        self.port = 6379  

    def write(self,website,city,year,month,day,deal_number):  
        try:  
            key = '_'.join([website,city,str(year),str(month),str(day)])  
            val = deal_number  
            r = redis.StrictRedis(host=self.host,port=self.port)  
            r.set(key,val)  
        except Exception, exception:  
            print exception  

    def read(self,website,city,year,month,day):  
        try:  
            key = '_'.join([website,city,str(year),str(month),str(day)])  
            r = redis.StrictRedis(host=self.host,port=self.port)  
            value = r.get(key)  
            print value  
            return value  
        except Exception, exception:  
            print exception  

if __name__ == '__main__':  
    db = Database()  
    db.write('itcastcpp','beijing',2016,1,22,8000)  
    db.read('itcastcpp','beijing',2016,1,22)  

```

## 实战Redis服务器部署

## 总结与建议

### 总结

1.Redis使用最佳方式是全部数据in-memory。

2.Redis更多场景是作为Memcached的替代者来使用。

3.当需要除key/value之外的更多数据类型支持时，使用Redis更合适。

4.当存储的数据不能被剔除时，使用Redis更合适。


### 建议

1.批量处理：

redis在处理数据时，最好是要进行批量处理，将一次处理1条数据改为多条，性能可以成倍提高。测试的目的就是要弄清楚批量和非批量处理之间的差别，性能差异非常大，所以在开发过程中尽量使用批量处理，即每次发送多条数据，以抵消网络速度影响。
 
2.网络：

redis在处理时受网络影响非常大，所以，部署最好能在本机部署，如果本机部署redis，能获取10到20倍的性能。集群情况下，网络硬件、网速要求一定要高。
      
3.内存：

如果没有足够内存，linux可能将reids一部分数据放到交换分区，导致读取速度非常慢导致超时。所以一定要预留足够多的内存供redis使用。


4.少用get/set多用hashset

作为一个key value存在，很多开发者自然的使用set/get方式来使用Redis，实际上这并不是最优化的使用方法。尤其在未启用VM情况下，Redis全部数据需要放入内存，节约内存尤其重要。

假如一个key-value单元需要最小占用512字节，即使只存一个字节也占了512字节。这时候就有一个设计模式，
可以把key复用，几个key-value放入一个key中，value再作为一个set存入，这样同样512字节就会存放10-100倍的容量。

这就是为了节约内存，建议使用hashset而不是set/get的方式来使用Redis


## 命令速查

http://doc.redisfans.com/
