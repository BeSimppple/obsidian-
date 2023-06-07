AOP面向切面编程(Aspect Oriented Programming)  
核心最终目的:解耦,提高扩展性和可用性
@aspect .开启AOP配置支持注解(作用是把当前类标识为一个切面供容器读取)
	将通知类IoC到Spring容器中
	<aop:aspectj‐autoproxy></aop:aspectj‐autoproxy>
	\<bean id="userDao" class="com.YL.dao.UserDao">\</bean>
简介:
	**是把我们程序重复的代码抽取出来封装，在需要执行的时候，使用动态代理的技术，在不修改源码的基础上，对我们的已有方法进行增强**。(和目标抽取出来,在引用进其他方法不同的是,不再在方法编程内产生强耦合. 可以使用aop在需要的时候调用引用的方法准确增强指定方法)
	底层就是通过Spring提供的动态代理技术实现的
	在目标方法执行时,生成代理对象,代理对象执行时进行增强功能的接入,再去调用目标对象的方法,完成增强
**AOP代理的两种方式**
	如果要代理的对象，实现了某个接口，那么Spring AOP会使用JDK Proxy，去创建代理对象。
	没有实现接口的对象，就无法使用JDK Proxy去进行代理了，这时候Spring AOP会使用 Cglib 生成一个被代理对象的子类来作为代理。
	1.JDK代理:基于接口的动态代理技术
		**特点:**
		1.基于接口的动态代理技术
		2.被代理类最少实现一个接口
		**原理:**
		利用反射来实现的，需要调用反射包下的Proxy类的newProxyInstance方法来返回代理对象，这个方法中有三个参数，分别是用于加载代理类的类加载器，被代理类实现的接口的class数组和一个用于增强方法的InvocaHandler实现类。
	2.cglib代理:基于父类的动态代理技术
		**特点:**
		1.基于父类的动态代理技术
		2.如果报 asmxxxx 异常，需要导入 asm.jar
		3.被代理类不能用 final 修饰的类
		**原理:**
		利用asm开源包来实现的，是把被代理类的class文件加载进来，通过修改它的字节码生成子类来处理
OOP与AOP的区别
	OOP开发(面向对象编程)，关注的是对象与对象之间的关系，对象方法和属性做切分
	AOP开发，关注的是过程中的业务,以业务做切分(将非主要业务封装)后通过动态代理切入
**AOP名词解释**
	**连接点(Joinpoint)：**
	类中的任意方法的运行时表示，可以简单理解为类中的方法
	**切入点(Pointcut):** 具有共性功能的方法的运行时表示，可以简单理解为具有共性功能的方法
	注意：切入点对应的是被挖去了共性功能后的方法执行时匹配断言（格式）
	**通知(Advice) :** 共性功能模块化，可以简单理解为将共性功能抽取出来制作成独立的方法
	**切面(Aspect) :** 切入点与通知的对应关系，可以简单理解为被抽取的共性功能与共性功能被抽取位置对应的方法之间的关系
	**目标对象(Target Object):** 包含切入点的运行时对象，开发阶段制作的是目标对象对应的类
	**织入(Weaving) ：** 是一个将通知功能加入原始字节码的动态过程，Spring使用运行时织入机制
AOP运行流程：
	运行时，Spring监控切面中所配置切入点对应方法的执行，发现执行了匹配的方法，使用AOP代理机制，创建代理对象（AOP代理），将原始方法（切入点）与通知进行融合，形成
	完整的业务逻辑并进行运行，此过程称为织入。
**切入点表达式**
	**作用:** 用于指定在哪些地方应该执行额外代码。它可以基于方法名称、方法参数、类名、注解等多种条件进行匹配。
	1.  **execute**
	2.  **within**
	3.  **this**
	4.  **target**
	5.  **args**
	6.  **@target**
	7.  **@within**
	8.  **@annotation**
	9.  @args
	**execute表达式:**
	execution( [权限修饰符] 返回值类型 包名.类名/接口名.方法名(参数))
	权限修饰符可以省略
	\*代表任意包名、任意类名、任意方法名、任意参数类型
	.代表任意一个包下
	..代表任意一个或多个包、任意一个或多个参数（类型任意）
	常用:* \*..UserServiceImpl.*(..)
	可以通过<aop:pointcut>标签进行切入点表达式的抽取
**通知类别**
	@Before在切入点前运行
	@After: 在切入点后运行，无论方法是否抛出异常
	@AfterReturning :在切入点后运行，只有方法正常结束才运行，抛出异常则不运行
	@AfterThrowing:在切入点中如果抛出异常运行,否则不运行
	@around: 在切入点前后运行，通过ProceedingJoinPoint对象调用procee()方法完成对
	around通知可以模拟其他通知获取切入点参数
Around通知详解:
	around使用ProceedingJoinPoint获取参数
	非around使用JoinPoint获取参数
	获取切入点返回值和异常对象
	概念:在通知方法中获取到切入点方法的返回值
	这种情况只存在程序运行后才能获取到返回值
	所以只有两种通知:around和after-returning
	-
	pjp是连接点: 原方法
	pjp.proceed() 执行原方法
	如果try catch则jvm处理器认为没有异常,被当前处理了
	throws则判定为有异常
	-
	around中获取到返回值
	Object msg =null
	msg=pjp.proceed()
	return msg;
	-
	afterReturning通知获取切入点方法返回值
	需要第一部在配置文件中添加属性returnning ="msg"
	public void afterReturning(String msg){
	System.out.println("after returning,切入点返回值:"+msg);
	}
	-
	afterThrowing通知获取切入点异常对象
	配置文件:<aop:after-throwing>标签中添加属性throwing="e"
	通知方法:添加一个形式参数e,变量名要和上面保持一致
切面执行顺序
	![[AOP(基于面动态代理)_image_1.png]]
	异常时:
	![[AOP(基于面动态代理)_image_2.png]]

sulth和sentinel就是使用了aop的面向切面的思想对编程没有入侵性.可以在运行前,运行时和运行后监控