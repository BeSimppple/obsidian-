springcloud-gateway(路由,权限,限流,监控)

gateway核心是过滤器链

pom依赖中不能有web包(底层tomcat与gateway的netty有冲突)和actuator

如果配置了sentinel等内容注意冲突(可能导致网关传递被sentinel中的拦截器或fallback拦截)

![[Gateway_image_1.jpg]]

为什么需要网关,网关怎么解决这些问题?

1.没有统一入口,需要交给前端地址去调用

2.权限不能统一控制(认证和授权)

3.对外(前端)会暴露协议接口

gateway作用是路由解决统一入口做一个请求转发,过滤器做一个权限认证,路由还可以隐藏实际地址

springcloud-gateway简介:

1：springcloud-gateway是第二代网关，取代zuul(对比下性能更强,且能完成转发,监控和限流)

2：基於Netty、Reactor、及webflux构建(因为网关是唯一入口访问量巨大,必须用高级服务器搭建)

3：是微服务的统一入口

gateway工作原理:

![[Gateway_image_2.jpg]]

1:客户端向 Spring Cloud Gateway 发出请求。然后在 Gateway Handler Mapping 中找到与请求相匹配的路由

2:将其发送到 Gateway Web Handler。

3:Handler 再通过指定的过滤器链(此处执行pre业务逻辑)来将请求发送到我们实际的服务执行业务逻辑，然后返回(此处执行post业务逻辑)。

过滤器之间用虚线分开是因为过滤器可能会在发送代理请求之前（“pre”）或之后（“post”）执行业务逻辑。

Gateway的功能作用(限流,权限,路由,过滤,监控)

![[Gateway_image_3.jpg]]

网关的核心功能:路由routes(route,prediacte,filter)

spring:

cloud:

gateway:

routes:

- id: baidu

uri: http://www.baidu.com

predicates:

