**Sleuth的作用**
	1.**定位问题具体位置**,sleuth提供了清晰的客户端和服务端调用链条时间表格,可以得出链条中每个服务的耗时，服务调用关系
**Sleuth的原理**
	1. 如果在web环境下运行时，spring boot会自动创建TracingFilter，该类创建是在TraceWebServletAutoConfiguration中完成的,TracingFilter实现了javax.servlet.Filter接口，spring将TracingFilter作为web过滤器设置到web容器中，这样TracingFilter会对所有的网络请求拦截。
	   在doFilter里面，创建Span对象时，如果是调用链的第一个服务，那么span id是一个long型的随机数，然后设置trace id=span id。如果不是第一个服务，则将header里面的trace id、span id、parent span id直接设置到新建的Span对象。
	 2. 在应用程序中生成唯一的Trace ID和Span ID。Trace ID是整个请求链的唯一标识符，而Span ID是单个请求的唯一标识符。
	 3. 当请求到达应用程序时，Sleuth会将Trace ID和Span ID注入请求头中。
	 4. 当请求通过应用程序时，Sleuth会自动记录请求的Trace ID和Span ID，并将它们传递到下一个服务。
	 5. 当请求结束时，Sleuth会将Trace ID和Span ID记录到日志中，以便开发人员可以查看请求的完整流程。
	![[Sleuth+Zipkin(链路监控)_image_1.png]]

Zipkin的作用:
	将sleuth获取到的数据收集并以图表形式保存并显示
Zipkin的架构
	ZipKin可以分为两部分，一部分是zipkin server，用来作为数据的采集存储、数据分析与展示；zipkin client是zipkin基于不同的语言及框架封装的一些列客户端工具，这些工具完成了追踪数据的生成与上报功能，架构如下
	![[Sleuth+Zipkin(链路监控)_image_2.png|350]]
	**Zipkin Collector**
		收集器组件
		接收或收集各应用传输的数据,这些信息转换为 Zipkin 内部处理的 Span 格式,以支持后续的存储、分析、展示等功能。
	**Storage**
		存储组件
		默认会将这些信息存储在**内存中**。我们也可以修改此存储策略,通过使用其他存储组件将跟踪信息存储到数据库中。
	**Zipkin Query Service**
		Zipkin 查询服务组件，
		负责查询Storage中存储的数据，提供简单的JSON API获取数据，主要提供给web UI使用它主要用来提供外部访问接口。比如给客户端展示跟踪信息,或是外接系统访问以实现监控等。
	**Web UI**
		提供简单的WEB 界面
		基于 API 组件实现的上层应用。
		注意：UI 中没有内置身份验证！
zipkin调用uml图
	![[Sleuth+Zipkin(链路监控)_image_3.jpg]]

Sleuth结合Zipkin(下图)
	![[Sleuth+Zipkin(链路监控)_image_4.jpg|375]]
	seluth示例数据(下图):
	![[Sleuth+Zipkin(链路监控)_image_5.jpg]]

IDEA整合sleuth和zipkin
	1.导入pom依赖(可以直接在父工程导入,因为大多数都需要集成该系统)
	spring-cloud-starter-sleuth
	spring-cloud-starter-zipkin
	2.下载zipkin的jar包(zipkin-server-2.12.9-exec.jar)
	3.编写zipkin本地启动脚本bat
	java -jar zipkin-server-2.12.9-exec.jar --server.port=9999
	pause
	4.项目properties配置
	spring.zipkin.base-url=\http://localhost:9999(#zipkin服务端地址是个大坑,前缀http://一定要加)
	spring.zipkin.discoveryClientEnabled=false(#禁止zipkin把自己当做nacos-client向nacos-server注册)
	spring.sleuth.sampler.rate=100(#sleuth抽样率默认是10%，设置为100表示100%请求上报zipkin)
	5.打开localhost:9999/zipkin 发送请求加载到zipkin页面
几个常见术语(span,trace,Annotation):
	Span：
		**基本工作单元**，例如，在一个新建的span中发送一个RPC等同于发送一个回应请求给RPC，span通过一个64位ID唯一标识，trace以另一个64位ID表示，span还有其他数据信息，比如摘要、时间戳事件、关键值注释(tags)、span的ID、以及进度ID(通常是IP地址) span在不断的启动和停止，同时记录了时间信息，当你创建了一个span，你必须在未来的某个时刻停止它。
	Trace：
		一系列spans组成的一个树状结构，例如，如果你正在跑一个分布式大数据工程，你可能需要创建一个trace。
	Annotation：
		用来及时记录一个事件的存在，一些核心annotations用来定义一个请求的开始和结束
		cs - Client Sent -客户端发起一个请求，这个annotion描述了这个span的开始
		sr - Server Received -服务端获得请求并准备开始处理它，如果将其sr减去cs时间戳便可得到网络延迟
		ss - Server Sent -注解表明请求处理的完成(当请求返回客户端)，如果ss减去sr时间戳便可得到服务端需要的处理请求时间
		cr - Client Received -表明span的结束，客户端成功接收到服务端的回复，如果cr减去cs时间戳便可得到客户端从服务端获取回复的所有所需时间 
	将Span和Trace在一个系统中使用Zipkin注解的过程图形化：

---
## Selth和Zipkin的数据持久化
持久化实现:
	数据异步同步放到ES搜索数据库中!
	或者放到mysql数据库

