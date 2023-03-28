Sleuth(链路监控)

springCloud-Sleuth(一般兼容zipkin(实时数据追踪系统生成图表))

sleuth的作用(定位具体问题位置)

1.解决微服务后期项目庞大服务之间调用错综复杂不好查找那个环节导致速度降低的问题(sleuth提供了清晰的客户端和服务端调用时间表格)

sleuth结合zipkin(下图)

![[Sleuth_image_1.jpg]]

seluth示例数据(下图):

![[Sleuth_image_2.jpg]]

springcloud整合sleuth和zipkin

1.导入pom依赖(可以直接在父工程导入,因为大多数都需要集成该系统)

spring-cloud-starter-sleuth

spring-cloud-starter-zipkin

2.下载zipkin的jar包(zipkin-server-2.12.9-exec.jar)

3.编写zipkin本地启动脚本bat

java -jar zipkin-server-2.12.9-exec.jar --server.port=9999

pause

4.项目properties配置

spring.zipkin.base-url=http://localhost:9999(#zipkin服务端地址是个大坑,前缀http://一定要加)

spring.zipkin.discoveryClientEnabled=false(#禁止zipkin把自己当做nacos-client向nacos-server注册)

spring.sleuth.sampler.rate=100(#sleuth抽样率默认是10%，设置为100表示100%请求上报zipkin)

5.打开localhost:9999/zipkin 发送请求加载到zipkin页面

几个常见术语(span,trace,Annotation):

Span：基本工作单元，例如，在一个新建的span中发送一个RPC等同于发送一个回应请求给RPC，span通过一个64位ID唯一标识，trace以另一个64位ID表示，span还有其他数据信息，比如摘要、时间戳事件、关键值注释(tags)、span的ID、以及进度ID(通常是IP地址) span在不断的启动和停止，同时记录了时间信息，当你创建了一个span，你必须在未来的某个时刻停止它。

Trace：一系列spans组成的一个树状结构，例如，如果你正在跑一个分布式大数据工程，你可能需要创建一个trace。

Annotation：用来及时记录一个事件的存在，一些核心annotations用来定义一个请求的开始和结束

cs - Client Sent -客户端发起一个请求，这个annotion描述了这个span的开始

sr - Server Received -服务端获得请求并准备开始处理它，如果将其sr减去cs时间戳便可得到网络延迟

ss - Server Sent -注解表明请求处理的完成(当请求返回客户端)，如果ss减去sr时间戳便可得到服务端需要的处理请求时间

cr - Client Received -表明span的结束，客户端成功接收到服务端的回复，如果cr减去cs时间戳便可得到客户端从服务端获取回复的所有所需时间 将Span和Trace在一个系统中使用Zipkin注解的过程图形化：

selth和zipkin的数据持久化

数据异步同步放到ES搜索数据库中!

或者放到mysql数据库

  

zipkin(实时数据追踪系统)

ZipKin[架构](https://so.csdn.net/so/search?q=%E6%9E%B6%E6%9E%84&spm=1001.2101.3001.7020)

    ZipKin可以分为两部分，一部分是zipkin server，用来作为数据的采集存储、数据分析与展示；zipkin client是zipkin基于不同的语言及框架封装的一些列客户端工具，这些工具完成了追踪数据的生成与上报功能，架构如下：

![[Sleuth_image_3.jpg]]
（1）Collector 接收或收集各应用传输的数据

（2）Storage 存储接受或收集过来的数据，当前支持Memory，MySQL，Cassandra，ElasticSearch等，默认存储在内存中。

（3）API（Query） 负责查询Storage中存储的数据，提供简单的JSON API获取数据，主要提供给web UI使用

  

（4）Web 提供简单的web界面

  

zipkin调用uml图

![[Sleuth_image_4.jpg]]