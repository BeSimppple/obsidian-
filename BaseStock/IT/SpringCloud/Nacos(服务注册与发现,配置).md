注册中心有:Nacos , Eureka , Consul , Zookeeper
![[Nacos(服务注册与发现,配置)_image_1.jpg]]

**nacos主要的作用,实现了什么功能?**
	1.**解决了服务的注册与发现**
	每个服务室独立的,他们并不知道其他服务的存在也无法调用,nacos的存在让服务注册到nacos上使其他服务能够了解这个服务的存在(通过name service),同时nacos也能通过发送的服务名请求去调用已注册的服务同时通过ribbon负载均衡更好的利用集群
	2.**解决了多个服务之间调用**
	如果一个集群增加新的实例或更改配置需要去改原始配置和多个项目代码的问题(通过config service),现在统一在nacos上进行配置 注册配置集群的端口号等配置信息

安装nacos(linux和windows)
	**windows**
	可以通过下载安装包下载(推荐),或者通过git获取源码编然后maven-install方式(不推荐)
	**linux**
	安装环境配置需求:jdk与maven
	yum安装nacos或者下载gz包解压
	修改配置文件application.properties->nacos产生的信息固定存储到mysql数据库
	spring.datasource.platform=mysql
	\#固定ip
	nacos.inetutils.ip-address=XXX
	\### 数据库数量 非集群就是1
	db.num=1
	###url等信息
	db.url.0=jdbc:mysql://127.0.0.1:3306/nacos?characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=UTC
	db.user=root
	db.password=123456
	**修改启动文件startup.sh(修改内存占用大小)**
	JAVA_OPT="${JAVA_OPT} -server -Xms300m -Xmx300m -Xmn100m -XX:MetaspaceSize=128m -XX:MaxMetaspaceSize=160m"
	cluster.conf
	**修改集群conf(如果一台服务器有多个nacos则不改)**
	使用nacos-mysql.sql文件生成对应数据库和表信息
	编辑statup.sh文件,因为默认启动模式为集群模式,需要手动修改为单机模式MODE="standalone"集群模式是cluster
	启动后输入地址加上8848/nacos则可访问
**nacos在idea中常用配置**
	spring.cloud.nacos.discovery.namespace=3cadb64d-6b4c-4f84-b25f-ceb34c948951(命名空间)
	spring.cloud.nacos.discovery.group=my-group(组配置)
	spring.cloud.nacos.discovery.server-addr:XXX(naocso,ip配置)
	@EnableDiscoveryClient 注册该项目到nacos
## nacos实现高可用(集群)
![[Nacos(服务注册与发现,配置)_image_2.jpg|400]]
1.vip不是必须的,因为nacos主要也是运维在使用不会有太大访问量
2.一主多从(类似redis哨兵模式)
伪集群:练习使用(搭建在linux一台虚拟机上多个不同端口区分)
真集群:nacos搭建在不同服务器上(生产使用)
1.linux上安装nacos 2.修改基本nacos.conf的ip和数据库等信息 3.修改startup.sh文件中JAVA_OPT=XXX内存占用大小
4.修改conf文件夹下的cluster.conf配置ip地址和端口号
5.启动nacos集群,然后到idea中配置集群所有addr
6.(可选)配置nginx反向代理,同样监听指定端口,upstream指定代理server,proxy_pass指定地址然后修改host模拟www.nacos.com/nacos登录
重点:记得startup.sh中的MODE="cluster"不要改了
## nacos实现持久化
只需将修改conf将数据源存到mysql
nacos宕机后消费者客户端还能调用服务端么?
可以,只要服务提供者不宕机不改变端口号或ip,不增加实例.便能通过本地缓存登录服务.
nacos源码核心代码
//定时30秒更新client缓存
DynamicServerListLoadBalancer.updateListOfServers
//从nacos server获取最新的实例列表
NacosServerList.getServers
**nacos工作原理**
![[Nacos(服务注册与发现,配置)_image_3.jpg]]
服务心跳5s一次由服务客户端发送给nacos
服务发现每30一次刷新由消费者客户端发送给nacos
服务健康检查由nacos服务器发送请求(通过actuator的http接口)给服务客户端
什么是服务注册于发现?
	首先一个分布式服务(一个完整的功能拆分给不同的服务去实现)其中一个服务可以搭建多个服务集群(一个服务可以部署多个服务实例[进程]，形成一个服务集群)
	其中一个小的服务的调用和负载均衡(在集群的环境下，采用相应的负载均衡策略，平均化服务的负载问题),主要依赖于nacos server(服务发现和调用)和ribbon(负载均衡)
	服务注册：将服务注册到注册中心，注册中心维护服务的元数据信息
	服务发现：服务的调用者，可以从注册中心根据应用名获取服务列表，客户端做负载均衡。
**nacos底层架构**
![[Nacos(服务注册与发现,配置)_image_4.jpg]]
nacos常用功能：
	名字服务 (Naming Service)
	命名服务是指通过指定的名字来获取资源或者服务的地址，提供者的信息
	 配置服务 (Configuration Service)
	动态配置服务让您能够以中心化、外部化和动态化的方式管理所有环境的配置。动态配置消除了配置变更时重新部署应用和服务的需要。配置中心化管理让实现无状态服务更简单，也让按需弹性扩展服务更容易。
**Nacos集群架构**
![[Nacos(服务注册与发现,配置)_image_5.jpg|500]]
真集群搭建:nacos实例搭建在不同的服务器里面
假集群搭建:一台服务器搭建多个nacos实例,通过不同端口区分,nginx代理多个nacos实例
生产环境需使用MySQL作为后端存储，因此需要搭建MySQL(存储nacos中的config配置文件?)
nacos领域模型
在不同的nameSpcae的微服务相互隔离
不同group的微服务相互隔离
![[Nacos(服务注册与发现,配置)_image_6.jpg|500]]
Nacos-config
![[Nacos(服务注册与发现,配置)_image_7.jpg|600]]
nacos-config主要解决什么问题?
	1.重复配置(配置共享common配置解决),配置分散
	2.配置不能动态刷新(@RefreshScope)
	同类型spring-cloud-config(读写太差),Apollo(不错)
nacos-config如何使用
	1.依赖nacos-config的pom依赖
	2.在nacos中自己创建对应命名空间和组
	3.添加common通用配置和每个模块配置
	4.idea的bootstrap.properties(bootstrap启动先于application config拉取配置必须在bootstrap
	)中添加nacos-config配置拉取nacos中的配置
配置最佳实践总结
1：尽量规避优先级，一个配置不要到处重复配
2：所有配置加注释
3：配置管理的人员尽量少
新建配置文件名命名规范(约定大于配置):
项目名-命名空间.properties
配置格式一般使用yaml或properties
动态刷新配置!(能在不停用服务器的情况下修改配置)
@RefreshScope注解加在Controller类上
如果调用了通用配置也想动态刷新,则要配置
spring.cloud.nacos.config.refreshable-dataids=common.properties
配置回滚!!
nacos控制台操作->配置管理->历史版本输入想找到项目名和组名->点击回滚
nacos通用配置
使用: 控制台新建通用配置(随意命名)java项目properties中引入通用配置
Nacos的Common.properties中常用配置:
统一的nacos-discovery-add,namespace
Sentinel的ip和端口
DB数据库的配置
映射别名配置