
Spring

Spring概述:

Spring是分层的 Java SE/EE应用轻量级开源框架，以 IoC（Inverse Of Control：反转控制）和 AOP（Aspect Oriented Programming：面向切面编程）为内核。

提供了视图层SpringMVC和持久层Spring JDBCTemplate以及业务层事务管理等众多的企业级应用技术，还能整合开源世界众多著名的第三方框架和类库.

体系:

![[Spring_image_1.jpg]]

依赖关系:

![](C:\Users\YuLi\AppData\Local\YNote\data\qq3AE958F571E4572DE9CC711939D04018\de32086d8507415ab39b11e4f567dd47\15fb117bfc0b4f30a4f2e169e57de47d.jpg)

Bean是什么?

bean是一个书面注解,凡是需要ioc翻转控制的带有方法和属性的类,需要实例化调用方法都需要祖册到bean来注入(DI)spring容器实现IOC(反转控制)

注入到spring容器中的bean我们就可以使用反射方式获取从而实现减少耦合减少new

Bean的生命周期:

单例在加载时候初始化,容器销毁同时死亡

多例:加载时候初始化,长时间不使用,被java中的gc垃圾回收器回收(不归spring容器管属jvm管)

1、 实例化(Instantiation)

无参构造参数创建bean实例

2、 属性设置(populate)

调用set方法设置属性值

3、 初始化(Initialization)

init初始化

4、 销毁(Destruction)

destroy销毁

IOC控制反转(inversion of control)

原本是主动获取程序通过new

现在是通过工程,工厂获取(反射)并返回 是被动的

这就是控制反转

作用:降低计算机程序有耦合

spring常用注解（不包含springmvc和spring boot）

使用DI注入类相关：

@Component：泛指各种组件

@Controller、@Service、@Repository都可以称为@Component。

@Controller：控制层

@Service：业务层

@Repository：数据访问层

@Bean导入第三方包里面的注解

@Import(要导入到容器中的组件)；

@ImportSelector：返回需要导入的组件的全类名数组；

注入Bean相关的：

@Autowired：由bean提供可以作用在变量、setter方法、构造函数上；有个属性为required，可以配置为false；

@Inject：由JSR-330提供用法和@Autowired一样。

@Resource：由JSR-250提供，差别在是按照名称匹配的

映射或序列化相关：

@JsonIgnore在json序列化时将java bean中的一些属性忽略掉，序列化和反序列化都受影响。

一般标记在属性或者方法上，返回的json数据即不包含该属性。

如果是fastjson则使用@JSONField

AOP（切面）相关的：

@Aspect 声明一个切面

@After 在方法执行之后执行（方法上）

@Before 在方法执行之前执行（方法上）

@Around 在方法执行之前与之后执行（方法上）

@PointCut 声明切点

Java配置类相关注解：

@Configuration声明当前类为配置类；

@Bean注解在方法上，声明当前方法的返回值为一个bean，替代xml中的方式；

@ComponentScan用于对Component进行扫描；

@Bean的属性支持相关：

@Scope设置类型包括：

设置Spring容器如何新建Bean实例（方法上，得有@Bean）

① Singleton（单例,一个Spring容器中只有一个bean实例，默认模式）,

② Protetype（每次调用新建一个bean）,

③ Request（web项目中，给每个http request新建一个bean）,

④ Session（web项目中，给每个http session新建一个bean）,

⑤ GlobalSession（给每一个 global http session新建一个Bean实例）

环境相关：

@Profile

指定组件在哪个环境的情况下才能被注册到容器中，不指定，任何环境下都能注册这个组件。

@Conditional

通过实现Condition接口，并重写matches方法，从而决定该bean是否被实例化。

异步相关注解：

@EnableAsync

配置类中通过此注解开启对异步任务的支持；

@Async

在实际执行的bean方法使用该注解来声明其是一个异步任务（方法上或类上所有的方法都将异步，需要@EnableAsync开启异步任务）

定时任务相关注解：

@EnableScheduling

在配置类上使用，开启计划任务的支持（类上）

@Scheduled

来申明这是一个任务，包括cron,fixDelay,fixRate等类型（方法上，需先开启计划任务的支持）

将配置文件内容注入（或映射到）实体类：

@value注解 从properties文件中读取内容，注入普通字符，注入操作系统属性，注入表达式结果，注入其它bean属性，注入文件资源，注入网站资源，注入配置文件等等 用法：${} #{} #{}

