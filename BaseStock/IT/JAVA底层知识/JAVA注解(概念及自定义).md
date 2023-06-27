**注解定义与使用原理**
	**定义**:
	1. **注解就是一个接口,默认继承Annotation接口**
	2. 注解也叫元数据,一种代码级别的说明与类,接口,枚举在同一层次
	3. 它可以声明在包,类,字段,方法,局部变量,方法参数等的前面,用来对这些元素进行说明,注释.在jdk1.5以后引入说明程序的
	**原理**:
	注解的实现原理是通过Java反射机制来实现的。在Java中，可以使用反射机制来获取类、方法、字段等的信息，并对它们进行操作。
	**注意**:
	注解单独使用是毫无用处的,如果想要注解有作用必须结合反射使用,反射是在运行时注解的默认生命周期在编译期.到不了运行时所以需要Retention来设置生命周期
	


**注解的属性**
	**生命周期**:
		有三种生命周期默认注解生命周期在编译器到不了运行时
		@Retention(RetentionPolicy.枚举类)来设定生命周期(Retention属于系统自带的注解)    
		生命周期长度: SOURCE < CLASS < RUNTIME
		1.SOURCE注解驻留在源文件阶段 2.CLASS字节码文件阶段 3.RUNTIME内存字节码阶段(运行时可见)
	**对象范围**:
		 @Target({ ElementType.枚举类 })   例如:ElementType.METHOD 表示该注释仅在方法上运行
		(系统自带的注解,可以标注修饰对象范围)
	其他属性
		如果注解中有String[] value[] 则表示可以直接输入参数会被识别成value
		如果没有则必须对应属性输入对应参数例如Type = "all"

元注解(系统自带注解)
	1. @Target：注解的作用目标
	2. @Retention：注解的生命周期
	3. @Documented：注解是否应当被包含在 JavaDoc 文档中
	4. @Inherited：是否允许子类继承该注解

**自定义注解**
	注解就是一个接口,默认继承Annotation接口
	格式:
	元注解
	public @interface 注释名称{}
	属性:接口中的抽象方法
	要求:
	1.属性(抽象方法)的返回值类型有下列取值
	基本数据类型
	String
	枚举
	注解
	以上类型的数组
	2.定义了属性(抽象方法)在使用时需要给属性赋值
	2.1.如果定义属性时,使用default关键字给属性默认初始化值,则使用注解时,可以不进行属性赋值
	2.2.如果只有一个属性需要赋值,并且属性的名称为Value.则Value可以省略,直接定义值即可
	2.3数组赋值时,值使用{}包裹.如果数组中只有一个值,则{}省略
	元注解:用来描述注解的注解
	常用元注解
		@Target:描述注解能够作用的位置
		@Retention:描述注解被保留的阶段
		@Documented:描述注解是否被抽到api文档中
		@Inherited:描述注解是否类继承
		在程序使用(解析)注解





**常用注解**:
	@Aspect
		@Aspect:作用是把当前类标识为一个切面供容器读取
		1.  @Pointcut：Pointcut是植入Advice的触发条件。每个Pointcut的定义包括2部分，一是表达式，二是方法签名。方法签名必须是 public及void型。可以将Pointcut中的方法看作是一个被Advice引用的助记符，因为表达式不直观，因此我们可以通过方法签名的方式为 此表达式命名。因此Pointcut中的方法只需要方法签名，而不需要在方法体内编写实际代码。
		2.  @Around：环绕增强，相当于MethodInterceptor
		3.  @AfterReturning：后置增强，相当于AfterReturningAdvice，方法正常退出时执行
		4.  @Before：标识一个前置增强方法，相当于BeforeAdvice的功能，相似功能的还有
		5.  @AfterThrowing：异常抛出增强，相当于ThrowsAdvice
		6.  @After: final增强，不管是抛出异常或者正常退出都会执行
	@Override:检测被该注解标注的方法是否是继承自父类(接口)的
	@Deprecated:该注解标注的内容,标识已过世
	@SuppressWarnings(传入警告类型):压制警告
		一般警告类型中直接填"all";
		一个注解里面只有一个属性 那么就是单值注解
		没有参数的注解,就是标记注解
		多个属性的,就是完整注解
	@Slf4j
		如果不想每次都写private  final Logger logger = LoggerFactory.getLogger(当前类名.class); 可以用注解@Slf4j;
		可以使用Slf4j中的 log.info等
	@Scheduled定时任务
		例如:@Scheduled(cron = "0 0 */2 * * ?")
		为每2小时执行1次
		配合cron表达式
	@ApiOperation
		@ApiOperation不是spring自带的注解是swagger里的
		com.wordnik.swagger.annotations.ApiOperation;
		@ApiOperation和@ApiParam为添加的API相关注解，个参数说明如下：
		@ApiOperation(value = “接口说明”, httpMethod = “接口请求方式”, response = “接口返回参数类型”, notes = “接口发布说明”；其他参数可参考源码；
		@ApiParam(required = “是否必须参数”, name = “参数名称”, value = “参数具体描述”
		@ApiResponse
			code - 响应的HTTP状态码
			message - 响应的信息内容
		@Api
			value - 字段说明
			description - 注释说明这个类
		@ApiModelProperty的用法   
			value–字段说明 
			name–重写属性名字 
			dataType–重写属性类型 
			required–是否必填 
			example–举例说明 
			hidden–隐藏


自定义Test测试注解
	1.创建一个注解,设定元注解Retention到运行时
	设置元注解Target指定注解位置
	2.创建Demo写入方法
	方法要求 没有返回值 public 且没有参数
	3.创建Demo
	Demo?.Class获取到指定Demo类
	clazz.getMthods();//获取到Demo中的所有方法
	for循环遍历
	使用method.isAnnotationPresent(注解类.class)判断
	如果有这个注解则返回true
	mthod.invoke(指定Demo类);
	如果有这个注解则运行它
	使用注解获取配置文件
	步骤:
	1.创建注解文件JDBCinfo
	并且使用元注解Retention设定到运行时
	写入4个String参数默认给值
	2.创建Demo类
	Demo.class获取当前类对象
	clazz,isAnnotationPresent(JDBCinfo.class)//判断是否注释当前类
	clazz.getAnnotation(JDBCinfo.class);//获取这些注释内容
	例如:String d = annotation.DriverClass();//获取到配置文件了


lamba表达式的 :: 类似.方法调用