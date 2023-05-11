Sentinel(与gateway整合后直接全局sentinel)
**Sentinel主要作用:**
	解决雪崩效应(也叫级联失效:指一个点失效导致关联的点无法运行也失效)
简介
	Sentinel 是一个中间件以qps或线程或异常或慢调用为切入点，从流量控制、熔断降级、系统负载保护等多个维度保护服务的稳定性。
	sentinel还可以实现对接口参数的限流(热点模式)
	sentinel还实现了接口权限的限制
sentinel分两个部分:
	**核心库（Java 客户端)** 不依赖任何框架/库，能够运行于所有 Java 运行时环境，同时对 Dubbo / Spring Cloud 等框架也有较好的支持。
	**控制台（Dashboard)** 基于 Spring Boot 开发，打包后可以直接运行，不需要额外的 Tomcat 等应用容器。能有实时监控功能看到500台一下机器秒级数据,同时提供spi扩展接口方便开发者自己编写特殊熔断场景需求
![[Sentinel(降级熔断)_image_1.jpg|600]]
Sentiel和Hystrix对比
	![[Sentinel(降级熔断)_image_2.jpg]]


Sentinel整合项目:
	1.下载sentinel控制台的jar包
	创建本地脚本bat文件输入java -jar sentinel-dashboard-1.8.0.jar --server.port=8888
	pause
	作用是启动控制台
	2.导入sentinel的pom依赖(需要被sentinel管控的项目)
	3.配置sentinel控制台交互端口号方便实时更新配置(熔断策略等)
	\#微服务与Sentinel控制台交互的端口，应用本地会起一个该端口占用的HttpServer
	\#默认是8719，如果8719被占用 +1，一直到没有被占用
	spring.cloud.sentinel.transport.port=8719
	spring.cloud.sentinel.transport.dashboard=127.0.0.1:8888
	4.调用接口后刷新控制台页面即可显示接口->进入粗点链路选项(列表视图)->有流控,降级,热点授权4个选项
sentinel限流(流控几种方式qps,线程)
服务的限流：访问量（QPS）比较大的情况下，拒绝超出服务能处理的请求
基于QPS或线程数的流控几种方式
QPS:Queries-per-second,每秒资源被访问的次数
线程模型:一个线程池取出一个线程供一次调用(调用完归还线程),同时有其他调用则再拉取一个线程
**流控模式分类(直接,关联,链路):**
	直接方式
		当被访问资源的QPS超过阈值，直接进行限流（限制访问），从而保护资源，
	关联方式
		设置一个关联路径,如果关联路径访问 QPS>指定值 则被流控的资源将会被限流
		例如:支付访问QPS超过指定值,则下单将会被限制访问
	链路方式:
		链路一：test1->/jifen/delete
		链路二：test2->jifen/delete
		当test1访问超过QPS设置的阈值 ，那么/jifen/delete只对链路一进行限制访问，而链路二，可以继续访问
