SpringCloud(是一种思想一种模板化规范架构)

springboot一个版本对应一个springcloud版本(必须对应)

例如:springboot2.3.4版本必须对应Hoxton版本(可上springcloud官网查询)因为cloud-alibaba依赖cloud而cloud依赖boot

每个组件例如sentinel等都做了规范,可以查询

配置文件后缀yaml和properties差异

加载顺序yml>properties,优先级properties>yml，properties会覆盖yml

加载顺序和优先级是相反的，正常的加载顺序是yml>properties，但是由于后加载的会覆盖先加载的

bootstrap配置文件和application配置文件差异

bootstrap优先于application加载,用来在程序引导时执行，应用于更加早期配置信息读取，如可以使用来配置application.yml中使用到参数等

application 应用程序特有配置信息，可以用来配置后续各个模块中需使用的公共参数等。

springcloud小结

1：nacos注册中心，解决了服务的注册与发现（微服务之间的调用）

1.5:nacos-config配置中心,解决了多个服务的配置文件分散,但是重复配置冗余的问题且配置不能动态刷新.(统一配置到nacos)

2：Ribbon客戶端负载均衡器，解决了多个微服务实例（或集群）的负载均衡

3：Openfeign 声明式HTTPclient，辅助服务之间远程调用

4：Sentinel 分布式系统的流量防卫兵，通过服务限流、熔断降级，保护服务的稳定，防止出现服务雪崩(一个环节系统崩溃导致其他系统牵连)

5：SpringCould-Gateway（api网关），微服务统一入口，网关可以统一处理（路由转发、过滤、限流、认证）

6：链路监控Sleuth和zipkin:追踪定位链路,结合zipkin更好的去追踪是哪个环节导致速度变慢(类似还有mysql开启慢sql查询,然后通过druid监控台监控)

7.Elasticsearch:作为一个中立的搜索数据库解决复杂查询问题

8.RocketMQ:异步处理请求的中间件(底层是生产者消费者设计模式)

9.redis 多功能kv格式高效内存中间件

10.linux 安全性和高度模块化系统

11.nginx 反向代理和负载均衡

12.freemarker 动态生成静态页面(减少数据库访问次数,占磁盘但速度快)

13.springSecurity 服务之间通过JWT令牌(存放在客户端RSA公私钥加密不易被破解)获取信息和权限

14.docker作为大容器将一个个软件环境和jar包封装成"集装箱"格式解决了版本差异冲突和快速安装软件和搭建集群

15.jekins 帮助我们脚本化的完成从gitee拉项目打成jar包进行基础运行测试上传到gitee的工具(CICD持续集成和持续部署)

14.Util项目作为自建工具类项目供其他包调用

util包作为工具包经常作为<dependency^>依赖被导入其他项目中,然后主项目不写任何工程主项目的pom包作为一个主依赖将通用依赖包一般放到主项目作为父类继承给子项目

然后依赖包的版本控制也是放在主项目的pom依赖中

8.spring-cloud-test引入后在test包下创建test类,然后类上加注释@springbootTest 之后使用@Test注解写方法即可

SpringCloud的概念?

全名:springcloud-netfilx

由于springcloud-nrtfilx已经停更我们主要是用springcloud-alibaba

springcloud是一种思想:将大的springboot项目拆解从而达到解耦合加速启动速度减少因为某个环节问题导致的全体宕机

,同时因为项目的分离可以采取更方便的语言去编写也更方便扩展后期需求最后每个服务之间调用只需通过统一的http协议通信即可 是分布式服务的思想.

springcloud是许多分布式框架与工具的集合体

（微服务全家桶、服务的注册与发现、分布式事务、服务降级、服务熔断、服务限流。。。。）

  

建立一个springcloud工程

1.创建一个maven项目(不添加任何)作为父工程,不写任何代码,作用是锁定版本和通用依赖(锁定springboot和springcloud和springcloud-alibaba的版本)或加入通用依赖

<dependencyManagement^>标签

2.new modele(在父工程下new一个子工程)

搭建maven环境依赖,然后创建application,yaml配置数据库信息,nacos地址,sever端口号,项目名等

