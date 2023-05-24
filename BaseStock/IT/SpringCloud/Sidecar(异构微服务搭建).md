**作用和原理**
	**作用**: 将非JVM的应用纳入SpringCloud的体系,Sidecar就是**异构服务的一个壳子**，来使得异构服务能够接入Spring Cloud的生态圈
	**原理**:
		 1. 在非JVM应用程序中添加Sidecar代理。**Sidecar代理可以使用HTTP或TCP协议与其他微服务进行通信，并将请求转发给非JVM应用程序**。
		 2. 在Spring Cloud体系中配置Sidecar代理 。在Spring Cloud Config Server中，需要添加非JVM应用程序的配置信息，并在Eureka Server中注册非JVM应用程序的实例并在Config中配置信息。在Spring Cloud Gateway或Zuul中，需要配置路由规则，将请求转发给非JVM应用程序的实例。

Idea整合Sidecar
	1. 导入依赖
		1. \<dependency> 
		2. \<groupId>com.alibaba.cloud\</groupId> <artifactId>spring-cloud-starter-alibaba-sidecar</artifactId> \</dependency>













