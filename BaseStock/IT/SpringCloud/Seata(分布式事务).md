事务4大特性ACID
	⑴ 原子性（Atomicity）
	⑵ 一致性（Consistency）
	⑶ 隔离性（Isolation）
	⑷ 持久性（Durability）
**本地事务和分布式事务区别**
	**本地事务:**
	1个数据源调用多个数据源
	![[Seata(分布式事务)_image_1.jpg|275]]
	**分布式事务:**
	1个数据源调用多个数据源,多个数据源也调用多个数据源
	![[Seata(分布式事务)_image_2.jpg|450]]
**Seata作用**:
	解决分布式服务中远程调用其他服务事务的回滚不能通过@Transactional本地事务完成回滚
**Seata工作原理:**
**Seata(AT模式)**(强一致性,弱可用性,效率高0侵入)
	
	![[Seata(分布式事务)_image_3.jpg|500]]
	AT模式角色分析(具体可查Seata快速入门官方文档):
	**Transaction Coordinator (TC)：**
		类似nacos注册与发现功能(seata-server)大的监视器
		事务协调器，维护全局事务的运行状态，负责协调并实现全局事务的提交或回滚
	**Transaction Manager ™(TM)：**
		事务的开启和任何异常处理的发起者(TC是实际处理者)
		控制全局事务的边界，负责开启一个全局事务，并最终发起全局提交或全局回滚的决议
	**Resource Manager (RM)：**
		每个服务的小监视器(检测服务事务是否成功如果失败反馈给TC)
		控制分支事务，负责分支注册、状态汇报，并接收事务协调器的指令，驱动分支（本地）事务的提交和回滚
	**Seata启动到结束详细流程:**
		**第一步:**
		.TM向TC申请开启全局事务,TC会生成一个**xid(全局事务id)**
		全局事务id会在调用链中通过openFeign传播
		**第二步:**
		事务获取全局锁(seataServer中有张lock_Table表 对应xid而xid对应一个pk(primeKey))
		两个全局事务 tx1 和 tx2，分别对 a 表的 m 字段进行更新操作，m 的初始值 1000。
		tx1 先开始，开启本地事务，拿到本地锁，更新操作 m = 1000 - 100 = 900。本地事务提交前，先拿到该记录的 全局锁 ，本地提交释放本地锁。 tx2 后开始，开启本地事务，拿到本地锁，更新操作 m = 900 - 100 = 800。本地事务提交前，尝试拿该记录的 全局锁 ，tx1 全局提交前，该记录的全局锁被 tx1 持有，tx2 需要重试(CAS自旋)等待 全局锁 。
		**第三步:**
		(一阶段提交)
		![[Seata(分布式事务)_image_4.jpg]]
		注册分支事务(Seata会封装代理增强database)
			1.解析SQL得到SQL类型(UPDATE),表(product),条件(where)等相关信息
			2.查询**前镜像**(数据修改前的数据);
			根据解析得到的条件信息,生成查询语句,定位数据
			3.执行业务SQL,增.删.改这条记录
			4.查询**后镜像**(数据修改后的数据),
			根据前镜像的结果拿到数据主键,然后通过主键定位数据
			5.插入回滚日志:将前镜像和后镜像以及SQL相关信息组成一条回滚日志(JSON格式)记录,插入到UNDO_LOG表(对应的服务的db数据库)中
			6.将sql直接commit,该数据锁住(行级锁),生成分支id,同时与xid绑定到seata-server的branch_table表
			7.将本地事务提交的结果上报给TC
		(二阶段回滚和提交)
		如果发生异常:
			![[Seata(分布式事务)_image_5.jpg]]
			1.TM通知tc进行全局事务回滚,
			2.拿 UNDO LOG 中的后镜与当前数据进行比较，如果有不同，说明数据被当前全局事务之外的动作做了修改(另做处理)
			3.一般校验正确则使用UNDO_LOG表中的信息进行回滚
			4.然后undo_log , 全局事务xid , branch_table的数据,
			global_table lock_table删除
			5.提交本地事务。并把本地事务的执行结果（即分支事务回滚的结果）上报给 TC
		(二阶段提交)
		如果全局事务成功:
			1.TC收到所有分支事务的提交请求,把请求放入一个异步任务的队列中进行全局事务提交
			2.然后分支事务将异步和批量地删除相应undo_log ,
			3.全局事务xid , branch_table的数据,
			global_table lock_table删除
**TCC模式(底层有入侵)**
		-
**Saga模式(软事务)**
		-