- Path=/**

-   Route（做一个地址跳转功能）

网关的基本模块，它由ID，目标URI，一系列的断言和过滤器组成，如果断言为true则匹配该路由

Predicate（类似nginx的proxy_pass匹配地址）

开发人员可以匹配HTTP请求中的所有内容（例如请求头或请求参数），如果请求与断言相匹配则进行路由

-   Filter（过滤）

指的是Spring框架中GatewayFilter的实例，使用过滤器，可以在请求被路由前或者之后对请求进行修改

  

注意:路由匹配是从上到下匹配,如果上面匹配了则不走下面

路由最后跳转的地址底层如何拼接:

![[Gateway_image_4.jpg]]

静态路由:固定地址

动态路由:lb://项目名 (由nacos负责转发)

Gateway访问模块(集群)的负载均衡

网关访问集群服务的负载均衡是内置的轮循负载均衡,而不同模块之间访问资源的负载均衡是由Ribbon完成的

如果目标地址是个集群则uri: lb://(nacos中微服务名称)

lb意思(loadblance)

Gateway谓词工厂

![[Gateway_image_5.jpg]]

- Cookie=name,jack

- Header=token

- Host=**.wfx.com,**.jd.com

- Query=baz

- RemoteAddr=192.168.234.122

其中Cookie和Head谓词就是security单点登录类似功能将需要认证的标识放到head或cookie中(主要放到head中存一个token认证)详见security

cookie谓词是按顺序排列kv形式

host作用是辅助url定位(项目后期可能一个大项目需要细分定位一般vue前端请求会传送host)

自定义谓词RoutePredicateFactory

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

Gateway的过滤器(主要用自定义)

Gateway内置过滤器:

1 AddRequestHeader GatewayFilter Factory 2 AddRequestParameter GatewayFilter Factory 3 AddResponseHeader GatewayFilter Factory 4 DedupeResponseHeader GatewayFilter Factory 5 Hystrix GatewayFilter Factory 6 FallbackHeaders GatewayFilter Factory 7 PrefixPath GatewayFilter Factory 8 PreserveHostHeader GatewayFilter Factory 9 RequestRateLimiter GatewayFilter Factory 10 RedirectTo GatewayFilter Factory 11 RemoveHopByHopHeadersFilter GatewayFilter Factory 12 RemoveRequestHeader GatewayFilter Factory 13 RemoveResponseHeader GatewayFilter Factory 14 RewritePath GatewayFilter Factory 15 RewriteResponseHeader GatewayFilter Factory 16 SaveSession GatewayFilter Factory 17 SecureHeaders GatewayFilter Factory 18 SetPath GatewayFilter Factory 19 SetResponseHeader GatewayFilter Factory 20 SetStatus GatewayFilter Factory 21 StripPrefix GatewayFilter Factory 22 Retry GatewayFilter Factory 23 RequestSize GatewayFilter Factory 24 Modify Request Body GatewayFilter Factory 25 Modify Response Body GatewayFilter Factory 26 Default Filters

自定义过滤器 GatewayFilterFactory(大致等同谓词自定义)

命名规范：过滤器工厂的类名必须以GatewayFilterFactory为后缀 同理谓词最后使用也是XXXGatewayFilterFactory

配置yaml XXX=???

实现方法public GatewayFilter apply(NameValueConfig config) return匿名内部类实现其中pre逻辑和post逻辑

实现方法ShortcutType shortcutFieldOrder()

return ShortcutType.XXX(内置枚举类)

同理谓词切割逻辑方法

过滤器主要功能可以实现在pre请求逻辑时候操作,也可以在post返回逻辑时候操作

过滤器执行顺序

过滤器执行的顺序，就是配置的顺序

局部处理器顺序比全局要高

全局通过@Order(数字)排序

Gateway全局过滤器

Spring Cloud Gateway内置的全局过滤器。包括： 1 Combined Global Filter and GatewayFilter Ordering 2 Forward Routing Filter 3 LoadBalancerClient Filter 4 Netty Routing Filter 5 Netty Write Response Filter 6 RouteToRequestUrl Filter 7 Websocket Routing Filter 8 Gateway Metrics Filter 9 Marking An Exchange As Routed

注意:过滤器练使用@bean注入到spring容器,使用@Order(数字)决定执行顺序,数字越小越先执行

Gateway整合sentinel

Sentinel从1.6.0版本能适配 Spring Cloud Gateway adapter

通过route 维度限流：即在 Spring 配置文件中配置的路由条目，资源名为对应的 routeId自定义

通过API接口 维度限流：用户可以利用 Sentinel 提供的 API 来自定义一些 API 分组

具体实现(同理单体sentinel):

1.导入sentinel的pom依赖

spring-cloud-starter-alibaba-sentinel

sentinel-spring-cloud-gateway-adapter

2.配置sentinel端口(dashborad和transport)和dashborad的ip

3.自定义限流与异常处理(GatewayConfiguration类)

限流:其中sentinelGatewayFilter方法,通过过滤器获取qps或等等来处理限流操作

异常处理:其中sentinelGatewayBlockExceptionHandler方法返回的

SentinelGatewayBlockExceptionHandler类定义了异常的处理方式->主要重写 writeResponse()方法//返回响应json数据

String jsonString = JSON.toJSONString(data);

DataBuffer buffer = response1.bufferFactory().wrap(jsonString.getBytes());

return response1.writeWith(Mono.just(buffer));(将字节数据转换输出出去)

最后修改GatewayConfiguration类

操作方式->因为该类多为私有方法,所以复制一份进行重写后覆盖原版(通过修改GatewayConfiguration类的sentinelGatewayBlockExceptionHandler方法,将返回值和返回类型改成我们自己定义的MySentinelGatewayBlockExceptionHandler)

方法上@Order(Ordered.HIGHEST_PRECEDENCE)最高级别过滤器最先定义

  

Gateway完成Sentinel整合后就可以让其他项目不需要整合Sentinel,全局通过gateway访问跳转来实现全局sentinel限流

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