@PropertySource（ 直接将指定目录下properties配置文件的内容映射到pojo实体类中 搭配@configurationProperties（prefix = “”）使用

其他：

@EnableAspectAutoProxy。。。等等Enable类型开启某功能注解

@ContextConfiguration用来加载配置配置文件，其中classes属性用来加载配置类。通常与@RunWith(SpringJUnit4ClassRunner.class)联合使用用来测试

@SuppressWarnings 批注允许您选择性地取消特定代码段（即，类或方法）中的警告

@Transient 如果一个属性并非数据库表的字段映射，就务必将其标示为@Transient，否则ORM框架默认其注解为@Basic；

  

ApplicationContext 接口的实现类ClassPathXmlApplicationContext：

它是从类的根路径下加载配置文件 推荐使用这种

会自动在读取配置文件的时候创建对象(空间换取时间)

注意事项:

ClassPathXmlApplicationContext()对象操纵bean.xml时候会默认自动调用对象的无参构造对象除了把socpe设置为prototype的多例设计模式

FileSystemXmlApplicationContext：

它是从磁盘路径上加载配置文件，配置文件可以在磁盘的任意位置。

AnnotationConfigApplicationContext:

当我们使用注解配置容器对象时，需要使用此类来创建 spring 容器。它用来读取注解。

BeanFactory:

是Spring容器中的顶层接口AppliactionContext是他的子接口

区别:BeanFactory不会主动在创建时读取对象

  

耦合及处理方法

耦合性就是模块之间的关联程度

内聚就是模块的独立能力强弱

低耦合高内聚是理想状态

Spring解绝三层架构的耦合问题

//1.尽量减少new关键字创建对象 减少导包(使用反射)

//但是使用反射javaBean对象创建次数太多了(配置使其变成单例)

//2.反射2和javaBean之间的耦合度高(使用反射工厂模式来解决)

//3.全限定类名字符串和硬编码问题(使用xml配置文件)

  

  

  

Spring创建对象的三种方式

1.构造器初始化,Bean对应类必须提供一个无参构造方法

2.调用静态工厂 静态工厂内是静态getInstance创建对象,直接类名 点 调用就能生成 这里bean给的就是类名和调用方法名

<bean id ="user3" factory-method="getIntance" class ="com.YL.Bean.Factory01"></bean>

3.调用动态工厂 动态工厂不是静态 需要类 和方法名 factory给的就是类

<bean id ="user4" factory-method="getIntance" factory-bean="factory02"></bean>

  

Bean的作用域

singleton:默认值，单例的

prototype:多例的

request:WEB项目中，Spring创建一个Bean的对象，将对象存入到request域中

session:WEB项目中，Spring创建一个Bean的对象，将对象存入到session域中

  

依赖注入DI

依赖注入： Dependency Injection。

它是 Spring 框架核心 IOC 的具体实现。

在编写程序时，通过控制反转，把对象的创建交给了Spring，但是代码中不可能出现没有依赖的情况

IOC 解耦只是降低他们的依赖关系，但不会消除。

  

依赖注入的数据类型

1.基本数据类型和String

2.javaBean类型

复杂类型/集合类型

1.依赖注入的方式

2.使用构造函数注入

3.使用set方法注入

4.使用注解

  

注意事项:

DI注入简单类型使用value 引用使用ref

假如父xml导入错误导致配置bean的id相同那么拿出的时候依赖覆盖原则,最晚覆盖的则被拿出

  

注入数组,集合,map

<bean. id. class.>

<property name.. >

<array>

<value>1</value>

<value>1</value>

</array>

同理集合

map则为

<map>

<entry key... value ... ></entry>

</map>

  

团队开发:

分为多个xml文件

可以用过import导入到一个组长类xml文件

Spring程序开发步骤

  

使用此方法

UserDao userDao = Spring客户端.getBean(id标识);

代替UserDao userDao = new UserDaoLmpl();

然后通过Spring框架读取xml配置文件

通过id标识获取到Bean全限定名

通过反射newInstance创建Bean对象 返回对象

1.导入Spring开发的基本包坐标

2.编写Dao接口和实现类

3.创建Spring核心配置文件

4.在Spring配置文件中配置UserDaoLmpl

5.使用Spring的API获得Bean实例