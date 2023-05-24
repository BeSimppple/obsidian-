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


Idea整合Sidecar
	使用alibaba-sideacar   因为netfilex的只支持zuul
	1. 导入依赖
		1. \<dependency> 
		2. \<groupId>com.alibaba.cloud\</groupId>
		3. \<artifactId>spring-cloud-starter-alibaba-sidecar\</artifactId> 
		4. \</dependency>
	2. 在config中写yaml配置
		sidecar:
		  # 异构微服务的IP
		  ip: 127.0.0.1
		  # 异构微服务的端口
		  port: 8060
		  # 异构微服务的健康检查URL
		  health-check-url: http://localhost:8060/health.json
		management:
		endpoint:
		    health:
		      show-details: always
	3. 启动类上添加@EnableSidecar注解













