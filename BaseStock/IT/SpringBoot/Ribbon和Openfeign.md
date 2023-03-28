Ribbon

Ribbon是Netflix公司开源的一个负载均衡的项目，是一个"客户端"负载均衡器，运行在客户端上 Ribbon是一个客户端层的负载均衡(客户端和服务端是一个发出和接受的关系,谁是发出方就是客户端)

例如:nginx就是'服务端'的负载均衡

什么是RestTemplate?(发送请求的工具类)

1:RestTemplate是java模拟浏览器发送http请求的工具类

2:RestTemplate基于`Apache`的`HttpClient`实现。HttpClient使用起来太过繁琐。spring提供了一种简单便捷的模板类来进行操作，这就是`RestTemplate`。

但是开发环境一般使用OpenFeign

![[Ribbon和Openfeign_image_1.jpg]]

  

RestTemplate的使用:

1.导入起步依赖(nacos-discovery包下就兼容了ribbon)

2.@LoadBalanced注解

在启动类中ioc RestTemplate @LoadTemplate注解配置负载均衡

@Bean

@LoadBalanced

public RestTemplate restTemplate(){

  

return new RestTemplate();

}

  

Ribbon工作原理:

第一步:Ribbon拦截服务器消费者 发送的所有请求

第二步:拦截到请求后,从URI中获取 server name

(源码中LoadBalancerAutoConfiguration中写了拦截器实例LoadBalancerInterceptor实现方法interceptor拦截方法)

第三步:Ribbon从Nacos server端根据 server name获取服务列表(XXX方法不用细追-可以查看源码但没必要)

第四步: Ribbon采用相应的Ruler(负载均衡策略)实现负载均衡,获得一个服务实例

第五步: 将服务实例的ip和端口交给RestTemplate或者OpenFeign远程调用

  

Ribbon的负载均衡策略自定义:

//注意这个配置类必须放在@ComponentScan默认扫描的范围外(写在根包内还会采用默认的)

@Configuration

public class MyRule {

@Bean

public IRule getRule(){

return new RoundRobinRule();

}

}

然后再启动类上加@RibbonClient(name="当前调用应用名",configruation="自定义均衡类")注解

OpenFeign(作用就是发送http请求)

Spring Cloud OpenFeign 是一种声明式、模板化的 HTTP 客户端 和 RestTemplate一样是远程调用,但是书写模式像是调用Service层

比起restTemplate隐藏了书写http形式更好看

导入spring-cloud-starts-openfigen依赖即可用(包内包含ribbon)

使用方法:

@EnableFeignClients开启feign客户端

请求客户端:使用OpenFeign书写接口类远程调用的接口声明(不写具体实现)然后在类上加上@FeignClient(服务端项目名)

接口方法上加上@RequestMapping(接口地址)

如果接口方法中传参必须写好注解(类是@RequestBody,基础类型是@RequestParam(XX),路径带参数{}则是@PathParam)

最后效果就是发送http请求:http://项目名/接口地址

请求服务端:接口实现类的提供者

  

1.请求客户端 配置起步依赖openFeign //这个包中已经包括了Ribbon

2.在 请求客户端 启动类上加注解@EnableFeignClients /开启feign客户端,同时让jvm系统不去找申明接口的实现化方法

3.在 请求客户端的 接口声明类上声明@FeignClient("提供者的server name")

4.在请求客户端 Controller层注入 接口声明类

5.可以直接接口类.方法调用(OpenFeign实际远程调用)提供者的接口实现方法

注意:接口声明类调用方法的参数 每个都需要添加注解

openFeign配合Security

客户端重写一个拦截器配置类@Configuration实现implements RequestInterceptor 重写apply方法获取request并添加token

从而实现每个远程调用接口都被拦截自动在请求头中放入token

服务端想拿到token可以自定义类implements RequestOriginParser重写parseOrigin(HttpServletRequest request)方法

或直接在自己接口中加入HttpServletRequest req 自己获取

  

范例:

接口声明类:

@Component

@FeignClient("wfx-jifen")

public interface IJifenBiz {

@PostMapping(value = "/jifen/update")

public Map update(@RequestBody Jifen jifen);

}

接口远程调用:

@Autowired

   private IJifenBiz iJifenBiz;

@PostMapping("update")

public ResultVo update(@RequestBody Order order){

//修改订单

System.out.println("修改订单成功");

//修改积分

Jifen jifen = new Jifen(order.getUid(),12,"修改积分");

iJifenBiz.update(jifen);

return new ResultVo(true,"save success");

}

  

OpenFeign的properties设置

//超时设置

#全局配置

# 请求连接的超时时间 默认的时间为 1 秒

ribbon.ConnectTimeout=5000

# 请求处理的超时时间 默认的时间为 1 秒

ribbon.ReadTimeout=5000