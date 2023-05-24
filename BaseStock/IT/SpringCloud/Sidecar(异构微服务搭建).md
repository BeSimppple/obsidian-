**作用和原理**
	**作用**: 将非JVM的应用纳入SpringCloud的体系,Sidecar就是**异构服务的一个壳子**，来使得异构服务能够接入Spring Cloud的生态圈
	**原理**:
		 1. **Sidecar代理可以使用HTTP或TCP协议与其他微服务进行通信，并将请求转发给非JVM应用程序**。
		 2. 在Spring Cloud体系中配置Sidecar代理 。在Spring Cloud Config Server中，需要添加非JVM应用程序的配置信息，并在Eureka Server中注册非JVM应用程序的实例并在Config中配置信息。在Spring Cloud Gateway或Zuul中，需要配置路由规则，将请求转发给非JVM应用程序的实例。
	**优点:**
		1.   接入简单，几行代码就可以将异构微服务整合到Spring Cloud生态
		2.   不侵入原代码
	**缺点:**
		1. 每接入一个异构微服务实例，都需要额外部署一个Alibaba Sidecar实例，增加了部署成本（虽然这个成本在Kubernetes环境中几乎可以忽略不计（只需将Alibaba Sidecar实例和异构微服务作为一个Pod部署即可））；
		2. 异构微服务调用Spring Cloud微服务时，本质是把Alibaba Sidecar当网关在使用，经过了一层转发，性能有一定下降。
**Service Mesh介绍**
	新一代微服务概念:
	ServiceMesh理论完美,但是实际处理并不完善,是一个非入侵式的黑盒系统(屏蔽分布式系统通信的复杂性(负载均衡、服务发现、认证授权、监控追踪、流量控制等等)，服务只用关注业务逻辑)
	**优点**:
		1.   真正的语言无关，服务可以用任何语言编写，只需和Service Mesh通信即可；
		2.   对应用透明，Service Mesh组件可以单独升级；
	**缺点**:
		1.   ice Mesh组件以代理模式计算并转发请求，一定程度上会降低通信系统性能，并增加系统资源开销；
		2.   Service Mesh组件接管了网络流量，因此服务的整体稳定性依赖于Service Mesh，同时额外引入的大量Servi
		3. 引入一个新的黑盒系统导致的错误排查问题并不完善
**SpringCloud和Service Mesh的区别?**
	**第二代微服务(代表:SpringCloud)**
	![[Sidecar(异构微服务搭建)_image_1.png]]
	**Service Mesh架构(代表:istio)**
	**核心:** 一个非入侵式的黑盒系统(屏蔽分布式系统通信的复杂性(负载均衡、服务发现、认证授权、监控追踪、流量控制等等)，服务只用关注业务逻辑)
	![[Sidecar(异构微服务搭建)_image_2.png]]
	**主要区别:**
		  1. 其一，开发框架通常只支持一种或几种特定的语言，回过头来看文章最开始对微服务的定义，一个重要的特性就是语言无关，但那些没有框架支持的语言编写的服务，很难融入面向微服务的架构体系，想因地制宜的用多种语言实现架构体系中的不同模块也很难做到；
		  2. 其二，框架以lib库的形式和服务联编，复杂项目依赖时的库版本兼容问题非常棘手，同时，框架库的升级也无法对服务透明，服务会因为和业务无关的lib库升级而被迫升级；
Sidecar和Service Mesh的区别
	  1. 目前Mesh主要使用场景在Kubernetes领域（Istio、Linkerd 2等，大多将Kubernetes作为First Class支持，虽然Istio也可部署在非Kubernetes环境），
	  2. 使用Alibaba Sidecar一个小组件就能解决问题了（核心代码不超过200行），引入整套Mesh方案，颇有点屠龙刀杀黄鳝的意思。


Idea整合Sidecar
	使用alibaba-sideacar   因为netfilex的只支持zuul
	1. **导入依赖**
		1. \<dependency> 
		2. \<groupId>com.alibaba.cloud\</groupId>
		3. \<artifactId>spring-cloud-starter-alibaba-sidecar\</artifactId> 
		4. \</dependency>
	2. **在config中写yaml配置**
		sidecar:
		  # 异构微服务的IP
		  ip: 127.0.0.1
		  # 异构微服务的端口
		  port: 8060
		  # 异构微服务的健康检查URL
		  health-check-url: \http://localhost:8060/health.json
		management:
		endpoint:
		    health:
		      show-details: always
	3. **启动类上添加@EnableSidecar注解**
	**检验:**/http://localhost:8060/health   查看返回是否为UP













