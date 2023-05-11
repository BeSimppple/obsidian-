springcloud-gateway简介:
	1：springcloud-gateway是第二代网关，取代zuul(对比下性能更强,且能完成转发,监控和限流)
	2：基於Netty、Reactor、及webflux构建(因为网关是唯一入口访问量巨大,必须用高级服务器搭建)
	3：是微服务的统一入口
**SpringCloud-Gateway的原理和作用**
	gateway核心是过滤器链
	**gateway工作原理:**
		原理是通过Spring WebFlux提供的反应式编程模型，将请求转发到不同的微服务实例中，同时可以对请求进行拦截、处理和转换，最终将响应返回给客户端
		![[Gateway(网关)_image_1.jpg|400]]
		1:客户端向 Spring Cloud Gateway 发出请求。然后在 Gateway Handler Mapping 中找到与请求相匹配的路由
		2:将其发送到 Gateway Web Handler。
		3:Handler 再通过指定的过滤器链(此处执行pre业务逻辑)来将请求发送到我们实际的服务执行业务逻辑，然后返回(此处执行post业务逻辑)。
		过滤器之间用虚线分开是因为过滤器可能会在发送代理请求之前（“pre”）或之后（“post”）执行业务逻辑。
	**Gateway作用:**
		(路由,权限,限流,监控)
		1.统一入口,需要交给前端地址去调用
		2.@hasAnyAuthority权限统一控制(认证和授权)
		3.对外(前端)隐藏协议接口
		gateway原理是路由解决统一入口做一个请求转发,过滤器做一个权限认证,路由还可以隐藏实际地址
	![[Gateway(网关)_image_2.jpg|500]]