Seata安装和应用:
	**windows和linux(将windows的复制进linux即可)**:
	1.下载Seata-server(官网二进制包binary)
	2.配置
	修改Seata-server下的file.conf->mode="db".并配置mysql的url和账号密码
	2.mysql中创建数据库(名字为seata)
	3.创建3张表(global_table,branch_table,lock_table)
	可以到官网或者github查询具体sql表的字段
	4.将seata放到nacos指定新建的namespace(名为seata因为配置文件比较多),需要使用seata服务时去访问这个命名空间
	5.编辑conf文件夹下的registry.conf配置文件,
	注册中心的type指定为nacos,配置nacos的url等信息,namespace指定为seata
	config配置中心的type指定为nacos,配置url等信息,namespace指定为seata
	6.从[https://github.com/seata/seata/tree/develop/script/config-center](https://github.com/seata/seata/tree/develop/script/config-center)
	github下获取config.txt文件
	复制到本地后修改
	service.vgroupMapping.自定义名字_tx_group=default
	store.mode=db
	store.db.driverClassName=com.mysql.jdbc.Driver
	store.db.url=jdbc:mysql://127.0.0.1:3306/seata?useUnicode=true
	store.db.user=root
	store.db.password=123456
	再使用官方脚本nacos-config.sh(会去上一级文件夹中主动找配置项config.txt)加载到nacos中
	使用git bash运行(linux则直接使用sh命令即可)
	sh seata-config.sh -h 127.0.0.1 -p 8848 -g SEATA_GROUP
	-t seata
	启动bin目录下的bat(sh)运行
	nohup sh /root/export/server/seata/bin/seata-server.sh > /root/export/server/seata/logs/seata.log 2>&1 &
Idea整合Seata
	0.创建undo_log表到服务的数据库(官网或github查具体字段)
	1.导入pom依赖(注意导入seata-spring-boot-starter需要排除自身的seata-all,然后导入之前下载seata版本一致的seata-all包)
	io.seata.seata-spring-boot-starter 1.3.0
	<!-- 这里需要排除自身的seata-all -->
	\<exclusions>
	\<exclusion>
	\<artifactId>seata-all\</artifactId>
	\<groupId>io.seata\</groupId>
	\</exclusion>
	\</exclusions>
	导入与之前下载的seata版本一致的包
	io.seata.seata-all 1.3.0
	2.配置properties文件
	seata.enabled=true//使用seata开启
	seata.tx-service-group=fy_group//该属性需要和配置文件config.txt文件中service.vgroupMapping一致
	配置seata,指定seata的配置中心,指定seata的注册中心
	3.在需要使用seata全局事务的实现接口上写上@Glovaltransational
	4.配置FeignClient拦截器(通过openFiegn传递xid)
	将xid放入threadLocal(RootContext.getXID()获取xid然后通过RequestTemplate.header()放入请求头中)
	5.下游微服务获取并绑定xid(不要加全局事务的注解) 配置拦截器(Interceptor)获取xid
	request.getHeader(XID)然后RootContext.bind(XID)将xid绑定到threadLocal中
	最后将配置拦截器的@Configuration类将刚写的自定义拦截器类@Autowired注入进来并配置拦截路径

Seata工作流程:
	@GloableTransational(TM)
	一个注解搞定
	TM控制全局的操作最初一行到最后一行,包括除了事务参与者以外的其他代码
	如果其他代码报错TM会告诉TC事务回滚不能执行
	假如有两个操作,
	1.生成订单(order微服务)2.减库存(goods微服务)
	order和goods分别对应不同的数据库连接,这时候就需要一个协调器(TC)协调两者的事务,
	1.这两个微服务向TC注册
	2.注册成功后由每个RM控制不同的微服务.由RM向TC汇报
**理论基础:**
	**CAP理论**
	大规模的分布式系统时会遇到三个特性：
	**一致性（consistency）**、
	**可用性（Availability）**、
	**分区容错（partition-tolerance）**
	而一个分布式系统最多只能满足其中的2项。
	一致性（consistency）
	即更新操作成功并返回客户端完成后，所有节点在同一时间的数据完全一致，不能存在中间状态(强一致性)
	如果允许存在一段中间状态,但是最终数据一致称之为最终一致性,允许部分数据不一致,那么为弱一致性
	可用性（Availability）
	可用性是指系统提供的服务必须一直处于可用的状态，对于用户的每一个操作请求总是能够在有限的时间内返回结果。
	分区容错性（partition-tolerance）
	分布式系统在遇到任何网络分区故障的时候，仍然需要能够保证对外提供满足一致性和可用性的服务，除非是整个网络环境都发生了故障。
	保证强一致性,必须加锁保证没有中间状态,但是枷锁一定会导致没有可用性
	CAP是不能同时满足的
	CA：单机业务可以满足一致性和可用性
	PA：分布式系统满足了强一致性，就必须丢失可用性
	PC：分布式系统满足了可用性，就必须丢失强一致性
	**BASE理论(CAP理论延伸)**
	基本可用（Basically Available）
	指分布式系统在出现不可预知故障的时候，允许损失部分可用性。
	软状态（ Soft State）
	指允许系统中的数据存在中间状态，并认为该中间状态的存在不会影响系统的整体可用性。
	最终一致（ Eventual Consistency）
	强调的是所有的数据更新操作，在经过一段时间的同步之后，最终都能够达到一个一致的状态。因此，最终一致性的本质是需要系统保证最终数据能够达到一致，而不需要实时保证系统数据的强一致性。