然后创建spring启动类(如果服务并没有显示在nacos则加上注解@EnableDiscoveryClient)

3.创建一个实例模块(以后也是根据需求分析创建新模块)

同理pom依赖后调试application.properties,之后便可以写接口了,然后远程调用就使用Openfeign(http请求) 里的restTemplate(需要在Configuration的类中注入restTemplate的@bean加上@LoadBalanced负载均衡注解)

public RestTemplate XXX(){ return new RestTemplate();}

通过nacos调用已经注册在nacos项目上的地址为

http://项目名(端口会自动负载均衡)/Controller类地址前缀/接口地址

如果要启动一个项目的集群首先要修改固定的port不能为固定

server.port={port:80001}代表如果没有指定启动端口则默认8001 然后点击项目edit Configuration(Environment->VM options 填入-Dport=XXX设置启动端口然后点击copy复制一个服务修改name区分)

4.逐步根据需求搭建和注入需要的bean

---------------------------------------------------------------------

SpringCloud常用注解(不包含springboot和spring):

@RefreshScope nacos动态配置刷新一般放在Controller层

@EnableDiscoveryClient 启动项注解,作用是注册为nacos的客户端

@EnableFeignClients 注册为openFeign客户端

@RibbonClient(XXX) ribbon负载均衡策略自定义

@EnableGlobalMethodSecurity(prePostEnable = true) 代表开启方法级别权限控制配合@PreAuthorize("hasAnyAuthority('XXX','XX')")

  

  

  

  

  

### 为什么要用springcloud?

springboot单体架构:

![](C:\Users\YuLi\AppData\Local\YNote\data\qq3AE958F571E4572DE9CC711939D04018\fb6899eee3dd460fa4d194768b5cc4d2\e958edfe2fad4c68bff7db56e1d020ec.jpg)

优点：

1：部署简单: 由于是完整的结构体，可以直接部署在一个服务器上即可。

2：技术单一: 项目不需要复杂的技术栈，往往一套熟悉的技术栈就可以完成开发。

3：用人成本低: 单个程序员可以完成业务接口到数据库的整个流程。

缺点：

1：系统启动慢， 一个进程包含了所有的业务逻辑，涉及到的启动模块过多，导致系统的启动、重启时间周期过长;

2：系统错误隔离性差、可用性差，任何一个模块的错误均可能造成整个系统的宕机;

3：可伸缩性差：系统的扩容只能只对这个应用进行扩容，不能做到对某个功能点进行扩容;

4：线上问题修复周期长：任何一个线上问题修复需要对整个应用系统进行全面升级。

5. 跨语言程度差

6. 不利于安全管理，所有开发人员都拥有全量代码

结论:单体架构不适合互联网（访问量大、业务复杂）项目,只适合传统项目（政府、学校、外包）

springcloud微服务架构

SpringCloud为微服务提供了完美的问题解决方案和框架

微服务的特点:

1:微服务是一种项目架构思想(风格)

2:微服务架构是一系列小服务的组合

3:任何一个微服务，都是一个独立的进程

4:轻量级通信(跨语言,跨平台)

5:服务粒度(围绕业务功能拆分)

6:去中心化管理(不依赖某一种语言)

微服务架构优点:

1.每个微服务组件都能独立部署,更加方便切割功能便于分工合作

2.耦合度低,但是内部内聚较高功能完善,且扩展容易

3.能够自由使用不同类型语言和工具实现功能

缺点:

1、依赖服务变更很难跟踪，其他团队的服务接口文档过期怎么办，依赖的服务没有准备好，如何验证我开发的功能。

2、微服务放大了分布式架构的系列问题，如分布式事务怎么处理，依赖服务不稳定怎么办？

3、运维复杂度陡增，如：部署物数量多、监控进程多导致整体运维复杂度提升。

![](C:\Users\YuLi\AppData\Local\YNote\data\qq3AE958F571E4572DE9CC711939D04018\4ae2c806456649188c5a9123e5e1018f\76e6810cf2544d28a84b376c7cbdc3ee.jpg)