**Gateway的功能作用实现
	![[Gateway(网关)_image_3.jpg|600]]
	**yaml写法**
		spring:
		  cloud:
		    gateway:
		      routes:
		      \- id: baidu
		         uri: http://www.baidu.com
		         predicates:
		         - Path=/**
	**Route关键字（路由,做一个地址跳转功能）**
		网关的基本模块，它由ID，目标URI，一系列的断言和过滤器组成，如果断言为true则匹配该路由
	**Predicate关键字（谓语,类似nginx的proxy_pass匹配地址）**
		开发人员可以匹配HTTP请求中的所有内容（例如请求头或请求参数），如果请求与断言相匹配则进行路由
	**Filter关键字（过滤）**
		指的是Spring框架中GatewayFilter的实例，使用过滤器，可以在请求被路由前或者之后对请求进行修改
		注意:路由匹配是从上到下匹配,如果上面匹配了则不走下面
		路由最后跳转的地址底层如何拼接:
		![[Gateway(网关)_image_4.jpg|400]]
		**静态路由:** 固定地址
		**动态路由:** lb://项目名 (由nacos负责转发,lb意思loadblance)
		Gateway访问模块(集群)的负载均衡
		网关访问集群服务的负载均衡是内置的轮循负载均衡,而不同模块之间访问资源的负载均衡是由Ribbon完成的
		如果目标地址是个集群则uri: lb://(nacos中微服务名称)
	限流通过整合Sentinel实现
	权限通过SpringSecurity+Oauth2的@hasAnyAuthority实现
	整合SpringBoot-actuator实现监控
**Gateway谓词工厂**
	![[Gateway(网关)_image_5.jpg]]
	- Cookie=name,jack
	- Header=token
	- Host=**.wfx.com,**.jd.com
	- Query=baz
	- RemoteAddr=192.168.234.122
	其中Cookie和Head谓词就是security单点登录类似功能将需要认证的标识放到head或cookie中(主要放到head中存一个token认证)详见security
	cookie谓词是按顺序排列kv形式
	host作用是辅助url定位(项目后期可能一个大项目需要细分定位一般vue前端请求会传送host)
	**自定义谓词RoutePredicateFactory**
		首先extends AbstractRoutePredicateFactory<变量>
		命名规范:后缀必须是RoutePredicateFactory<变量>
		例如:XXXRoutePredicateFactory
		实现方法apply(对象 config)一般返回boolean值确定能否通行
		serverWebExchange.getRequest().getHeaders().get(config.getName());获取变量名
		if(!tokenValue.equals(config.getId())){return false;}判断参数是否正确
		实现方法shortcutFieldOrder() --告诉谓词传入的参数怎么分割 不能为null
		可以实现网关时获取请求头(token等)与redis的token进行比对
		(实现Springsecurity令牌的效验)
		注入spring容器后,到properties的predicate中写XXX(XXX为XXXRoutePredicateFactory的前缀)=???

---
## GateWay过滤器
过滤器执行顺序
	1.过滤器执行的顺序，就是配置的顺序
	2.局部处理器顺序比全局要高
	3.全局通过@Order(数字越小越先执行)排序
Gateway内置过滤器工厂:
	| 过滤器工厂                  | 作用                                                                                          | 参数                                                               |
	| --------------------------- | --------------------------------------------------------------------------------------------- | ------------------------------------------------------------------ |
	| AddRequestHeader            | 为原始请求添加Header                                                                          | Header的名称及值                                                   |
	| AddRequestParameter         | 为原始请求添加请求参数                                                                        | 参数名称及值                                                       |
	| AddResponseHeader           | 为原始响应添加Header                                                                          | Header的名称及值                                                   |
	| DedupeResponseHeader        | 剔除响应头中重复的值                                                                          | 需要去重的Header名称及去重策略                                     |
	| Hystrix                     | 为路由引入Hystrix的断路器保护                                                                 | HystrixCommand的名称                                               |
	| FallbackHeaders             | 为fallbackUri的请求头中添加具体的异常信息                                                     | Header的名称                                                       |
	| PrefixPath                  | 为原始请求路径添加前缀                                                                        | 前缀路径                                                           |
	| PreserveHostHeader          | 为请求添加一个preserveHostHeader=true的属性，路由过滤器会检查该属性以决定是否要发送原始的Host | 无                                                                 |
	| RequestRateLimiter          | 用于对请求限流，限流算法为令牌桶                                                              | keyResolver、rateLimiter、statusCode、denyEmptyKey、emptyKeyStatus |
	| RedirectTo                  | 将原始请求重定向到指定的URL                                                                   | http状态码及重定向的url                                            |
	| RemoveHopByHopHeadersFilter | 为原始请求删除IETF组织规定的一系列Header                                                      | 默认就会启用，可以通过配置指定仅删除哪些Header                     |
	| RemoveRequestHeader         | 为原始请求删除某个Header                                                                      | Header名称                                                         |
	| RemoveResponseHeader        | 为原始响应删除某个Header                                                                      | Header名称                                                         |
	| RewritePath                 | 重写原始的请求路径                                                                            | 原始路径正则表达式以及重写后路径的正则表达式                       |
	| RewriteResponseHeader       | 重写原始响应中的某个Header                                                                    | Header名称，值的正则表达式，重写后的值                             |
	| SaveSession                 | 在转发请求之前，强制执行WebSession::save操作                                                  | 无                                                                 |
	| secureHeaders               | 为原始响应添加一系列起安全作用的响应头                                                        | 无，支持修改这些安全响应头的值                                     |
	| SetPath                     | 修改原始的请求路径                                                                            | 修改后的路径                                                       |
	| SetResponseHeader           | 修改原始响应中某个Header的值                                                                  | Header名称，修改后的值                                             |
	| SetStatus                   | 修改原始响应的状态码                                                                          | HTTP 状态码，可以是数字，也可以是字符串                            |
	| StripPrefix                 | 用于截断原始请求的路径                                                                        | 使用数字表示要截断的路径的数量                                     |
	| Retry                       | 针对不同的响应进行重试                                                                        | retries、statuses、methods、series                                 |
	| RequestSize                 | 设置允许接收最大请求包的大小。如果请求包大小超过设置的值，则返回 413 Payload Too Large        | 请求包大小，单位为字节，默认值为5M                                 |
	| ModifyRequestBody           | 在转发请求之前修改原始请求体内容                                                              | 修改后的请求体内容                                                 |
	| ModifyResponseBody          | 修改原始响应体的内容                                                                          | 修改后的响应体内容                                                 |
	| Default                     | 为所有路由添加过滤器                                                                          | 过滤器工厂名称及值                                                 |
**自定义过滤器 GatewayFilterFactory(常用)**
	过滤器通过@Bean注入Spring容器,其他大致等同谓词自定义
	命名规范：过滤器工厂的类名必须以GatewayFilterFactory为后缀 同理谓词最后使用也是XXXGatewayFilterFactory
	配置yaml XXX=???
	实现方法public GatewayFilter apply(NameValueConfig config) return匿名内部类实现其中pre逻辑和post逻辑
	实现方法ShortcutType shortcutFieldOrder()
	return ShortcutType.XXX(内置枚举类)
	同理谓词切割逻辑方法
	过滤器主要功能可以实现在pre请求逻辑时候操作,也可以在post返回逻辑时候操作
Spring Cloud Gateway内置的全局过滤器。
	1 Combined Global Filter and GatewayFilter Ordering 
	2 Forward Routing Filter 
	3 LoadBalancerClient Filter 
	4 Netty Routing Filter 
	5 Netty Write Response Filter 
	6 RouteToRequestUrl Filter 
	7 Websocket Routing Filter 
	8 Gateway Metrics Filter 
	9 Marking An Exchange As Routed

---

Gateway整合sentinel
	Gateway完成Sentinel整合后就可以让其他项目不需要整合Sentinel,全局通过gateway访问跳转来实现全局sentinel限流
	Sentinel从1.6.0版本能适配 Spring Cloud Gateway adapter
	通过route 维度限流：即在 Spring 配置文件中配置的路由条目，资源名为对应的 routeId自定义
	通过API接口 维度限流：用户可以利用 Sentinel 提供的 API 来自定义一些 API 分组
	具体实现(同理单体sentinel):
	1.导入sentinel的pom依赖
	spring-cloud-starter-alibaba-sentinel
	sentinel-spring-cloud-gateway-adapter
	2.配置sentinel端口(dashborad和transport)和dashborad的ip
	3.自定义限流与异常处理(GatewayConfiguration类)
	**限流:** 其中sentinelGatewayFilter方法,通过过滤器获取qps或等等来处理限流操作
	**异常处理:** 其中sentinelGatewayBlockExceptionHandler方法返回的
	SentinelGatewayBlockExceptionHandler类定义了异常的处理方式->主要重写 writeResponse()方法//返回响应json数据
	String jsonString = JSON.toJSONString(data);
	DataBuffer buffer = response1.bufferFactory().wrap(jsonString.getBytes());
	return response1.writeWith(Mono.just(buffer));(将字节数据转换输出出去)
	最后修改GatewayConfiguration类
	操作方式->因为该类多为私有方法,所以复制一份进行重写后覆盖原版(通过修改GatewayConfiguration类的sentinelGatewayBlockExceptionHandler方法,将返回值和返回类型改成我们自己定义的MySentinelGatewayBlockExceptionHandler)
	方法上@Order(Ordered.HIGHEST_PRECEDENCE)最高级别过滤器最先定义
gateway跨域处理:
	由于gateway使用的是webflux，而不是springmvc，所以需要先关闭webflux的cors，再从gateway的filter里边设置cors就行了。config.addAllowedOrigin()设置允许访问的域名
	新建corsConfig类@Configuration
	@Bean
	public CorsWebFilter corsFilter() {
	CorsConfiguration config = new CorsConfiguration();
	config.addAllowedMethod("*");
	config.addAllowedOrigin("http://localhost:9528");
	config.addAllowedHeader("*");
	UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource(new PathPatternParser());
	source.registerCorsConfiguration("/**", config);
	return new CorsWebFilter(source);
	}

注意:
	pom依赖中不能有web包(底层tomcat与gateway的netty有冲突)和actuator
	如果配置了sentinel等内容注意冲突(可能导致网关传递被sentinel中的拦截器或fallback拦截)