**流控效果分类(快速失败,warm-up,排队等待)**
	**快速失败**(直接返回)
	![[Sentinel(降级熔断)_image_3.jpg|400]]
	**Warm-up方式**
		(该方式常用于某接口需要额外开销场景(例如每次调用需要创建数据库链接)
		即预热/冷启动方式。当系统长期处于低水位的情况下，当流量突然增加时，直接把系统拉升到高水位可能瞬间把系统压垮。通过"冷启动"，让通过的流量缓慢增加，在一定时间内逐渐增加到阈值上限，给冷系统一个预热的时间，避免冷系统被压垮
		默认 coldFactor 为 3，即请求 QPS 从 threshold / 3 开始，经预热时长逐渐升至设定的 QPS 阈值。
		例如:QPS=100 系统能承受QPS=20 则warmup (5s 21) 即qps由qps=21/3=7 在5s内慢慢变成qps=21
		然后再慢慢处理这100QPS
	**匀速排队方式:**
		(常用于峰值和低谷不清楚的情况)
		例如将1w个请求匀速处理(设置qps为5代表每秒处理5个匀速处理,设置超时时间为1000秒则总共处理5000条qps,剩下的5000则抛弃)
		设置QPS或者线程数阈值,和设置超时时间,当时间内没能达到阈值以下则直接丢弃
**Sentinel服务熔断降级(几种方式)**
![[Sentinel(降级熔断)_image_4.jpg|300]]
达到条件触发熔断->熔断开启->熔断时间结束->关闭熔断降级
熔断3种常用策略(慢调用比例,异常比例,异常数)
服务的熔断降级：服务远程调用过程中出现超时、服务本身在运行时出现异常，对服务进行熔断降级
慢调用比例方式:
设置最大RT(响应时间),如果一个请求超过该时间则将被定义为慢调用
最小请求数:每秒最小的请求书
熔断时长:断路器打开的时间，断路器打开的时候资源的主逻辑不能被调用，只能调用资源的备选逻辑
慢调用比例策略触发熔断的条件
比例阈值=慢调用请求数/总请求数
慢调用触发熔断条件
1.每秒请求数必须大于最小请求数
2.慢调用比例大于比例阈值
异常比例方式:
当单位统计时长（statIntervalMs）内请求数目大于设置的最小请求数目，并且异常的比例大于阈值，则接下来的熔断时长内请求会自动被熔断。
异常数方式:
异常数 (ERROR_COUNT)：当单位统计时长内的异常数目超过阈值且每秒的最小请求数大于指定阈值之后会自动进行熔断。
sentinel对参数的qps限流(热点限流)
基于qps次数方式进行限流
接口方法上加@sentinelResource(资源名)默认资源名为访问url
接口方法的参数上加上@RequestParam(value="XXX",required=false)
首先在接口上加入该注解定义资源名之后才能对参数进行限流
在控制台对指定接口添加热点限流后进入编辑
![[Sentinel(降级熔断)_image_5.jpg|400]]
参数索引默认写0,单机阈值表示该接口最多被访问10次
,统计窗口时长为单位时间(阈值/单位时间)同理qps限流
参数限流主要是在高级选项的参数例外项中选定参数类型
参数值(int为指定数字String为指定字符串) 设定限定阈值表示该参数最多被传入"指定阈值"次数
,与qps限流不同点在于针对传入指定参数次数进行限流同时也可以针对qps进行限流
参数限流和qps限流同时存在,但是参数限流优先级高于qps限流,如果参数限流阈值>qps阈值且传入该参数则按参数限流阈值进行限流,如果未传指定参数则按qps限流
Sentinel--权限规则(白名单,黑名单)
首先接口被调用模块中创建一个类implements RequestOriginParser然后注入spring容器(由sentinel提供内有方法需要实现作用是获取请求来源的身份信息与黑名单进行匹配)
重写parseOrigin方法,返回微服务的身份
return (String) request.getHeader("source");
然后接口调用模块的OpenFeign指定请求头
@RequestHeader("source") String source加入请求参数列表这样请求头中source被拦截解析对应黑名单判定是否放行
这样便可以在流控中根据服务身份定义黑名单和白名单
对于一些接口对应一些服务可以设置黑名单拒绝访问
sentinel限流中针对来源也是同理
sentinel如何定义熔断具体操作
(通过@SentinelResource)
可以自定义资源名(value)
@sentinelResource(value=资源名)默认资源名为访问url
只有在自定义资源名之后才能对指定的参数进行限流
可以自定义限流处理(blockHandler)
@SentinelResource(blockHandlerClass= CustomBlockHandler.class,blockHandler="testblockHandler")
在CustomBlockHandler类中写降级处理方法testblockHandler
必须传参(BlockException exception)
当触发限流后会去找指定的限流类中的限流方法,调用限流方法处理
注意:在其他类定义限流方法,方法上必须加static
可以自定义熔断降级处理(fallback)
@SentinelResource(fallback = "testeFallback",
fallbackClass = CustomerFallback.class)
同理自定义限流处理,且方法后最好加上throws Exception
必须传参(Throwable throwable)
sentniel系统规则(针对系统整体)
有Load自适应(linux使用设定参考值为cpu核心数*2.5)
cpu使用率(设置机器cpu使用率阈值)
平均RT(设置单台机器所有入口流量的平均RT阈值,单位毫秒)
qps(设置单台机器所有入口流量并发qps的阈值)
并发线程数(设置单台机器所有入口流量并发线程数的阈值)
sentinel底层如何实现的限流和熔断操作(调用已经写好的transport包内的方法去流控)
![[Sentinel(降级熔断)_image_6.jpg]]
spring.cloud.sentinel.transport.port=8719(相当于有1套额外的server项目,端口8719作用就是工具类注册到sentinel控制台,然后去实现控制台操作的限流和熔断等操作 每个服务都需要注册一个transport-simple才能实现限流)
spring.cloud.sentinel.transport.dashboard=127.0.0.1:8888(控制台端口号)
这个图表示如果在控制台修改后,会发送api请求到transport-simple,然后调用transport-simple的底层api实现限流或熔断的功能
Sentinel持久化方案(重要,将限流和熔断等规则放到nacos或本地,不会因为项目重启而丢失)
生产直接用现成的ASHS(阿里云提供的付费产品)
默认模式
![[Sentinel(降级熔断)_image_7.jpg]]
(图表示sentinel默认情况是通过transport里的api将规则直接推到内存中)
Push模式(生产环境常用)
![[Sentinel(降级熔断)_image_8.jpg]]
对于 push 模式的数据源,如远程配置中心（ZooKeeper, Nacos, Apollo等等），推送的操作不应由 Sentinel 客户端进行，而应该经控制台统一进行管理，直接进行推送，数据源仅负责获取配置中心推送的配置并更新到本地。因此推送规则正确做法应该是 配置中心控制台/Sentinel 控制台 → 配置中心(nacos) → Sentinel 数据源 → Sentinel，而不是经 Sentinel 数据源推送至配置中心。(但是sentinel底层源码还是通过dashborad推送到sentinel客户端而不是nacos配置中心,所以需要修改sentinel源码)：
第一步:POM依赖
spring-cloud-starter-alibaba-sentinel
sentinel-datasource-nacos
第二步:添加配置
# sentinel dashboard 地址
spring.cloud.sentinel.transport.dashboard=localhost:8888
# nacos 地址
spring.cloud.sentinel.datasource.ds.nacos.server-addr=localhost:8848
spring.cloud.sentinel.datasource.ds.nacos.dataId=wfx-jifen-flow-rule
spring.cloud.sentinel.datasource.ds.nacos.groupId=DEFAULT_GROUP
spring.cloud.sentinel.datasource.ds.nacos.namespace=4cb9d569-7e5d-4d5f-bb0c-2079a0fc93ef
spring.cloud.sentinel.datasource.ds.nacos.data-type=json
# 规则类型
spring.cloud.sentinel.datasource.ds.nacos.rule-type=flow
第三步:Nacos创建限流规则(如果修改了源码则应该由dashborad生产到nacos不需要该步骤)
[
  {
       "resource": "/hello",
       "limitApp": "default",
       "grade": 1,
       "count": 2,
       "strategy": 0,
       "controlBehavior": 0,
       "clusterMode": false
  }
]
-   resource：资源名
-   limitApp：流控针对的调用来源，default不区分来源
-   grade：限流阈值类型(0-根据并发数量来限流 1-根据QPS来进行流量控制)
-   count：限流阈值
-   strategy：调用关系限流策略
-   controlBehavior：流量控制效果(直接拒绝、WarmUP、匀速排队)
-   clusterMode：是否集群模式
pull模式(不常用)
![[Sentinel(降级熔断)_image_9.jpg]]
底层逻辑:
FileRefreshableDataSource 定时从指定文件中读取规则JSON文件【图中的本地文件】，如果发现文件发生变化，就更新规则缓存。
FileWritableDataSource 接收控制台规则推送，并根据配置，修改规则JSON文件【图中的本地文件】
1.pom依赖
sentinel-datasource-extension
2.写java放到config包(官方有提供储,存逻辑代码)
3.配置(resources/META-INF/services 目录下创建文件)
com.alibaba.csp.sentinel.init.InitFunc
# 改成上面FileDataSourceInit的包名类名全路径即可。
com.wfx.config.FileDataSourceInit
-   优点
-   简单易懂
-   没有多余依赖（比如配置中心、缓存等）
-   缺点
-   由于规则是用 FileRefreshableDataSource 定时更新的，所以规则更新会有延迟。如果FileRefreshableDataSource定时时间过大，可能长时间延迟；如果FileRefreshableDataSource过小，又会影响性能；
-   规则存储在本地文件，如果有一天需要迁移微服务，那么需要把规则文件一起迁移，否则规则会丢失。
Gateway整合sentinel
Sentinel从 1.6.0 版本开始提供了 Spring Cloud Gateway 的适配模块，可以提供两种资源维度的限流：
-   route 维度：即在 Spring 配置文件中配置的路由条目，资源名为对应的 routeId自定义
-   API 维度：用户可以利用 Sentinel 提供的 API 来自定义一些 API 分组
配置sentinel端口和dashboard页面的ip及端口
Sentinel DashBoard 作用控制台:查看资源的运行状况 定制限流,熔断规则
自定义异常处理(@SentinelResource)
复制SentinelGatewayBlockExceptionHandler的全部
主要重写 writeResponse()//返回响应json数据
自定义的配置
GatewayConfiguration
完成Sentinel整合后就可以让其他项目不需要整合Sentinel,直接全局Sentinel