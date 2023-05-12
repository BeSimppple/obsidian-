**SpringBoot的主旨是:约定大于配置**
**Springboot作用:**
	1.解决SSM框架的XML配置和jar包过多不易于修改和查看
	2.jar包版本不匹配版本管理困难问题
	**核心功能（优点）**
	 1.**==起步依赖==**（spring起步依赖包配置了对应功能需要的整合包例如nosql相关的需要的redis和jedis等减少了插件之间的冲突，减少过多的jar包依赖）,
	 2.**==自动配置==**(@AutoConfigrationPakage)
		自动配置原理:
		 启动类@SpringbootApplication注解下，有三个关键注解
		（1）@springbootConfiguration:表示启动类是一个自动配置类
		（2）@CompontScan:扫描启动类所在包外的组件到容器中
		（3）@EnableConfigutarion:最关键的一个注解，他拥有两个子注解，其中@AutoConfigurationpackage会将启动类所在包下的所有组件到容器中，@Import会导入一个自动配置文件选择器，他会去加载META_INF目录下的spring.factories文件，这个文件中存放很大自动配置类的全类名，这些类会根据元注解的装配条件生效，生效的类就会被实例化，加载到ioc容器中
		-
		起步依赖包内还提供了自动配置类通过java配置类(@Configuration)减少xml配置工厂等步骤,可以一步在properties文件中配置会被识别提取给自动配置类中使用
		 @Configuration注解告知这是一个配置类
		 @PropertySource指定配置文件路径
		 @ConfigurationProperties指定配置的前缀提取@bean注解等同于spring的xml文件中的bean作为加入spring容器的书面申请<small>(@bean一般放在方法上告诉spring容器可以从下面方法返回一个bean）</small>
	 3.**内嵌式web服务器**（Tomcat\jetty）等（使web服务无需打包成war包可直接打成jar包）
	 4.**提供POM**，简化maven配置
	 5.**版本锁定**，springboot根据使用的版本提供插件版本，如需改变可以version覆盖或parent引用修改
		springboot2.3.4release需要的环境mavean3.3+和jdk1.8+
		目前2022/10/24最新的正式版是2.7.5于2022年5月发布
springboot项目的创建三种方式
	1.spring官网在线生成（如果未被识别为maven项目则手动help-》find action-》add maven project）
	2.idea向导在线生成（新建项目-》选中spring initializr（默认使用外网进行创建可以输入http://start.aliyun.com使用阿里云进行创建））
	3.新建maven项目自己创建脚手架的方式离线生成
**springboot项目结构：**
	**springboot中约定大于配置，约定就是（默认扫描根包）（配置类就用application开头。yaml或。properties结尾）（test包是一一对应java包所以外面两层文件夹不能省略）**
	结构规范:**service**,**mapper**,**web**,**entity**必须创建在根包（引导类所在的包）下面才能被scan（扫描）到
	![[SpringBoot_image_1.jpg|550]]
	配置文件后缀yaml和properties的差异
		**加载顺序yaml>properties**
		**优先级properties>yaml**
		加载顺序和优先级是相反的，后加载的会覆盖先加载的 ,properties会覆盖yaml
	bootstrap配置文件和application配置文件差异
		**bootstrap优先于application加载,用来在程序引导时执行，应用于更加早期配置信息读取**，如可以使用来配置application.yml中使用到参数等
		application 应用程序特有配置信息，可以用来配置后续各个模块中需使用的公共参数等。
	properties中包含许多springboot默认配置但是不显示(可以查看springboot文档)
springboot常用properties配置：
	spring.profiles.active: dev(多环境切换环境配置,通用配置放原版,差异配置放对应环境properties)
	server.port=8081（修改端口号）
	server.servlet.context-path=/wfx（修改根路径）
	spring.jackson.date-format=yyyy-MM-dd HH:mm:ss（时间规范写法）
	spring.jackson.time-zone=Asia/Chongqing（修改时区）
	spring.datasource.XXX =XXX(数据库连接池信息配置)
	spring.redis.cluster.nodes=XXX(redis集群ip端口配置)
	mybatis-plus.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl(mybatis-plus提供的打印sql日志功能)
	spring.profiles.active=pro(切换环境)

springboot整合SSM框架
	springboot已经有springMVC和spring了实际上就是加入mybatis
	首先pom加入mybatis-start起步依赖和mysql驱动
	起步依赖中有配置类MybatisAutoConfiguration。class其中包含几个类（DataSourceProperties类等），最后得出的需求是我们需要配置数据库的基础参数（url，driverclass，username，password）到properties中供这DataSourceProperties个类映射使用 数据库类型type默认hirki
	1.在启动类上加@MapperScan()扫描指定的mapper接口包
	2.在配置类上设置别名 mybatis.type-aliases-package=XXX(这样才能映射到正确实体类)
	3.mybatis.mapper-locations=classpath:com.qf.mapper/*Mapper.xml(springboot可以省略mapper映射路径会自动配置因为约定>配置)
	mybatis主要使用映射进行sql的操作建立对应mapper的xml文件(负责编写复杂sql语句或者返回复杂类型如resultMap)在resources包下(必须保证名字相同才能映射,注意包必须一个一个建立)

springboot整合redis
加入redis pom起步依赖,包含了redis和lettuce(不同于jedis是异步和线程安全)与mybatis起步依赖包相同同时创建了RedsiAutoConfiguration
加入commons-pool12的jar包因为redis集群需要其中依赖
spring.redis.cluster.nodes=XXX配置节点
spring.redis.lettcue.pool.XXX=XXX配置连接池参数
spring.redis.password=XXX密码
配置完成后可以使用@Autowired取出StringRedisTemplate(String方式字符串序列化明文显示)或RedisTemplate(obj类的序列化方式不明文显示)
一般使用string的
Springboot整合其他工具软件都是同理最好能有springboot支持的启动类jar包省去其他配置依赖
例如添加mybatis-plus或element或jenkins
**SpringBoot中常用注解（可能包括spring和springMVC和springcloud）**
	@SpringBootApplication 等同于同时使用@Configuration(声明当前类是一个配置类)，@EnableAutoConfiguration(开启自动配置)和
	@ConditionalOnbean 是springboot提供的条件注解，意思是进行mybatis自动配置前，必须要有datasource对象
	@MapperScan,全局扫描在引导类上加（指定扫描位置默认加上@Mapper作为数据库映射的代理类）
	@ComponentScan(配置组件扫描的指令)及其默认属性。
	@PropertySource  导入properties文件
	@ImportResource 将资源导入spring容器
	@Scope指定bean的作用域，默认singleton，其它包括prototype、request、session、globalSession
	@Lazy 使bean懒加载，取消bean预初始化。
	@Primary 自动装配时当出现多个Bean候选者时，被注解为@Primary的Bean将作为首选者，否者将抛出异常
	@AutoConfigureBefore 在指定配置类初始化前加载
	@AutoConfigureOrder 指定配置类初始化顺序，越小初始化越早
	缓存相关：
	@EnableCaching，开启缓存配置，支持子类代理或者AspectJ增强
	@CacheConfig，在一个类下，提供公共缓存配置
	@Cacheable，放着方法和类上，缓存方法或类下所有方法的返回值
	@CachePut，每次先执行方法，再将结果放入缓存
	@CacheEvict，删除缓存
	@Caching，可以配置@Cacheable、@CachePut、@CacheEvict
	注入配置文件properties相关：
	@EnableConfigurationProperties，启动@ConfigurationProperties功能
	@ConfigurationProperties，将properties文件里的内容，自动注入bean对应的属性中
	JPA(持久层api)相关：
	@Entity ，@Table(name="")
	表明这是一个[实体类](https://so.csdn.net/so/search?q=%E5%AE%9E%E4%BD%93%E7%B1%BB&spm=1001.2101.3001.7020)，一般用于jpa，这两个注解一块使用，但是如果表名和实体类名相同的话，@Table可以省略。
	@Alias("goods_")配置别名,有时候会发生别名和其他项目冲突的情况
	@Column 如果字段名和列名相同，则可以省略。
	@Id 表示该属性为主键。
	@TableId(type = IdType.AUTO)mybatis-plus提供的注解表示该属性对应数据库表中主键并且是自增长
	@Transient（常用） 表示该属性并非一个到数据库表的字段的映射，ORM框架将忽略该属性。如果不是数据库表的字段映射而是java项目加的则必须标注该注解
	@JsonIgnore 在实体类向前台返回数据时用来忽略不想传递给前台的属性或接口。
	事务相关：@transactional
	导入配置文件相关：
	@PropertySource 引入单个properties文件：
	@ImportResource 导入xml配置文件
	@Import 导入额外的配置文件
	lombok常用注解：
	@Data注解 （自动包含getter、setter、NoArgsConstructor、equals、canEqual、hashCode、toString）
	@NoArgsConstructor注解：在JavaBean或类JavaBean中使用，使用此注解会生成对应的无参构造方法；
	@AllArgsConstructor注解：在JavaBean或类JavaBean中使用，使用此注解会生成对应的有参构造方法；
	@Slf4j：在需要打印日志的类中使用，当项目中使用了slf4j打印日志框架时使用该注解，会简化日志的打印流程，只需调用info方法即可；
	@Log4j：使用了log4j框架，原理同slf4j
	@ToString(exclude={"column1","column2"}) 其他get和set等同理tostring写法
	意义：排除多个column列所对应的元素，其中间用英文状态下的逗号进行分割，即在生成toString方法时不包含多个column参数；
	@NonNull 给方法中的参数加会自动校验是否为空
	数据库相关注解：
	@MapperScan与
	@Mapper Mybatis 的注解，和 Spring 没有关系，@Repository 和@component 是 Spring 的注解，用于声明一个 Bean。
	在 Spring 程序中，Mybatis 需要找到对应的 mapper接口，在编译的时候动态生成代理类，实现数据库查询功能，所以我们需要在接口上添加 @Mapper 注解。
	通过命令行配置属性（直接通过cmd启动项目）
	命令：
	java -jar xxx.jar --server.port=8888
	通过使用–-server.port属性来设置xxx.jar应用的监听端口为8888
SpringBoot注意事项
	注意：当你做了pom依赖就立马启动工程，那么启动会报错，为什么呢？因为Mybatis-springboot-starter在自动化配置的时候，需要使用到datasource，但是容器中还没有datasource（因为你都没告诉springboot你的数据库信息）所以第二步就是配置数据库信息
	mybatis-plus代码生成器：
	表-驱动生成--》entity,Mapper接口,Mapper xml文件，Service接口，Service实现类，Controller
	Spring的基础配置等
	springboot2.0以后使用的默认的数据库连接池为HiKariCP
# redis集群版
spring.redis.cluster.nodes=192.168.234.131:7001,192.168.234.131:7002,192.168.234.131:7003
spring.redis.password=
​#切换环境(Springboot多环境配置)
spring.profiles.active=pro
#指定数据库类型:
spring.datasource.type=com.alibaba.druid.pool.DruidDataSource
#配置别名
mybatis.type-aliases-package=com.qf.entity
#加载Mybatis映射文件
mybatis.mapper-locations=classpath:com.qf.mapper/*Mapper.xml
thymeleaf入门使用
起步依赖->配置thymeleaf
常用方法th:each th:if 等
springboot和spring 和Springcloud的区别?
1.springboot对第三方插件封装和整合，提供第三方接口
无需配置复杂的xml
2.springcloud基于springboot实现的框架,springcloud是一些列框架的集合体
SpringBoot专注于快速方便的开发单个个体微服务。
SpringCloud是关注全局的微服务协调、整理、治理的框架，它将SpringBoot开发的单体整合并管理起来。
SpringBoot可以离开SpringCloud独立使用开发项目，但是SpringCloud离不开SpringBoot，属于依赖关系。
springboot的优点：
1：可以快速创建独立服务（独立开发、独立部署、独立运行、独立维护,功能单一）(微服务) springboot的大背景就是微服务架构
2：提供了内置的tomcat服务器，服务可以无需打成war包，直接打成jar
java -jar xx.jar
3：springboot提供了起步依赖,依赖jar包冲突变少：mybatis-spring-boot-starter(包含mybatis、spring)
4：springboot提供了自动配置，0xml配置,0xml配置不代表没有配置，只不过将xml配置转换成java（@Configuration、@Bean）配置，
将java配置放入jar中，服务依赖jar包，从而屏蔽掉了配置
5：springboot提供了服务的健康状态监测的功能
6：0 xml配置
一些概念：
　　Jedis：是Redis的Java实现客户端，提供了比较全面的Redis命令的支持，
　　Redisson：实现了分布式和可扩展的Java数据结构。
　　Lettuce：高级Redis客户端，用于线程安全同步，异步和响应使用，支持集群，Sentinel，管道和编码器。
优点：
　　Jedis：比较全面的提供了Redis的操作特性
　　Redisson：促使使用者对Redis的关注分离，提供很多分布式相关操作服务，例如，分布式锁，分布式集合，可通过Redis支持延迟队列
后续解决多项目的分布式锁会使用redisson
　　Lettuce：主要在一些分布式缓存框架上使用比较多
springboot是一个项目建立完成后,放到tomcat服务器.
一般是打包成war包或jar包放到webapps文件夹然后启动tomcat,和nginx(负载均衡多个tomcat同事做代理和ip地址管理),数据链接mysql数据库.一些功能可以通过完全独立的redis中间件实现