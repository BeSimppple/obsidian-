Freemarker静态化技术

什么是Freemarker?

Freemarker是java编写的模板引擎，它可以将模板页面+动态数据渲染成静态html页面
![[Freemarker模板技术_image_1.jpg]]
网页静态化技术的作用:

1：减轻数据库访问压力(主要)

2：空间换时间访问效率更高(静态页面放硬盘,更快)

3：可以使用nginx作为静态资源服务器(代替tomcat)，并发量更高

4：静态页面更有利于网站SEO(爬虫排名优化(静态化页面的数据都是实际值,而动态页面的是代码命令获取实际值无法根据代码分词))

适合大规模且相对变化不频繁的数据

例如:商品详情页,旅游网站详情页,新闻详情页

Freemarker整合IDEA实现详情页

1.pom依赖

org.freemarker.freemarker 2.3.30

在rousource中定义一个ftl(FreemarkerTemplate)模板页面

页面中需要传输数据的部分写上Freemarker的指令(官方文档可查)

2.代码:

生成静态页面思路流程:

1.构建Configuration对象

new Configuration(Configuration.getVersion())

2.设置模板文件目录(Directory)

configuration.setDirectoryForTemplateLoading(new File(模板页面路径))

3.设置字符集 configuration.setDefaultEncoding("utf-8")

4.获取模板对象 configuration.getTemplate(模板页面)返回template

5.创建模型数据 new HashMap(){{put(key,value)}}

6.创建输出流(Writer)对象 new FileWriter(路径)

7.输出 template.process(模型数据,输出流)

8.关闭 writer.close()

详情页实现思路
![[Freemarker模板技术_image_2.jpg]]
商品审核通过->调用es模块加入ES数据库->同时通过es模块将商品的id通过MQ发送给page模块->page模块写MQ监听器@service使用广播模式消费->拿到商品id后通过openFeign请求商品模块接口到数据库拿信息->然后使用Freemarker生成静态化页面到nginx的包下->配置nginx的conf的servername进行代理配置

wfx-page集群模块搭载nginx,并且由good-page通过广播模式让每个小nginx都有所有页面,然后由一个大的Nginx负载均衡

(注意JWT令牌的验证和传递(如果不能实现解耦合的使用可以直接在page模块进行查db并生成也页面操作不要远程调用goods)

然后和前端联调,配置cros跨域配置

Freemarker常用指令(官方文档查看)

# if判断

<#if goodsPrice gt 99 >

<p>太贵了 买不起!!</p>

</#if>

# for循环

<#list goodsList as goods>

<p>${goods.goodsName}</p>

</#list>

id唯一(雪花算法)

随着订单越来越多，将来势必会进行分库分表，所以订单id不能采取主键自增长的方式，必须采用分布式id解决

采用:IdWorker

满足1.长度一致2.不包含字符串3,保证唯一

![[4C719AD53A354C408E34CFC7F1808162.jpg]]

IdWorker采用的是雪花算法(SnowFlake)

SnowFlake的优点是，整体上按照时间自增排序，并且整个分布式系统内不会产生ID碰撞(由数据中心ID和机器ID作区分)，并且效率较高，经测试，SnowFlake每秒能够产生26万ID左右。

IdWorker的使用

1.将IdWorker工具类放到util工程

2.初始化bean到spring容器

3.使用( idWorker.nextId() )