全称:message Queue(异步请求中间件)
nameserver端口:9876 启动端口自行设置:8011
常用有:rabbitMQ和Kafka此处主要使用rocketMQ
MQ最大作用和特性:
	**1.异步处理**
	只需要将Data传给MQ队列然后MQ会返回ack就可以继续执行了,消息在MQ队列中由消费者自行消费(完全解耦合)
	**2.流量削峰:**
	如果有大量的请求放到队列会平均分散到一段时间处理
	MQ底层设计模式:
	生产者消费者的设计模式
	生产者(客户端)：生产消息,往queue存放消息
	消息队列Queue(MQ)：存储消息(缓存作用)
	消费者(服务端)：消费消息
![[RocketMQ(异步同步)_image_1.jpg]]
rocketMQ安装和启动:
	**windows:**
	1.下载release的zip包或者下载二进制包使用javac编译
	配置ROCKETMQ_HOME环境变量指定到release文件夹
	注意环境变量路径上的文件夹不要带中文
	2.到conf包创建nameserver.properteis然后配置
	listenPort=9876
	listenIp=127.0.0.1
	3.到bin目录下创建bat脚本文件my-nameserver.bat
	写上start mqnamesrv.cmd -c ../conf/nameserver.properties
	(由mqnamesrv.cmd启动runserver.cmd完成nameserver启动)
	runserver.cmd中可以修改占用内存大小
	4.到conf包下配置broker.conf
	brokerClusterName=rocketmq-cluster(#所属集群名字)
	brokerName=broker-a(#broker名字，注意此处不同的配置文件填写的不一样)
	brokerId=0(#0 表示 Master，>0 表示 Slave)
	namesrvAddr=127.0.0.1:9876(#nameServer地址，集群使用分号分割)
	listenPort=10911(#Broker 对外服务的监听端口)
	brokerIP1=127.0.0.1(#Broker监听的ip)
	defaultTopicQueueNums=4(#在发送消息时，自动创建服务器不存在的topic，默认创建的队列数)
	autoCreateTopicEnable=true(#是否允许 Broker 自动创建Topic，建议线下开启，线上关闭)
	autoCreateSubscriptionGroup=true(#是否允许 Broker 自动创建订阅组，建议线下开启，线上关闭)
	deleteWhen=04(#删除文件时间点，默认凌晨 4点)
	fileReservedTime=120(#文件保留时间，默认 48 小时)
	mapedFileSizeCommitLog=1073741824(#commitLog每个文件的大小默认1G)
	mapedFileSizeConsumeQueue=300000(#ConsumeQueue每个文件默认存30W条，根据业务情况调整)
	5.创建my-broker.bat文件启动runbroker.cmd文件
	写上start mqbroker.cmd -c ../conf/broker.conf
	runbroker.cmd中可以修改配置内存大小
	set "JAVA_OPT=%JAVA_OPT% -cp "%CLASSPATH%""
	注意这个CLASSPATH外边如果没有双引号需要再包一层不然可能提示找不到jdk包中的dt.jar
	6.下载控制台源码(控制台可视化查看辅助操作MQ)
	[https://github.com/apache/rocketmq-externals/archive/master.zip](https://github.com/apache/rocketmq-externals/archive/master.zip)
	然后idea打开源码找到rocketmq-console-ng模块
	修改对应配置:服务的端口号和nameserver的ip地址和端口号,然后到pom文件修改rocketmq的版本为自己下载的指定版本
	加入commons-io的jar包
	7.将rocketmq-console-ng包使用maven的packge打包成jar包
	mvn clean package -Dmaven.test.skip=true
	放到桌面后写一个bat脚本 java -jar XXX启动项目
	最后使用指定的ip端口号打开页面即可(页面中可以配置nameserver地址,查看消息是否发送成功,配置broker集群节点等)
	**linux:**
	1.tar.gz包解压
	2.见网上
	3.下载控制台源码
	git clone https://github.com/apache/rocketmq-externals.git
MQ的启动流程:
	![[RocketMQ(异步同步)_image_2.jpg]]

MQ进程角色说明
	![[RocketMQ(异步同步)_image_3.jpg|600]]
	生产者组（Producer Group）
		同一类Producer的集合，这类Producer发送同一类消息且发送逻辑一致。如果发送的是事务消息且原始生产者在发送之后崩溃，则Broker服务器会联系同一生产者组的其他生产者实例以提交或回溯消费。
	消费者组（Consumer Group）
		同一类Consumer的集合，这类Consumer通常消费同一类消息且消费逻辑一致。消费者组的消费者实例必须订阅完全相同的Topic(消费同一种信息,比如订单信息)。
		RocketMQ 支持两种消息模式：**集群消费**（Clustering）和**广播消费**（Broadcasting）。
		消息服务器(Broker)->消息存储中心
		名称服务器(NameServer)(类似服nacos的NameServer用来存放Broker服务,让Broker注册)
		解决增加broker集群定位等问题
		用来保存 Broker 相关 Topic 等元信息并给 Producer ，提供 Consumer 查找 Broker 信息。做一个负载均衡
	消息（Message）(传输的最小单位)
		生产和消费数据的最小单位，消息系统所传输信息的物理载体,每条消息必须属于一个主题(Topic)。RocketMQ中每个消息拥有唯一的Message ID，且可以携带具有业务标识的Key。系统提供了通过Message ID和Key查询消息的功能。
	主题（Topic）(一组消息的组名)
		主题之间消息完全隔离每条消息只能属于一个主题，是RocketMQ进行消息订阅的基本单位。
	标签（Tag）(用于将一组消息中不同消息标签化分类)
		方便MQ查询
RocketMQ(分布式架构)启动流程
	![[RocketMQ(异步同步)_image_4.jpg]]
	1. 启动 Namesrv，Namesrv起 来后监听端口，等待 Broker、Producer、Consumer连上来，相当于一个路由控制中心。
	2. Broker 启动，跟所有的 Namesrv 保持长连接，定时发送心跳包。
	3. 收发消息前，先创建 Topic 。创建 Topic 时，需要指定该 Topic 要存储在 哪些 Broker上。也可以在发送消息时自动创建Topic。
	4. Producer 发送消息。
	5. Consumer 消费消息。
MQ常用注解
	@RocketMQMessageListener(consumerGroup = "es-consumer",
	topic = "test-topic4",selectorExpression = "test-tag4",
	consumeMode = ConsumeMode.CONCURRENTLY,
	messageModel = MessageModel.CLUSTERING)

---
## MQ的事务消息
![[RocketMQ(异步同步)_image_5.jpg]]
正常事务发送与提交阶段
	1.生产者发送一个半消息给broker(半消息是指的暂时不能消费的消息)
	2.服务端响应
	3.开始执行本地事务
	4.根据本地事务的执行情况执行Commit或者Rollback
事务信息的补偿流程
	1.如果broker长时间没有收到本地事务的执行状态,会向生产者发起一个确认会查的操作请求(默认最多15次后直接回滚)
	2.生产者收到确认会查请求后,检查本地事务的执行状态
	3.根据检查后的结果执行Commit或者Rollback操作 补偿阶段主要是用于解决生产者在发送Commit或者Rollbacke操作时发生超时或失败的情况
事务消息的三种状态:
	TransactionStatus.CommitTransaction：提交事务消息，消费者可以消费此消息
	TransactionStatus.RollbackTransaction：回滚事务，它代表该消息将被删除，不允许被消费。
	TransactionStatus.Unknown ：中间状态，它代表需要检查消息队列来确定状态。
如何使用事务消息:
	在创建生产者时使用TransactionMQProducer 而不是默认的DefaultMQProducer .并且设置 setTransactionListener(new TransactionListener(){…}); 实现接口方法事务监听器,监听事务状态
事务消息的特性:
	1.事务消息不支持定时和批量
	2.为了避免一个消息被多次检查，导致半数队列消息堆积，RocketMQ限制了单个消息的默认检查次数为15次，通过修改broker配置文件中的transactionCheckMax参数进行调整
	3.特定的时间段之后才检查事务，通过broker配置文件参数transactionTimeout或用户配置CHECK_IMMUNITY_TIME_IN_SECONDS调整时间
	4.一个事务消息可能被检查或消费多次
	5.提交过的消息重新放到用户目标主题可能会失败
	6.事务消息的生产者ID不能与其他类型消息的生产者ID共享

---
## MQ的持久化(消息持久化)
简介:
	![[RocketMQ(异步同步)_image_6.jpg]]
	生产者发送消息到Broker后，Master-Broker会将消息写入磁盘上的一个日志文件——CommitLog，按照顺序写入文件末尾，CommitLog中包含了各种各样不同类型的Topic对应的消息内容，CommitLog文件每个限定最大1GB，Master-Broker收到消息之后就将内容直接追加到文件末尾，如果一个CommitLog写满了1GB，就会创建一个新的CommitLog文件。
	ConsumeQueue文件里存储的是一条消息对应在CommitLog文件中的offset偏移量,消息长度,tag hashcode等而不是data本身
	优点是(commitLog被所有消费队列共享,所有读写都是由commitLog来串行化访问磁盘读写 ,)
	缺点是(写顺序一致向最后一条加,但是读取数据是随机的会先读取消费队列再读取CommitLog)
	优化方案:使用PageChache先写入内存,然后统一异步刷新到commmitLog
MQ结合IDEA:
	1.导入pom依赖
	org.apache.rocketmq.rocketmq-spring-boot-starter 2.1.0(启动包对应的是rocketMQ的4.6.0版本)
	2.properties配置
	#指定nameServer
	rocketmq.name-server=127.0.0.1:9876
	#指定发送者组名
	rocketmq.producer.group=wfx-goods-producer
	3.在Controller层直接注入rocketMQTemplate就可以使用了能实现大部分功能,复杂的自己可以重写
	4.消费模块(例如ES模块)实现自定义监听器(接收product生产者模块发送的消息) implements RocketMQListener\<T>
	加上注解@RocketMQMessageListener(设置消费者组名,主题,标签,消费模式(order或无序),消费模式(集群或广播))
	5.重写onMessage(T){}方法将接收到的T类型的消息处理(例如注入ESService然后使用.toES(T)将数据传到ES库)
**发送消息的步骤:**
	1.创建消息生产者producer(DefaultMQProducer)，并指定生产者group
	DefaultMQProducer product = new DefaultMQProducer(组名)
	2.指定producer的setNamesrvAddr()设置Nameserver地址
	3.启动producer.start()
	4.创建Message对象，new Message(topic,tags,消息的字节)
	5.发送消息producer.send(msg)
	6.关闭生产者producer.shutdown()
	**---详细:**
	1.创建消费者Consumer(DefaultMQPushConsumerconsumer = new DefaultMQPushConsumer(组名))
	push模式,设置每隔一段时间broker将消息推给消费者
	pull模式,由消费者自行获取消息消费
	2.指定Nameserver地址
	consumer,.setNamesrvAddr()
	3.订阅(subscribe)指定--主题Topic(还可以设置订阅指定标签)
	consumer.subscribe("test-topic1","test-tag1");
	4.设置消费模式(MessageModel),
	consumer.setMessageModel(MessageModel.XXX)
	MessageModel.CLUSTERING (集群模式,默认集群 )
	MessageModel.BROADCASTING(广播模式)
	设置抓取个数consumer.setPullBatchSize(个数)
	5.注册（register）回调函数，处理消息
	consumer.registerMessageListener(new MessageListenerConcurrently(){
	ConsumeConcurrentlyStatus consumeMessage(List\<MessageExt> msgs, ConsumeConcurrentlyContext context){
	通过遍历List集合拿到消息,获取消息中的内容
	}
	}
	)
	6.开启集群 consumer.start();(不要关)
**发送消息模式(同步,异步,单向)**
	1.同步发消息(生产者同步发送给MQ,MQ还是异步处理消息)
	消息发送会阻塞send方法,发送获得ack(应答)后继续往下执行,(默认等待5秒不回再继续往下走)这种方式保证了0消息丢失
	2.异步发送
	异步发送不会阻塞send方法,发送结果通过回调的方式告诉producer
	消息重试次数设置:
	producer.setRetryTimesWhenSendFailed();
	可以使用修改重试次数和sendResult()做补偿逻辑来保证消息0丢失
	3.单向消息
	单向消息不会阻塞send方法,不关心发送是否成功
	异步消息和同步消息差别就是send方法 异步消息使用重载的send方法 且不能关闭生产者 单向消息使用sendOneway()方法,不回调也不关闭生产者
	消费者集群创建(消费消息)
两种消费模式:
	1.集群消费模式
	多个数据面对多个消费者时,不同消费者不会重复消费同一个消息,默认只允许消息被消费一次,不会被重复消费
	2.广播消费模式
	多个数据面对多个消费者时,一条消息会重复被多个消费者消费,

---
## 常见问题:

如何保证消息0丢失?
	同步发送就是0丢失,异步发送则可以设置失败多次重试,如果还是失败可以回调函数补偿或进入死信队列后查看问题
消息队列(queue)中如何全局有序?
	![[RocketMQ(异步同步)_image_7.jpg]]
	全局有序消息不适合广播模式,只适合集群模式
	默认一个topic创建4个queue来存放消息(可以在conf下修改broke.conf)生产者发布的消息会分布在不同的queue中,对于一个具体的queue是局部有序,但是对全局无序
	解决办法:
	设置queue下标,将全部消息存放在一个queue中,放空其他queue
	product.send(消息,new MessageQueueSelector() {
	public MessageQueue select(List\<MessageQueue> list, Message message, Object o) {
	int index = (Integer) 队列脚标;
	return list.get(index);(返回Q队列集合中的第脚标个)
	}
	}, 队列脚标);)(此处的队列脚标赋值给Object o)
	consumer使用orderly模式进行有序消费
消息分区队列(queue)有序?
	(例如:湖北放一个queue,湖南放一个queue)
	解决办法:
	product可以根据消息的属性判断将湖北省放到脚标0的quee,然后湖南省放到脚标1的queue中
	consumer使用orderly模式进行有序消费
	同理可以设置一个内置循环数对queue进行负载均衡
MQ的重试机制可能会引发重复消费怎么解决?
	以集群方式消费。假设一条消息发往ConsumerGroup，由A消费，但是由于A消费过慢导致超时，如果Broker将消息发送给B去消费，这样就产生了重复消费问题。因此，使用MQ时应该对一些关键消息进行幂等去重的处理(例如为单个消息设置唯一索引(UUID雪落id等)或先查询后插入或redis介入)。
延时消息(rocketMQ放入temp-queue)
	![[RocketMQ(异步同步)_image_8.jpg]]
	例如:解决订单长时间未支付
	RabbitMQ使用: TTL+死信队列
	RocketMQ使用延时消息
	message.setDelayTimeLevel(级别);
	延时级别效果在broke.conf修改
	会先在临时队列tmp-queue停留延时队列限定时间后再存入queue,后被消费者消费
重试队列(RocketMQ消费端默认有重试机制)
	只有在消息模式为集群模式时，Broker才会自动进行重试，广播消息是不会重试的。
	消费端重试分为两种原因
	1.异常重试：由于Consumer端逻辑出现了异常，导致返回了RECONSUME_LATER状态，那么Broker就会在一段时间后尝试重试。
	2.超时重试：如果Consumer端处理时间过长，或者由于某些原因线程挂起，导致迟迟没有返回消费状态，Broker就会认为Consumer消费超时，此时会发起超时重试。
	重试队列名称为：%RETRY%+consumergroup
	可在broker.conf文件中配置Consumer端的重试次数和重试时间间隔
	messageDelayLevel=1s 5s 10s 30s 1m 2m 3m 4m 5m 6m 7m 8m 9m 10m 20m 30m 1h 2h
死信队列(Dead-Letter Queue->Letter Message）
	正常情况下无法被消费(发送多次达到重试次数上限)的消息称为死信消息 存储死信消息的特殊队列称为死信队列
	**死信消息特性：**
	1:不会再被消费者正常消费。
	2:有效期与正常消息相同，均为 3 天，3 天后会被自动删除。因此，请在死信消息产生后的 3 天内及时处理。
	死信队列特性：
	1:一个死信队列对应一个 Group ID， 而不是对应单个消费者实例。
	2:如果一个 Group ID 未产生死信消息，消息队列 RocketMQ 版不会为其创建相应的死信队列。
	3:一个死信队列包含了对应 Group ID 产生的所有死信消息，不论该消息属于哪个 Topic。
	4:消息队列 RocketMQ 版控制台提供对死信消息的查询、重发的功能。