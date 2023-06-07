![[Redis原理_image_1.jpg|400]]
## **为什么使用Redis?** 因为Redis快
**[Redis为什么快?](https://segmentfault.com/a/1190000041488709)**(Redis只有在Linux上能发挥速度,因为只有linux有epoll函数，其它系统会自动降级成select函数,在windows平台不支持windows的iocp函数模型因为是多线程模型且iocp跨平台可能会产生问题)
	1.完全基于内存的IO
	2.绝大部分情况是单线程(避免竞争和锁定)
		Redis的网络IO和键值对的读写都是由一个线程来完成的，Redis的请求时包括获取（Socket读），解析，执行，内容返回（socket写）等都是由一个顺序串行的主线程处理，这就是所谓的单线程。
		在Redis6.0中新增加了多线程的功能来提高I/O读写性能，他的主要实现思路是将**主线程的IO读写任务拆分给一组独立的线程去执行**，将最耗时的socket读取，请求解析，写入单独外包出去，剩下的命令执行任然是由主线程串行执行和内存的数据交互。
		异步删除或大数据量操作等IO多路复用操作是多线程
	3.高效,简单的数据结构(Hash等O(1)结构)
	4.采用IO多路复用和非阻塞IO(epol模型)
		多路:多个客户端访问
		复用:单进程处理多任务套接字
		原理从**select->poll→epol**阶段
		select是不断遍历访问任务是否完成,最多同时管理1024个
		polll使用pollfd数组来代替select中的bitmap，数组中没有1024的限制,是管理量更大的select
		epol改进poll,只轮询那些真正发出了事件的流，并且只依次顺序的处理就绪的流,遍历的时候如果未完成则告诉任务完成后执行XX规范操作(减少遍历次数)
常用场景:
	应对高并发场景,可以将常用的页面或者数据存入redis（内存）,从而减少到数据库访问次数,提高效率和减少数据库压力 
	1.redis还可以充当中间数据库存放登录的session数据,验证码等等，因为分布式集群搭载很多服务器不可能每个都存放复制
	2.redis还可以充当消息队列（生产者消费者队列）
	本质是将存到数据库的内容,放到本地内存中,以此来加快加载速度减少数据库压力，面对高并发压力缓解压力
**Redis常用的基础数据结构:**
	1.字符串String 
		(因为底层是C语言所以redis重新写了个SDS(simple dynamic String)来实现String的功能,内部结构类似java中的arraylist)
		Redis string 将字符串存储到字符类型的`buf[]`中，并使用 `len`、`free`对`buf[]`数组的长度和未使用的字符数进行描述
		![[Redis原理_image_2.png|290]]
		String 采用了预先分配冗余空间的方式来减少内存的频繁分配
		![[Redis原理_image_3.png|350]]
		当字符串所占空间小于 1MB 时，Redis 对字符串存储空间的扩容是以成倍的方式增加的；而当所占空间超过 1MB 时，每次扩容只增加 1MB最大512mb
	2.字典Hash（kv键值对结构 hash可以帮助减少redis中key的数量，hash结构同时很适合存放对象）
	3.列表List
	4.集合Set（有set srand命令可加入数据然后实现随机取出）
	5.有序集合SortedSet(Zset)（通过score进行排序每个key都会设置一个sroce分数该结构适合分页过滤排序）
	6.HyperLogLog
	7.Geo(地理位置)
	8.Pub / Sub
	9.Redis Module(自定义数据类型)
	10.stream(流)
	11.bitmap(位图)
压缩列表和跳跃表的区别
	- **压缩列表**（ziplist）本质上就是一个字节数组，是Redis为了节约内存而设计的一种线性数据结构，可以包含多个元素，每个元素可以是一个字节数组或一个整数。
	- **跳跃表**（skiplist）是一种有序数据结构，它通过在每个节点中维持多个指向其他节点的指针，从而达到快速访问节点的目的。跳跃表支持平均O（logN）、最坏O（N）复杂度的节点查找，还可以通过顺序性操作来批量处理节点。
**redis底层结构**
	**底层就是Hash表的格式**,将数据一个个存入hash表中
	当一个hash值的数据成链表过多的时候
	不是变成红黑树或者二叉树
	而是再reHash一个2倍大的Hash表,一个个重新存入新的大的hash表
	**Redis是由C语言编写的**
**Redsi事务：**
	mutli # 开启事务
	exec # 提交事务
	discard # 回滚事务
	**单条指令能保证原子性，多条不能**
	**编译时异常会回滚，运行时异常不会回滚**
Redis的安装(Linux与windows)
	**windows**
	官方不提供windows版本redis，3.2版本是里程碑
	下载zip包后解压，文件夹内有redis-cli客户端启动和redis-server服务端启动（server是服务器，cli是前端输入指令页面）
	----------------------------------
	**linux**
	 redis6版本要求gcc版本必须5.3以上（默认安装为4.8），查看gcc版本命令gcc -v
	挨个执行gcc完成升级命令
	yum -y install centos-release-scl
	yum -y install devtoolset-9-gcc devtoolset-9-gcc-c++ devtoolset-9-binutils
	scl enable devtoolset-9 bash
	echo "source /opt/rh/devtoolset-9/enable" >>/etc/profile　　--使永久生效
	yum install tcl -y
	配置完环境开始redis安装，同理其他tomcat等安装
	可使用sget和gz包等方式，此处使用gz包下载到目录后tar命令解压到指定目录后进入redis文件夹使用make命令执行makefile文件编译src目录下的c文件然后使用make install安装完毕
	到redis下的bin目录启动redis-server完成启动
	但是该启动页面会固定在linux主页面没法操作，所以要讲reids源文件的conf文件复制过到bin目录进行后台启动
	然后修改复制到bin目录下的conf文件daemonize的值改为yes
	/etc/profile配置环境变量 （REDIS_HOME地址 PATH地址为$REDIS_HOME$/bin）
	source /etc/profile生效刷新
	之后配合conf启动./redis-server ./redis.conf 启动成功
	然后启动redis-cli客户端来输入指令操作
常见的NOSQL数据库：
	1.  键值数据库：Redis、Memcached、Riak
	2.  列族数据库：Bigtable、HBase、Cassandra
	3.  文档数据库：MongoDB、CouchDB、MarkLogic
	4.  图形数据库：Neo4j、InfoGrid
Memcached和Redis比较  
	1.线程上memcached是多线程
	2.memcached支持图文
	3.redis支持持久化和多种数据类型
	![[Redis原理_image_4.jpg]]
Redis常用命令：
	list的：lpush，lpop，lrange，llen
	set的：sadd，spop，smembers，scard，srandmember
	hash的：hset，hget，hmset，hlen，hexisits，hkeys，hvals
	zset的：zadd，zrange，zrevrange，zrangebyscore，zrem
	**其他常用：**
	key相关的：
	keys XXX（查询一类key），exists key（是否存在），del key（删除），expire key seconds（设置key并设置存在时间），ttl key（查看key剩余时间）， persist key（常驻内存key）
	db相关的：
	select index（切换到指定数据库redis有16个db从0-15默认使用0），move key dbindex（迁移到指定数据库），flushdb（清空数据库），flushall（清空所有数据库），dbsize（数据库数据总条数），tastsave（最后一次保存数据的时间戳）
	ACL权限控制命令
	ACL命令（权限控制命令）Access Control
	ACL whoami （查询当前用户）
	ACL list（查询所有用户）
	ACL setuser allen on >123456 +@all ~*（设置一个新用户allen密码为123456，all代表拥有所有命令权限~*代表拥有所有key使用权）
	AUTH allen XXX（auth登录allen账号）
	# 只能创建以lakers为前缀的key
	ACL setuser james on >123456 +@all ~lakers*（同理allen但是只能操作lakers为前缀的keys）
	ACL setuser james -SET（不再拥有set权限）
	ACL DELUSER james（删除jame用户）
	auth输入密码
	其余命令可以去redis官网commands查看

---
## redis的持久化方案:
**RDB(Redis Databases):内存中的数据集快照写入磁盘，也就是 Snapshot 快照,恢复时是将快照文件直接读到内存里。**
	dbfilename dump.rdb文件修改rdb方案
	生成dump.rdb文件需要修改配置dir参数将rdb文件放到指定文件夹，每次生成新的会覆盖旧的
	优点：恢复数据快、节省磁盘空间
	缺点：如果数据量比较大的情况会影响性能、数据持久化得不到很高的保证
两种触发方式:
	1.自动触发,同时满足两个条件时候触发,bin/redis.conf中可以设置
	save 900 1 代表每900秒检测如果修改次数大于1则触发
	save 60 10000 代表每60秒检测一次如果修改次数大于10000则触发
	2.手动触发
	1、save(同步操作)
	该命令会阻塞当前Redis服务器，执行save命令期间，Redis不能处理其他命令，直到RDB过程完成为止。
	显然该命令对于内存比较大的实例会造成长时间阻塞，这是致命的缺陷，为了解决此问题，Redis提供了第二种方式。
	2、bgsave(异步操作)
	执行该命令时，Redis会在后台异步进行快照操作，快照同时还可以响应客户端请求。具体操作是Redis进程执行fork操作创建子进程，RDB持久化过程由子进程负责，完成后自动结束。阻塞只发生在fork阶段，一般时间很短。
	3.正常关闭redis-server（shutdown，手动）
**AOF(Append Of File):以日志的形式记录每个写操作（读操作不保存）**
	appendfilename "appendonly.aof" 文件修改aof方案
	appendonly no 开启aof持久化（默认关闭）
	appendfsync：持久化策略的配置；
	no,写后不会有fsync调用，由操作系统自动调度刷磁盘，性能是最好的。
	always表示每次写入都执行fsync，以保证数据同步到磁盘，效率很低；
	everysec表示每秒执行一次fsync，可能会导致丢失这1s数据。通常选择 everysec ，兼顾安全性和效率。
	优点：丢失数据概率更低
	缺点：恢复数据慢、比RDB占用磁盘空间、频率高对性能有一定影响（因为在主进程中进行）
	特性:
	将Redis所有的写操作记录下来（读操作不记录），以文件追加的方式存放起来，当Redis重启恢复数据时，将操作日志从头到尾执行一遍，恢复数据
持久化方案的选择:
	如果对数据安全性要求很高，采用aof的持久化方案
	如果redis只是做缓存、对数据的安全性要求不是很高，那么采用默认的rdb持久化方案即可
	同时启动并没有问题,以AOF为主
	重启后也以AOF文件做恢复（因为数据完整性更高）
	优化方案:
	bgsave做镜像全量持久化，aof做增量持久化。因为bgsave会耗费较长时间，不够实时，在停机的时候会导致大量丢失数据，所以需要aof来配合使用。在redis实例重启时，优先使用aof来恢复内存的状态，如果没有aof日志，就会使用rdb文件来恢复。

---
## Redis高可用:
**哨兵模式**
哨兵模式特点:
 一主2从3哨兵 哨兵数量必须为2n+1(保证投票顺利)
	读写分离:为了解决redis的读写压力,从而进行分工
	master:主节点负责写,同时可以负责读
	slave:从节点:只能读不能写
	主从同步和一主多从:保证数据的一致性
	为了能开启多个redis,伪装成集群必须要差异化配置
哨兵模式实现:
	主从配置(差异配置必需)
	修改配置项
	pid文件名（pid内存的地址存的进程号）
	port端口
	rdb文件名
	关闭或更换aof名字
	文件配置：
	7001节点配置 :redis7001.conf
	include /export/server/redis/redis.conf
	(指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件) 把通用配置引用过来,同时又重写了自己的特异话配置
	port 7001(伪集群 主要通过端口区分 真集群不需要区分)
	pidfile /var/run/redis7001.pid
	dbfilename redis7001.rdb
	appendfilename "appendonly7001.aof"
	持久化的仆从关系 需要在配置上添加以下2句:
	slaveof 192.168.234.131 7001
	masterauth 123456 从master节点读取数据如果master设置了密码需要输入
	配置完后进入redis-cli -h ip地址 -p 端口号进入redis
	输入命令info replication查看主从关系是否对应
	哨兵模式实现
	直接创建sentinel.conf文件使用命令redis-sentinel sentinel.conf启动即可
	三个哨兵就新建3个分别sentinel01.conf 、sentinel02.conf、sentinel03.conf，注意区分端口
	配置内容:
	bind 0.0.0.0
	#哨兵的端口
	port 26379
	#mymaster自定义
	sentinel monitor mymaster 192.168.234.122 7001 1
	sentinel auth-pass mymaster 123456
	然后启动哨兵（需要差异化配置端口）
	redis-sentinel sentinel01.conf
	redis-sentinel sentinel02.conf
	redis-sentinel sentinel03.conf
	redis哨兵模式如何集成springboot
	修改applicationContext。xml的配置将sentinel哨兵信息告知主从信息告知
**redis集群版**
![[Redis原理_image_5.jpg|400]]
**集群模式特点**:
	1.去中心化
	2.master之间可以通过ping-pong相互通讯
	3.**每个master都被映射到0-16384个插槽上，集群的key是根据key的hash值与插槽绑定**
	hash slot只分配给master不会给slave
	三台服务器搭建集群:master和slave必须错开
	保证一台服务器宕机的时候其slave的备份文件并不会一起消失(6台则无该烦恼)
	取数据时根据get的槽的位置找到集群中对应redis  
linux中如何实现Redis集群
	1.同哨兵模式新建redis7001.conf文件修改差异化配置，在哨兵模式的基础上加cluster-config-file nodes7001.conf（# 生成的node文件）
	2.没有salveof xxx的配置
	#如果redis设置密码，加上如下配置requirepass+”密码“或masterauth +“密码”
	启动每个redis
	redis-cli --cluster create 192.168.234.131:7001 \
	192.168.234.131:7002 \
	192.168.234.131:7003 \
	192.168.234.131:7004 \
	192.168.234.131:7005 \
	192.168.234.131:7006 \
	--cluster-replicas 1
	然后就会提示创建集群3个主节点对应3个从节点-yes
Idea整合redis集群
	SSM项目在xml文件设置
	springboot使用redis起步依赖(内置Lettuce：是Redis的Java实现客户端，提供了比较全面的Redis命令的支持，)
	使用springcloud的yaml会比较方便
	Jedis实现java项目链接redis （以后会用spring整合的spring-data-redis但也需要jedis依赖）
	pom依赖后使用jedis类操作
	在此之前还需要修改redis的bind配置才能保证链接到redis
	可以再cmd使用talnet ip地址 端口命令查看能否链接到
	bind配置本机的网卡对应的ip地址，表示只有通过指定网卡进来的主机才能访问redis
	默认为127.0.0.1本地回环地址表示只有本地能访问redis无法远程链接
	设置完成后redis-cli必须要加-h ip地址来访问指定的ip地址进入
	使用redis.properties配置redis连接池等参数
	使用applicationContext-redis.xml文件引用redis.properties文件进行整合
	使用springcloud后会使用yaml文件配置redis更方便
	额外内容：如果要限制redis访问名单可以通过阿里安全组实现
	redis的密码及权限控制：
	如果要保证数据安全性还需要对redis访问设置密码保证权限控制，需要在conf配置中改requirepass
	设置完成后进入redis后必须要输入auth 密码才能有权限操作同理java访问也需要通过jedis的auth方法先输入密码spring.redis.cluster.nodes=192.168.234.131:7001,192.168.234.131:7002,192.168.234.131:7003
集群与哨兵的差别？
	1.解决哨兵模式的"写"压力大的问题
	2.解决内存存储的瓶颈
**Redsi主从同步原理:**
	主从同步第一次是全量同步：slave第一次请求master节点会根据replid判断是否是第一次同步，是的话master会生成RDB发送给slave。
	-
	后续为增量同步：在发送RDB期间，会产生一个缓存区间记录发送RDB期间产生的新的命令,slave节点在加载完后，会持续读取缓存区间中的数据
**Redis缓存一致性问题?**
	Redis缓存一致性解决方案主要思考的是删除缓存和更新数据库的先后顺序
	1.  使用版本号：在缓存数据中添加版本号，每次更新数据时都更新版本号。当从缓存中读取数据时，与应用程序中的版本号进行比较，如果不一致，则说明数据已经过期，需要从数据库中重新读取。
	2.  使用缓存锁：在更新缓存数据时，使用缓存锁来防止多个线程同时更新同一条数据。可以使用Redis的SETNX命令来实现缓存锁。
	-
	用中间件canal订阅binlog日志提取需要删除的key，然后另写一段非业务代码去获取key并尝试删除，若删除失败就把删除失败的key发送到消息队列，然后进行删除重试


缓存击穿问题(热点数据过期,导致高并发去访问数据库)
	1.加锁(分布式锁)，两次判断，如果redis没有则去数据库取并重新赋值到redis，如果有则直接从redis取（减轻了一直从数据库取消耗的资源和时间减轻数据库压力）
	2.热点数据永不过期
缓存穿透(不存在数据访问导致缓存失效一直访问数据库)
	1.缓存空值和虚假值并设置过滤条件筛除明显不符合条件的数据例如id<0
	(注意设置过期时间小于普通数据过期时间)
	2.布隆过滤器(提前将数据库中所有的商品id放入布隆过滤器)
缓存雪崩(数据同时过期对数据库压力增大)
	1.设置过期时间函数消减过期峰值,让过期时间平均分布 

Redis内存淘汰策略:
	常用:(1)淘汰最久没使用的（2）淘汰一段时间内最少使用的（3）淘汰快要过期的

Redis+Lua脚本
	lua是轻量化C语言脚本
	辅助Redis脚本化命令(核心作用将多个命令写成lua脚本从而原子化)

Redsi客户端(Lettuce和Jedis)：
	Springboot启动依赖中配置的就是Lettuce替代了Jedis作redis客户端
	高级Redis客户端，用于线程安全同步，异步和响应使用，支持集群，Sentinel，管道和编码器。**`Lettuce`是一个高性能基于`Java`编写的`Redis`驱动框架**
	[Lettuce详解](https://juejin.cn/post/6844903954778701832)