基于注解的IOC配置
	概念:等同于xml配置,只是配置方式不同
	注解ioc一般使用自己的代码,别人代码不用操作复杂
	环境搭建:导入jar包依赖
	在xml配置文件中
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="
	∙∙∙∙∙∙
	将这两个配置完成才能引用
**使用的注解:**
	注解创建对象
	@Component
		等同于xml中Bean的配置 有属性value指定bean的id
		@Controller @Service @Repository
		是三个同类型衍生注解
		不过更加见名知意-代表mvc框架的3个层级
	注解依赖注入
	等同于xml中相当于： \<property name="" ref="">、 \<property name="" value="">配置
	@Autowired
		自动按照类型注入。当使用注解注入属性时， set 方法可以省略。它只能注入其他 bean 类型。当有多个 类型匹配时，使用要注入的对象变量名称作为 bean 的 id，在 spring 容器查找，找到了也可以注入成功。找不到 就报错。
	@Qualifier
		在自动按照类型注入的基础之上，再按照 Bean 的 id 注入。它在给字段注入时不能独立使用，必须和 @Autowire 一起使用；但是给方法参数注入时，可以独立使用。
		属性:
		value：指定 bean 的 id。
	@Resource
		直接按照 Bean 的 id 注入。它也只能注入其他 bean 类型。
		属性：
		name：指定 bean 的 id。
	@Value
		注入基本数据类型和 String 类型数据的
		属性：
		value：用于指定值
		注解改变作用范围
	@Scope
		指定 bean 的作用范围。
		属性：
		value：指定范围的值。singleton prototype request session globalsession
	**新注解**
	@Configuration
		用于指定当前类是一个 spring 配置类， 当创建容器时会从该类上加载注解。 获取容器时需要使用 AnnotationApplicationContext(有@Configuration 注解的类.class)。
	@ComponentScan
		用于指定 spring 在初始化容器时要扫描的包。 作用和在 spring 的 xml 配置文件中的：<context:component-scan base-package="com.qzw"/>是一样的。
		属性：
		basePackages：用于指定要扫描的包。和该注解中的 value 属性作用一样。
	@Bean
		该注解只能写在方法上，表明使用此方法创建一个对象，并且放入 spring 容器。
		属性：
		name：给当前@Bean 注解方法创建的对象指定一个名称(即 bean 的 id）。
	@PropertySource
		用于加载properties文件。例如我们配置数据源时，可以把连接数据库的信息写到properties 配置文件中，就可以使用此注解指定 properties配置文件。
		属性：
		value[]：用于指定properties文件路径。如果是在类路径下，需要写上 classpath:
	@import
		用于导入其他配置类.
		属性：
		value[]：用于指定其他配置类的字节码。

**注解整合JUnit**
	Spring提供了对JUnit的整合支持
	操作步骤:
	1.导入jar包依赖Spring-test
	2.设置类运行器(注解添加到运行程序类的上方) @RunWith(SpringJUnit4ClassRunner.class)
	这一步的目的是:设置JUnit的运行期为Spring
	3.设置读取Spring的配置文件路径
	@ContextConfiguration(locations="classpath:Spring01.xml")
	这一步的目的是:加载配置
**Spring注解和Xml的区别**
	注解的优势：
	配置简单，维护方便（我们找到类，就相当于找到了对应的配置）。
	XML 的优势：
	修改时，不用改源码。不涉及重新编译和部署
	![[IOC(控制反转给容器)_image_1.jpg|400]]
	我们发现，之所以现在离不开 xml 配置文件，是因为我们有一句很关键的配置：
	<context:component‐scan base‐package="com.qzw"></context:component‐scan>



