### SpringTask
SpringTask简介
	轻量级的Quartz(定时任务工具)
	只需要spring-web的jar包不需要额外依赖
	启动器上@EnableScheduling开启
	使用方式(定时调用方法):
	@Component
	public class TaskTest {
	@Scheduled(cron = "* * * * * ?")
	public void po(){
	System.out.println(System.currentTimeMillis());
	}
	}
cron表达式可自行网上查阅(或使用网上的在线cron表达式生成器)\
死锁的4个必要条件(取消一个便可避免死锁)
	1、互斥条件：一个资源每次只能被一个进程使用；
	2、请求与保持条件：一个进程因请求资源而阻塞时，对已获得的资源保持不放；
	3、不剥夺条件:进程已获得的资源，在末使用完之前，不能强行剥夺；
	4、循环等待条件:若干进程之间形成一种头尾相接的循环等待资源关系；
分布式锁(redis生成"独立"锁)
	例如:解决超买超卖问题
	加锁解决高并发同时访问到库存数量>0的情况
	单机版sync可以做锁,但是集群需要分布式锁
	redis中命令 **setnx锁**(set not exist)
	**原理:设置一个锁由1个线程获取返回1表示成功,第二个线程再去获取的时候返回0表示失败,直到第一个线程删除锁**
	stringRedisdTemplate,boundValueOps("lock:"+goodId).setIfAbsent("1")
	if判断是否有锁,成功才能下单这种方式不会阻塞线程
	while循环判断是否有锁,直到成功才能下单这会阻塞线程
	最后根据key删除锁
**分布式死锁(原因和解决方案)**
	原因1:获取到锁的线程一直没有释放锁造成死锁(可能expire命令前程序崩溃导致未执行)
	解决方案1:死锁解决方案:锁设置过期时间,
	设置过期时间产生的问题:
	1.线程1还没处理完,锁已经过期,线程2获取到锁然后被线程1把锁释放掉了
	2.线程1业务没处理完,锁过期,线程2获取锁造成超买超卖问题
	解决问题方案1:
	释放错锁的问题:1.将锁的value值进行判断后删除
	解决方案2:
	使用Redisson框架(解决分布式死锁)
	Redisson是架设在Redis基础上的一个JAVA驻内存数据网络
	当程序还没执行完会自动给线程锁续命10秒,直到锁被消耗则不会续命,5秒后超时
Redisson结合IDEA实现:
	1.pom依赖
	org.redisson.redisson 3.12.0
	2.配置
	@Configuration
	public class MyRedissonConfig {
	//注册RedissonClient对象
	@Bean(destroyMethod="shutdown")
	RedissonClient redisson() throws IOException {
	Config config = new Config(); config.useSingleServer().setAddress("redis://192.168.182.150:6379");
	RedissonClient redissonClient = Redisson.create(config);
	return redissonClient;
	}
	}
	3.Controller层注入 RedissonClient 直接使用
	使用RLock lock = RedissonClient.getLock()获取锁
	lock.lock(设置过期时间)//设置阻塞锁
	lock.tryLock(尝试获取时间,过期时间)//非阻塞锁,在尝试获取时间内没有获取到锁,则return拿不到锁
	业务代码...
	lock.isHeldByCurrentThread()判断当前锁是否在用
	lock.unlock()//释放锁

加锁和删除锁如何保证同一线程:
	加锁和释放锁的不是同一线程。可以在Value中存入uuid，删除时进行验证。但是要注意验证锁和删除锁也不是一个原子性操作，可以用lua脚本使之成为原子性操作
获取锁时主节点宕机导致锁丢失
	问题
	redis集群下主节点宕机导致锁丢失。使用红锁解决
## 秒杀业务(场景介绍)
设计流程:
	1.秒杀的特点明确(指定时间限制,确认库存限制,不走购物车直接支付)
	2.秒杀业务流程问题(下单未支付多久超时,活动持续时间)
	3.秒杀表设计(秒杀商品表,秒杀订单状态表)
	4.设计秒杀方案(从db到redis)此处采用准点将商品db放到redis
	5.设计秒杀显示状态(根据审核状态,秒杀数,活动结束时间)
使用spring task完成定时任务作用(准点将数据从db储存到redis) @EnableScheduling写在启动项上
写定时任务@Scheduled并ioc到spring容器
流程:
![[Redisson分布式锁和SpringTask_image_1.jpg]]
redis存储格式:
大key:seckill_goods_2020080808
小key:goods_id
小value:商品信息的json
秒杀商品显示条件
1.审核状态必须是seckill_status=1
2.商品秒杀数必须seckill_count>0
3.活动没有结束 seckill_endtime> =now()
满足以上条件的数据（db）从db获取存储到Redis
秒杀业务结合IDEA
流程:
	1.获取正在开始以及即将开始一共五个时间区间
	通过写的DateUtil工具类
	List<Date> dateMenus =DateUtil.getDateMenus()
	2.根据时间区间获取秒杀商品
	dateMenus.for
	使用QueryWapper加上过滤条件过滤
	new QueryWrapper<Goods>();
	//审核状态、库存、开始时间>=时间区间的开始时间、结束时间<=时间区间开始时间+2小时
	3.根据对应格式放入redis
	stringRedisTemplate.boundHashOps
	4.前端联调
	5.下单,显示商品,等接口
	编写控制器层提供接口从redis中查找商品,下单
	下单流程思路:
	入参:商品id+对应时间区间的开始时间
	查找商品,判断库存>0
	生成订单--减库存--如果库存=0则清空redis商品记录并将数据库库存变0
	如果库存!=0则更新redis中商品
	6,微信支付系统统一下单,--支付成功通知并回调接口修改redis订单状态
	(或支付超时处理)